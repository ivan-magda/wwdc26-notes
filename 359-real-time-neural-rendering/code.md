# Code samples — Session 359

One snippet extracted from the Code tab. Timestamp maps to the transcript (~08:46).

## 8:46 — Dejittered camera-only motion vectors (Metal shader)

```cpp
#include <metal_stdlib>
using namespace metal;

// Compute camera-only motion vectors
float4 clipCurrent = viewProjCurrent * float4(worldPos, 1.0);
float2 ndcCurrent = clipCurrent.xy / clipCurrent.w;

float4 clipPrevious = viewProjPrevious * float4(worldPos, 1.0);
float2 ndcPrevious = clipPrevious.xy / clipPrevious.w;

float2 motion = ndcPrevious - ndcCurrent;

// Get subpixel offset for current and previous frames
float2 jitterCurrent = getJitter(frameIndex);
float2 jitterPrevious = getJitter(frameIndexPrevious);
motion -= jitterPrevious - jitterCurrent;
```

- Projects `worldPos` through the current and previous frame's view-projection
  matrices, takes the screen-space (NDC) difference, then **subtracts the jitter
  deltas** so MetalFX receives clean, dejittered motion vectors (no sub-pixel shift).
- This is the static-object / camera-only path. For moving or deforming geometry,
  store each vertex's previous-frame world position (or skin twice) and compute the
  actual displacement. For genuinely unreliable motion (e.g. alpha-blended particles),
  use the reactive mask instead.

---

## Spoken APIs (named in the transcript, no code shown)

- **MetalFX Denoising** — combined neural denoiser + upscaler. Auxiliary inputs:
  diffuse albedo, depth, motion vectors. Plus: transparency overlay (upscale-only),
  denoiser strength mask (0–1), reactive mask.
- **Metal 4 ML command encoder** — run a pre-trained model in your command buffer.
  Setup: load an `MTLPackage`, specify the network function via a function descriptor,
  create a machine learning pipeline descriptor. Execution: create the encoder, build
  an argument table (inputs + outputs), dispatch the command buffer.
- **TensorOps API** — build small networks inline in any shader stage; `matmul` 2D
  tensor operation; thread vs **SIMD-group execution scope**; **cooperative tensors**
  (storage distributed across thread-group threads, kept in fast thread storage).
- **MTLPackage** — export format for offline-trained models (e.g. from PyTorch).
- Reference: **Metal Performance Primitives (MPP) Programming Guide** for full
  TensorOps / back-propagation code.
