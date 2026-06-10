# Code samples — Session 330

Extracted from the Code tab. Host-side snippets are Objective-C (Metal API); shader
snippets are Metal Shading Language (C++). Timestamps map to the transcript.

## ~3:53 — Creating a tensor with a quantized data type (host)

```objc
// Creating a tensor with a quantized data type from device

#define RANK 2

MTLTensorDescriptor *tensorDesc = [MTLTensorDescriptor new];

tensorDesc.dataType = MTLTensorDataTypeMetalFloat8E4M3;
tensorDesc.usage = MTLTensorUsageCompute;

NSInteger dimensions[RANK] = {NumCols, NumRows};
tensorDesc.dimensions = [[MTLTensorExtents alloc] initWithRank:RANK values:dimensions];

NSError *err = nil;
id <MTLTensor> tensor = [device newTensorWithDescriptor:tensorDesc error:&err];
```

## ~4:44 — Adding a scales auxiliary plane (host)

```objc
// Creating a tensor with a scales auxiliary plane from device

#define RANK 2

MTLTensorAuxiliaryPlaneDescriptor *planeDesc = [MTLTensorAuxiliaryPlaneDescriptor new];
planeDesc.dataType = MTLTensorDataTypeMetalFloat8UE8M0;

NSInteger blockFactors[RANK] = {32, 1};
planeDesc.blockFactors = [[MTLTensorExtents alloc] initWithRank:RANK values:blockFactors];

MTLTensorAuxiliaryPlaneDescriptorMap *auxiliaryPlanes =
    [MTLTensorAuxiliaryPlaneDescriptorMap new];
[auxiliaryPlanes setDescriptor:planeDesc forPlane:MTLTensorPlaneTypeScales];

MTLTensorDescriptor *tensorDesc = [MTLTensorDescriptor new];
tensorDesc.dataType = MTLTensorDataTypeMetalFloat8E4M3;
tensorDesc.usage = MTLTensorUsageCompute;

NSInteger dimensions[RANK] = {NumCols, NumRows};
tensorDesc.dimensions = [[MTLTensorExtents alloc] initWithRank:RANK values:dimensions];
tensorDesc.auxiliaryPlanes = auxiliaryPlanes;

NSError *err = nil;
id <MTLTensor> tensor = [device newTensorWithDescriptor:tensorDesc error:&err];
```

## ~6:07 — MXFP8 multi-plane tensor handle (shader, bound from host)

```cpp
// Type aliases for a MXFP8 multi-plane tensor handle

#include <metal_tensor>

using namespace metal;

using scales_plane = tensor_blockwise<tensor_plane_scales,
                                      device metal_fp8_ue8m0_format,
                                      32, 1>;

using mxfp8_tensor = tensor<device metal_fp8_e4m3_format,
                            dextents<int, 2>,
                            tensor_handle,
                            scales_plane>;

kernel void matmul(mxfp8_tensor matrixA [[buffer(0)]],
                   mxfp8_tensor matrixB [[buffer(1)]],
                   tensor<device half, dextents<int, 2>> matrixC [[buffer(2)]])
{
    // ...
}
```

## ~6:43 — MXFP8 multi-plane tensor inline (shader, built on the stack)

```cpp
// Type aliases for a MXFP8 multi-plane tensor inline

#include <metal_tensor>

using namespace metal;

using scales_plane = tensor_blockwise<tensor_plane_scales,
                                      device metal_fp8_ue8m0_format,
                                      32, 1>;

using mxfp8_tensor_inline = tensor<device metal_fp8_e4m3_format,
                                   dextents<int, 2>,
                                   tensor_inline,
                                   scales_plane>;

// Construct tensor on the stack from buffer pointers
mxfp8_tensor_inline matrixA(dataBufferA,
                             dextents<int, 2>(K, M),
                             array<int, 2>({ 1, K }),
                             scales_plane(scalesBufferA));
```

## ~7:12 — Slice tiles by threadgroup ID and run quantized matmul (shader)

```cpp
// Slice the tensors to extract the relevant tile
auto tA = matrixA.slice(0, tgid.y * TILEM);
auto tB = matrixB.slice(tgid.x * TILEN, 0);
auto tC = matrixC.slice(tgid.x * TILEN, tgid.y * TILEM);

// Set up the matmul descriptor
constexpr auto descriptor = matmul2d_descriptor(TILEM,                  // M
                                                TILEN,                  // N
                                                dynamic_length_v<int>,  // K
                                                false,   // Left matrix transposed
                                                false);  // Right matrix transposed

matmul2d<descriptor, execution_simdgroups<4>> op;

// Run the op — TensorOps handles dequantization automatically
op.run(tA, tB, tC);
```

## ~10:09 — FlashAttention: QxK matmul into a cooperative tensor (shader)

