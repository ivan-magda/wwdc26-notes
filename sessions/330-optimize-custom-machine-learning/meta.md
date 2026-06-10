# Session 330 — Optimize custom machine learning operations with Metal tensors

- **URL:** https://developer.apple.com/videos/play/wwdc2026/330/
- **Duration:** 16m
- **Speakers:** Shiyao (GPU Software Engineer)

## Description

Explore Metal tensors and the TensorOps Metal Shading Language library, and learn how
to write optimized custom ML kernels on Apple Silicon. The session covers where
TensorOps sits in Apple's ML software stack (and why you might drop down to the Metal
level), best practices for working with quantized data — including the new quantized
data types and MX scaling formats with multi-plane `MTLTensor` objects — and how to
build advanced fused operations such as FlashAttention using cooperative tensors and
row reductions. It closes by integrating a custom Metal kernel into a Core AI model.

## Key topics

- **Where TensorOps fits** — the ML stack runs from Core AI / MLX (high-level) down
  through Metal Performance Shaders to Metal Performance Primitives + the TensorOps
  library; reasons to work at the Metal level (custom ops, contributing to MLX /
  llama.cpp, Metal-based apps).
- **M5 neural accelerator** — new hardware block in each shader core of the M5 family;
  accelerates dense compute-bound work like LLM prefill. TensorOps uses it
  automatically across all Apple Silicon GPU generations.
- **Quantized data types** — 4- and 8-bit integers added in macOS/iOS 26; 4- and 8-bit
  floating point and 2-bit integer types added in macOS/iOS 27. Create by setting a
  quantized `dataType` on `MTLTensorDescriptor`.
- **Multi-plane tensors / scale factors** — a single `MTLTensor` can carry quantized
  element data plus a scales plane (FP8 E8M0 block-wise format) via
  `MTLTensorAuxiliaryPlaneDescriptor` + `MTLTensorAuxiliaryPlaneDescriptorMap`.
- **Quantized matmul** — `tensor_handle` vs `tensor_inline` tensor types in the shader;
  slicing data + scales planes together by threadgroup ID; `matmul2d_descriptor` /
  `matmul2d` op; automatic dequantization; custom dequant into cooperative tensors to
  skip the threadgroup-memory round trip.
- **FlashAttention with TensorOps** — `execution_simdgroup` scope so each SIMD group
  owns full rows; cooperative tensors for the QxK intermediate; `reduce_rows` +
  `map_iterator` for SoftMax row max; passing a cooperative tensor directly as a matmul
  input (`get_left_input_cooperative_tensor`, `is_compatible_as_left_input`) — new in
  macOS/iOS 27, eliminates the threadgroup-memory round-trip required in macOS 26.
- **Core AI integration** — `TorchMetalKernel` registered from a Python string, swapped
  in for HuggingFace attention; export PyTorch → Core AI asset; demo on a Sam3 image
  segmentation model.

## Related sessions to fetch (referenced in this talk)

- [ ] Get started with TensorOps / M5 machine learning talk (basics of writing a
      matmul kernel with TensorOps — named but title not given verbatim)
- [ ] Deep Dive into Core AI Model authoring and Optimization
- [ ] Related Core AI sessions
- [ ] Related Metal sessions

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of how Metal tensors and TensorOps enable optimized
  custom ML kernels on Apple Silicon.
- **0:21 Apple's ML software stack** — Tour from Core AI / MLX down to Metal Performance
  Shaders, Metal Performance Primitives, and TensorOps; why work at the Metal level;
  the M5 neural accelerator.
- **2:25 Managing quantized data** — How quantization reduces memory bandwidth for large
  models; new quantized data types natively supported by TensorOps, including MX
  scaling formats.
- **4:23 Multi-plane tensors** — A single `MTLTensor` representing quantized element data
  and scale factors as separate planes; configuring multi-plane tensor descriptors.
- **5:17 Quantized matrix multiplication** — Extending a tiled matmul kernel to support
  quantized inputs: binding scales planes, inline tensors, slicing with threadgroup IDs,
  handling custom dequantization formats.
- **9:31 Building advanced ops** — Implementing FlashAttention with TensorOps: custom
  SIMD group mappings, cooperative tensors, row reductions, SoftMax, and the new API for
  passing cooperative tensors directly as matmul inputs (no threadgroup-memory round-trip).
- **13:35 Integrating custom ops into Core AI** — Integrating a custom Metal TensorOps
  kernel into a Core AI app using Core AI's Python tools to convert PyTorch models.
- **15:25 Next steps** — Summary of TensorOps features (quantized types, multi-plane
  tensors, FlashAttention, Core AI integration); pointers to sample code and related
  sessions.

## Code

See `code.md` — 10 snippets extracted from the Code tab.
