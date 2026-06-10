---
title: "Optimize custom machine learning operations with Metal tensors — Full Digest"
session: WWDC26 · 330
url: https://developer.apple.com/videos/play/wwdc2026/330/
duration: 16m
speakers: Shiyao (GPU Software Engineer)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 330 — Optimize custom machine learning operations with Metal tensors

## TL;DR

This is the deep-dive companion to the intro TensorOps / M5 sessions. If you write
custom ML kernels in Metal Shading Language — whether for a Core AI custom op, a
contribution to MLX or llama.cpp, or a Metal app — TensorOps gives you matmul,
convolution, reductions, and FlashAttention building blocks that automatically use
hardware acceleration across every Apple Silicon GPU generation, including the new
**M5 neural accelerator** (a per-shader-core block tuned for dense compute-bound work
like LLM prefill).

Four headline themes:

1. **Native quantized data types.** TensorOps now understands low-precision weights
   directly. 4-/8-bit integers landed in macOS/iOS **26**; **27** adds 4-/8-bit
   floating point and 2-bit integers. You set a quantized `dataType` on the tensor
   descriptor and feed it straight in — TensorOps dequantizes for you and uses
   hardware acceleration.
2. **Multi-plane tensors.** A single `MTLTensor` can now carry both the quantized
   element data and its **scale factors** as a separate plane (FP8 **E8M0** block-wise
   format). One object, slices stay in sync.
3. **FlashAttention with cooperative tensors.** The fused attention pattern (QxK →
   SoftMax → ·V) is buildable with `execution_simdgroup` scoping, `reduce_rows`,
   `map_iterator`, and — new in macOS/iOS **27** — passing a **cooperative tensor
   directly as a matmul input**, eliminating the threadgroup-memory round-trip that
   macOS 26 forced.
4. **Plugs into Core AI.** A custom Metal kernel registers as a `TorchMetalKernel`
   string, swaps in for HuggingFace attention, and exports PyTorch → Core AI asset.
   Demoed end-to-end on a **Sam3** image-segmentation model.

---

## 1. Where TensorOps sits in the ML stack (and the M5 neural accelerator)

Apple's ML stack is layered: **Core AI** and **MLX** at the top (deploy a model with
minimal code), **Metal Performance Shaders** below that (ready-made high-performance
kernels), and at the bottom **Metal Performance Primitives + the TensorOps library**,
which everything else builds on.

Why drop to the Metal level?

- ML research moves fast — you want a **custom op** that plugs into a higher-level
  framework like Core AI.
- You're **contributing to an ML framework** (MLX, llama.cpp).
- You're writing a **Metal-based application**.

**TensorOps** is a Metal Shading Language API that accelerates tensor operations on the
GPU (matmul, convolution, …). It auto-selects whatever hardware acceleration is
available on each Apple Silicon GPU generation, so you don't branch on hardware. In
particular it exploits the **M5 neural accelerator** — a new block located *directly in
each shader core*, sitting alongside the other GPU pipelines, designed for dense
compute-bound work such as the **prefill stage of an LLM**.

The talk assumes the basics from the intro TensorOps / M5 machine-learning session and
builds upward: quantized data first, then FlashAttention.

## 2. Working with quantized data

State-of-the-art models keep getting larger, and **inference is typically memory-
bandwidth bound**. Compressing weights both fits bigger models in memory and saves
bandwidth. The standard tool is **quantization**: take higher-precision weights (e.g.
16-bit half) and reduce them to a lower-precision type (e.g. **4-bit**). Quantized
weights pair with **scale factors** that map the values back into the original range at
compute time.

TensorOps now natively supports quantized data types in addition to 16-/32-bit float:

| Added in | Types |
|---|---|
| macOS/iOS **26** | 4-bit and 8-bit **integer** |
| macOS/iOS **27** | 4-bit and 8-bit **floating point**, 2-bit **integer** |

You create a quantized tensor just like a regular one — fill the descriptor, set a
quantized `dataType`, then `newTensorWithDescriptor:` on the device:

```objc
MTLTensorDescriptor *tensorDesc = [MTLTensorDescriptor new];
tensorDesc.dataType = MTLTensorDataTypeMetalFloat8E4M3;
tensorDesc.usage = MTLTensorUsageCompute;

NSInteger dimensions[RANK] = {NumCols, NumRows};
tensorDesc.dimensions = [[MTLTensorExtents alloc] initWithRank:RANK values:dimensions];

id <MTLTensor> tensor = [device newTensorWithDescriptor:tensorDesc error:&err];
```