```cpp
// Setup QxK matrix multiplication op
constexpr auto mul_qk_op_desc = matmul2d_descriptor(/* ... */);
matmul2d<mul_qk_op_desc, execution_simdgroups> mul_qk_op;

// Slice Q, K, V
auto tQSlice = tQ.slice<D, ROWS_PER_SIMD>(0, sgid * ROWS_PER_SIMD);
auto tKSlice = tK.slice<D, BK>(0, k);
auto tVSlice = tV.slice<D, BK>(0, k);

// Create cooperative tensor to store tile of QxK
auto ctQK = mul_qk_op.get_destination_cooperative_tensor<decltype(tQSlice),
                                                         decltype(tKSlice),
                                                         float>();

// Multiply QxK
mul_qk_op.run(tQSlice, tKSlice, ctQK);
```

## ~11:18 — Row-max reduction over the QxK tile (shader)

```cpp
// Create a cooperative tensor to store row reduction output
auto ctTileRowMax = mul_qk_op.get_row_reduction_destination_cooperative_tensor<
                        decltype(tQSlice),
                        decltype(tKSlice),
                        float>();

// Compute max over each row of QxK tile
reduce_rows(ctQK, ctTileRowMax, reduction_operation::max, -INFINITY);
```

## ~11:55 — SoftMax via map_iterator over the cooperative tensor (shader)

```cpp
// Iterate over elements of QxK tile
#pragma clang loop unroll(full)
for (auto it = ctQK.begin(); it != ctQK.end(); it++) {
    // Fetch row max corresponding to this element
    auto row_it = ctRowMax.map_iterator(it);

    // Subtract row max from each element and compute exponent
    *it = exp(*it - *row_it);
}
```

## ~12:33 — Reuse the cooperative tensor directly as a matmul input (shader)

```cpp
constexpr auto mul_sv_op_desc = matmul2d_descriptor(/* ... */);
matmul2d<mul_sv_op_desc, metal::execution_simdgroup> mul_sv_op;

if (mul_sv_op.is_compatible_as_left_input<float, half, float>(ctQK)) {
    // Directly reuse cooperative tensor as input
    auto ctQKIn = mul_sv_op.get_left_input_cooperative_tensor<float, half, float>(ctQK);
    mul_sv_op.run(ctQKIn, tVSlice, ctO);
} else {
    // Store and reload through threadgroup memory if layout is not compatible
    ctQK.store(tgTensor);
    simdgroup_barrier(mem_flags::mem_threadgroup);

    auto ctQKIn = mul_sv_op.get_left_input_cooperative_tensor<float, half, float>();
    ctQKIn.load(tgTensor);
    mul_sv_op.run(ctQKIn, tVSlice, ctO);
}
```

---

## Useful API facts surfaced by the code

- **Quantized `MTLTensorDataType`s** named: `MTLTensorDataTypeMetalFloat8E4M3` (data),
  `MTLTensorDataTypeMetalFloat8UE8M0` (scales / E8M0 block-wise format).
- **Scales plane** is configured via `MTLTensorAuxiliaryPlaneDescriptor` (set
  `dataType` + `blockFactors`, e.g. `{32, 1}` = one scale per 32 data elements) and
  attached through `MTLTensorAuxiliaryPlaneDescriptorMap` with
  `MTLTensorPlaneTypeScales`, then set on `tensorDesc.auxiliaryPlanes`.
- **Shader tensor types** (`<metal_tensor>`): `tensor_blockwise<tensor_plane_scales, ...>`
  for the scales plane; `tensor<...>` parameterized by storage format
  (`metal_fp8_e4m3_format`), extents (`dextents<int, 2>`), a tag
  (`tensor_handle` for host-allocated, `tensor_inline` for stack-constructed), and the
  scales plane type.
- **matmul**: `matmul2d_descriptor(M, N, K, leftTransposed, rightTransposed)` with
  `dynamic_length_v<int>` for dynamic dims; `matmul2d<descriptor, execution_simdgroups<N>>`
  selects the op + SIMD-group count; `op.run(A, B, C)`. `execution_simdgroup` (singular)
  scope gives each SIMD group its own independent matmul (used in FlashAttention).
- **Cooperative tensors**: `get_destination_cooperative_tensor<...>()`,
  `get_row_reduction_destination_cooperative_tensor<...>()`,
  `reduce_rows(src, dst, reduction_operation::max, -INFINITY)`, `map_iterator(it)` to map
  between tensors of different shapes, and `begin()/end()` iteration.
- **Direct cooperative-tensor reuse (macOS/iOS 27)**:
  `is_compatible_as_left_input<...>(ct)` (and right variant) gates
  `get_left_input_cooperative_tensor<...>(ct)`; fallback is `ct.store(tg)` +
  `simdgroup_barrier` + `ctIn.load(tg)`. The `op.run(...)` call is identical either way.
- **Alignment**: the new small quantized types have additional alignment requirements vs
  larger types — check the Metal documentation.