### Multi-plane tensors — scales live inside the tensor

In macOS/iOS **27**, a single `MTLTensor` can represent the scales alongside the
quantized data as an **additional scale plane**. The plane supports the popular **FP8
E8M0 block-wise** scale format, where each scale element applies to a *block* of data
elements. You declare a `MTLTensorAuxiliaryPlaneDescriptor` (data type +
`blockFactors`), wrap it in a `MTLTensorAuxiliaryPlaneDescriptorMap` tagged
`MTLTensorPlaneTypeScales`, and attach it to the original descriptor — data, scales,
and metadata then pack into one tensor object:

```objc
MTLTensorAuxiliaryPlaneDescriptor *planeDesc = [MTLTensorAuxiliaryPlaneDescriptor new];
planeDesc.dataType = MTLTensorDataTypeMetalFloat8UE8M0;

NSInteger blockFactors[RANK] = {32, 1};   // one scale per 32 data elements
planeDesc.blockFactors = [[MTLTensorExtents alloc] initWithRank:RANK values:blockFactors];

MTLTensorAuxiliaryPlaneDescriptorMap *auxiliaryPlanes =
    [MTLTensorAuxiliaryPlaneDescriptorMap new];
[auxiliaryPlanes setDescriptor:planeDesc forPlane:MTLTensorPlaneTypeScales];

tensorDesc.auxiliaryPlanes = auxiliaryPlanes;
```

## 3. Quantized matrix multiplication

Matmul is the core ML operation — LLMs run millions of them per inference. The basic
high-performance recipe (from the M5 session) is to **tile** the inputs and run
tile-wise matmuls with TensorOps, maximizing parallelism and cache residency.
Quantization on top of that further cuts memory traffic.

In the shader, declare type aliases up front. A `scales_plane` with `fp8_e8m0_` data
and a **32×1** block size (every 32 data elements share one scale), then the full
tensor type combining an FP8 data format with that scales plane:

```cpp
using scales_plane = tensor_blockwise<tensor_plane_scales,
                                      device metal_fp8_ue8m0_format, 32, 1>;

using mxfp8_tensor = tensor<device metal_fp8_e4m3_format, dextents<int, 2>,
                            tensor_handle, scales_plane>;

kernel void matmul(mxfp8_tensor matrixA [[buffer(0)]],
                   mxfp8_tensor matrixB [[buffer(1)]],
                   tensor<device half, dextents<int, 2>> matrixC [[buffer(2)]]) { /* ... */ }
```

**Handle vs inline.** If you allocated a full `MTLTensor` on the host, bind it with the
`tensor_handle` tag. If you'd rather not create a host tensor, swap `tensor_handle` for
`tensor_inline` and construct it on the shader's stack from buffer pointers + metadata:

```cpp
using mxfp8_tensor_inline = tensor<device metal_fp8_e4m3_format, dextents<int, 2>,
                                   tensor_inline, scales_plane>;

mxfp8_tensor_inline matrixA(dataBufferA, dextents<int, 2>(K, M),
                            array<int, 2>({ 1, K }), scales_plane(scalesBufferA));
```

**Slice + run.** Divide the work over threadgroups, slice each tile by threadgroup ID
(the data *and* scales planes slice together according to the block size), then set up
and run the matmul exactly as you would for non-quantized tensors — TensorOps handles
dequantization automatically:

```cpp
auto tA = matrixA.slice(0, tgid.y * TILEM);
auto tB = matrixB.slice(tgid.x * TILEN, 0);
auto tC = matrixC.slice(tgid.x * TILEN, tgid.y * TILEM);

constexpr auto descriptor = matmul2d_descriptor(TILEM, TILEN, dynamic_length_v<int>,
                                                false, false);
matmul2d<descriptor, execution_simdgroups<4>> op;
op.run(tA, tB, tC);   // dequantization handled for you
```

### Custom dequantization formats

Feed quantized data straight into TensorOps whenever you can, so it uses hardware
acceleration. If you need a **custom dequant format**, two paths:

- **Simple but slower:** each thread loads a chunk from device memory, dequantizes to
  f16 in **threadgroup memory**, then passes it as an inline threadgroup tensor. Costs
  extra loads/stores.
- **Better:** dequantize into a **cooperative tensor** (storage spread across the
  thread-private registers of the participating threads) and pass that as a matmul
  input — skipping the threadgroup-memory round-trip.

> Heads-up: the new small quantized types carry **additional alignment requirements**
> vs larger types. Check the Metal docs.

## 4. Building FlashAttention with TensorOps

Attention is the core of every transformer: multiply **Q·K**, run **SoftMax** (row
reductions over the intermediate), then multiply by **V**. **FlashAttention** fuses all
three into one kernel. Building it with TensorOps uses several features in concert.

**1 — Custom SIMD-group mapping.** Use the `execution_simdgroup` scope so each SIMD
group owns *complete rows* of the intermediate matrix. That lets each group compute
SoftMax without exchanging data across groups; each runs an independent matmul in
parallel, sliced by SIMD-group ID. The QxK result goes into a **cooperative tensor**
so it can feed the next step without touching memory:

```cpp
constexpr auto mul_qk_op_desc = matmul2d_descriptor(/* ... */);
matmul2d<mul_qk_op_desc, execution_simdgroups> mul_qk_op;

auto tQSlice = tQ.slice<D, ROWS_PER_SIMD>(0, sgid * ROWS_PER_SIMD);
auto tKSlice = tK.slice<D, BK>(0, k);
auto tVSlice = tV.slice<D, BK>(0, k);

auto ctQK = mul_qk_op.get_destination_cooperative_tensor<decltype(tQSlice),
                                                         decltype(tKSlice), float>();
mul_qk_op.run(tQSlice, tKSlice, ctQK);
```

**2 — Row reductions for SoftMax.** `reduce_rows` computes the per-row max (threads
exchange data among themselves), returning another cooperative tensor of a different
shape:

```cpp
auto ctTileRowMax = mul_qk_op.get_row_reduction_destination_cooperative_tensor<
                        decltype(tQSlice), decltype(tKSlice), float>();
reduce_rows(ctQK, ctTileRowMax, reduction_operation::max, -INFINITY);
```

**3 — map_iterator bridges the two shapes.** Because the QxK tensor and the row-max
tensor differ in shape, `map_iterator` turns an iterator over the 2D tensor into the
iterator for the matching row-max element. Loop, map, then compute the SoftMax exponent
in place:

```cpp
#pragma clang loop unroll(full)
for (auto it = ctQK.begin(); it != ctQK.end(); it++) {
    auto row_it = ctRowMax.map_iterator(it);
    *it = exp(*it - *row_it);
}
```

**4 — Multiply by V, reusing the cooperative tensor directly.** This is the headline
27 improvement. In macOS 26 you had to store the SoftMax result to threadgroup memory
before the second matmul. Now you can pass a cooperative tensor **straight in** via
`get_left_input_cooperative_tensor`. Not every cooperative tensor is reusable (layout
depends on data types and other factors), so gate it with `is_compatible_as_left_input`
/ `..._right_input`; if incompatible, fall back to the store/reload path. Either way
`op.run(...)` is identical:

```cpp
matmul2d<mul_sv_op_desc, metal::execution_simdgroup> mul_sv_op;

if (mul_sv_op.is_compatible_as_left_input<float, half, float>(ctQK)) {
    auto ctQKIn = mul_sv_op.get_left_input_cooperative_tensor<float, half, float>(ctQK);
    mul_sv_op.run(ctQKIn, tVSlice, ctO);
} else {
    ctQK.store(tgTensor);                         // round-trip fallback
    simdgroup_barrier(mem_flags::mem_threadgroup);
    auto ctQKIn = mul_sv_op.get_left_input_cooperative_tensor<float, half, float>();
    ctQKIn.load(tgTensor);
    mul_sv_op.run(ctQKIn, tVSlice, ctO);
}
```

## 5. Integrating a custom op into Core AI

Core AI's Python tools convert PyTorch models to Core AI models, **including custom
Metal kernels**. The mechanics live in *"Deep Dive into Core AI Model authoring and
Optimization"* — Shiyao follows those steps to drop the custom FlashAttention kernel
into a **Sam3** image-segmentation model:

1. Define the kernel body as a **string in Python** and register a `TorchMetalKernel`.
2. Replace the default **HuggingFace attention** implementation with one that calls the
   kernel.
3. Load the model from HuggingFace and **export from PyTorch as an optimized Core AI
   asset**.

**Demo:** Sam3 does promptable concept segmentation — give it an image plus text, get a
mask. Prompted to "label all pixels containing a car," it segments the car (highlighted
blue), confirming the custom attention kernel is fully integrated.

## Next steps / resources named

- **Metal Performance Primitives documentation** — full API reference.
- **Programming guide** — performance-optimization guidelines.
- **TensorOps sample code** — download for details not covered in the talk.
- Related **Core AI** and **Metal** sessions.
