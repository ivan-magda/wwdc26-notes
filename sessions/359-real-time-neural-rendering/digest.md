---
title: "Build real-time neural rendering pipelines with Metal — Full Digest"
session: WWDC26 · 359
url: https://developer.apple.com/videos/play/wwdc2026/359/
duration: 22m
speakers: Yulia (GPU Software Engineer, Apple)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 359 — Build real-time neural rendering pipelines with Metal

## TL;DR

Machine learning is moving from research into production in real-time rendering, and
Metal 4 gives you **three levels of integration** — each one trading off control for
flexibility:

1. **MetalFX Denoising** — a ready-to-use, black-box neural denoiser + upscaler. Feed a
   noisy one-sample-per-pixel path-traced frame plus a few auxiliary inputs, get back
   near-final quality at interactive frame rates. Demoed in Maxon's Redshift Live.
2. **Metal 4 ML command encoder** — deploy your own pre-trained model (e.g. a neural
   tone mapper) inside your command buffer, alongside compute and render work, with no
   context switch. Collapses a whole multi-stage post-processing chain into one neural
   evaluation.
3. **TensorOps API** — build *tiny* networks inline in your shaders, running on the
   neural accelerator in M5 / A19 Pro GPUs. Small enough to train **online**, every few
   frames, adapting to dynamic scene conditions.

The throughline: pick the level that matches how much control you need. Apple's
recommended starting point is MetalFX Denoising for any app with real-time viewports.

---

## 1. The toolset (the three levels)

On Apple platforms you get a full ML toolset for rendering:

- **MetalFX** — highest level, fully integrated black box: neural denoising + upscaling.
- **Metal 4 ML command encoder** — run pre-trained models directly in your command
  buffer; more control over integration and scheduling.
- **TensorOps API** — most flexible: design and run custom models directly in shaders,
  fully leveraging the **neural accelerator introduced in the M5 and A19 Pro Apple
  silicon GPUs**.

Many rendering techniques that traditionally used analytical methods can now be ML-based
— neural denoising, neural textures, learned tone mapping, and more — improving quality,
performance, or memory footprint at every pipeline stage.

## 2. MetalFX Denoising

In a path tracer, the frame budget might only allow one (or a few) samples per pixel to
stay interactive — and one sample is naturally noisy. MetalFX Denoising is designed for
the low-latency demands of a live viewport: a **combined neural upscaler and denoiser**,
platform-integrated and optimized for Apple silicon.

**Integration flow:**

1. Generate auxiliary inputs — **diffuse albedo, depth, motion vectors**, and a few
   others (your renderer may already produce these).
2. Feed them to MetalFX → it produces a denoised image.
3. Complete your pipeline with post-processing and display.

**The Redshift Live demo (Maxon):** a modern real-time path tracer in Cinema 4D on Apple
silicon. With one sample per pixel you see noise during camera movement; enable the
MetalFX denoiser and the image becomes dramatically stable and noise-free — clean,
near-final quality at interactive frame rates with real-time ray-traced lighting,
shadows, and global illumination. Artists can watch lighting update live (e.g. moving a
tree). MetalFX uses both **spatial and temporal** techniques to transform the noisy
1-spp frame.

> Deep-dive on inputs and integration: **"Go further with Metal 4 games."**

### Best practice 1 — keep your inputs clean

Denoiser output quality depends directly on input quality. Auxiliary inputs are normally
noise-free; keep them that way. **Diffuse albedo is the strongest signal** — make it as
close as possible to a noise-free version of the final on-screen result.

Tooling tip: build **debug views** for each input in your engine, and use a **GPU
capture** to inspect textures frame-by-frame so you can validate that inputs look the way
the model expects.

Two tools for parts of the scene you don't want denoised (or that are already clean):

- **Transparency overlay** — for effects with no meaningful surface that are already
  noise-free (particles, fog, volumetrics, sky). The overlay input is **only upscaled
  and composited** into the final result, not denoised.
- **Denoiser strength mask** — per-pixel control from **0 (no denoising) to 1 (max
  strength)**. Example: mark the sky as not-to-be-denoised.

### Best practice 2 — store what the viewer sees (primary surface replacement)

Reflective and transmissive materials are tricky because the inputs (especially diffuse
albedo) should represent the **final desired output**, not the surface's own properties.

- **Mirror:** has no color of its own — the viewer sees the reflected surface. Store the
  **reflected** geometry's properties (albedo, normal, roughness) in the mirror-like
  object.
- **Glass:** the viewer sees a blend of reflected + transmitted (transmitted can be
  noisy). Blend geometry properties like diffuse albedo by the **Fresnel term** (how much
  light is reflected vs refracted at the intersection), substantially reducing input
  noise.

This is the well-known **primary surface replacement** technique — the primary surface
albedo is replaced by the combined reflected + refracted albedo. Done right, reflections
stay beautiful and sharp.

### Best practice 3 — get your motion vectors right

Correct motion vectors are essential for temporal stability. A motion vector is a
**per-pixel screen-space displacement** from the current frame to the previous frame —
it answers "where was this pixel in the previous frame?" The model uses them to
understand the scene under motion and over time; getting them right is the difference
between a blurry and a sharp output under motion.

**MetalFX expects dejittered motion vectors** — without the sub-pixel jitter shifts.
Otherwise the vectors can be up to one pixel wrong, causing edge shimmering.

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

- This is the **camera-only path** for static objects: project `worldPos` through both
  frames' matrices, take the NDC difference, then subtract the jitter deltas for a clean
  unjittered vector.
- **Moving / deforming geometry:** the camera-only path won't see displacement — store
  each vertex's previous-frame world position (or skin twice) and compute the actual
  motion vector.
- **Genuinely unreliable motion** (e.g. fast alpha-blended particles): use the
  **reactive mask** instead. (More in "Go further with Metal 4 games.")

Redshift Live ships every one of these best practices.

## 3. Deploy custom ML networks with Metal 4 (neural tone mapper)

Beyond denoising, more pipeline stages are becoming ML-based — and Metal 4 gives you two
ways to bring your own technique in:

- **ML command encoder** — deploy a trained model right in your command buffer, in the
  same pipeline, with no context switch.
- **TensorOps API** — build a small hardware-accelerated network directly in a shader.

> Deep-dive on both APIs: **"Combine Metal 4 machine learning and graphics."**

**The tone-mapping example.** Renderers have extended post-processing chains to map HDR
to a displayable image matching artistic intent — tone mapping, color grade, film
emulation — each stage with its own parameters and concatenated outputs, growing
arbitrarily complex. The best results come from understanding image content, which is
exactly what a network can learn. So: **take the whole color pipeline (or part of it) and
replace it with a single neural network** that learns the color transformation.

**HDRNet** (Gharbi et al., 2017) is one such architecture:
- Works on a small **downsampled** version of the image.
- Performs **global and local** analysis (scene-level + small details).
- Produces color transformations for **16×16 tiles**, applied with smart **edge-aware**
  techniques for the final tone-mapped result.

**Workflow:**

1. Develop and train the network in your framework of choice (e.g. **PyTorch**).
   Training data: manually tone-mapped previous projects, or many tone-mapped images
   from your renderer.
2. Export the trained model to an **`MTLPackage`**.
3. **Setup** in Metal 4 (similar to loading a regular pipeline): load the `MTLPackage`,
   specify the network function with a function descriptor, create a machine-learning
   pipeline descriptor.
4. **Dispatch:** create an encoder, create an argument table with inputs/outputs,
   dispatch the command buffer. Now compute, ML, and rendering work run together.

**Updated pipeline:** path tracer → MetalFX denoising → neural tone mapper, **all in the
same command buffer, same frame**. The ML encoder replaced the entire multi-stage
post-processing chain with one neural evaluation.

## 4. Inline neural networks with TensorOps (online training)

The opposite of the big offline-trained network: **tiny networks for one specific task** —
a few thousand parameters or less, trained on your own scene data, sometimes trained
**online every few frames**. The network only ever sees one scenario, so it doesn't need
to generalize. Small enough to fit **inline in your shader**, among your ALU and
texture-sampling instructions. The enabler is **TensorOps**, available in any rendering
pipeline stage.

### The sky-visibility probe example

A skybox used for image-based lighting casts soft illumination on scene geometry — the
**average light from all visible directions** at a point. Normally precomputed offline
and sampled at runtime. But scenes are rarely static (e.g. a dynamic day-night cycle), so
the offline-learned signal can fall out of sync. This is a learnable function, so use
**online training**.

**Standard loop** (offline-trained): update world → dispatch ML encoder for inference →
produce lighting info → shade.

**Online-training loop** (per frame):

1. Generate a direction to sample.
2. Run inference on the model to get the result.
3. Compute the **analytical solution** to the sky-illumination problem and use it to
   compute the **error**.
4. Run a **back-propagation** pass to progressively improve the model.

This is the same flow as offline training, just repeated across frames — so the inference
pass can run **inline in your shading pass**, and the model adapts to new world conditions
every frame, usable for shading right away. Not possible with the standard offline
workflow. The concept generalizes to any technique that can learn a signal.

### Anatomy of the MLP

A network has three building blocks: the **input layer** (input features), the **output
layer** (final predictions), and the **hidden layers** (where learning happens).

The sky probe is a **fully connected multilayer perceptron (MLP)** with a **3-4-4-3**
shape:
- Input: **3 floats** encoding a direction.
- Two hidden layers of **4 neurons** each.
- Output: **3 floats** = the average illumination color from that direction.

You can experiment with input size and the number/size of layers per application.

**Tensors.** Batch multiple inputs into a **2D matrix** (rows = batch of input
directions; inputs can also carry positional or material data). The output tensor is
likewise a 2D matrix (batch of colors).

### Evaluating the forward pass in a shader

1. Take the input tensor and the first hidden layer's weights tensor.
2. Multiply them with a **`matmul` 2D tensor operation** → a pre-activation result.
3. Store the result, then apply the **activation function** in place.
4. Repeat matmul + activation for each subsequent layer up to the output layer.
5. Store the output tensor and use it in your compute shader immediately or later.

**Execution scope — the key performance lever:**

- **Thread execution scope:** a single thread executes the whole tensor op. Good for
  divergent work or pipeline stages where you don't control the thread group.
- **SIMD-group execution scope** (in a compute stage, when you have full control): all
  participating threads work on the same matrix multiplication. This unlocks
  **cooperative tensors** — storage distributed across the thread-group's threads,
  **avoiding an expensive round trip to main memory**. Using a cooperative tensor as the
  matmul output keeps the result in **fast thread storage**, where the activation is
  applied in place.

Result: the small network learned the sky signal efficiently — the neural render matches
the ray-traced ground truth. The same building blocks build the **back-propagation pass**
for online training.

> Full code: **Metal Performance Primitives (MPP) Programming Guide.**

## 5. Next steps

Three levels of ML in the rendering pipeline, each giving more control:

- **MetalFX** — platform-integrated neural denoising. Three best practices: keep inputs
  clean, store what the viewer sees, get motion vectors right.
- **MTLPackage + ML command encoder** — export offline-trained models, deploy at
  runtime; replace an entire post-processing pipeline with one neural evaluation.
- **TensorOps API** — build tiny networks directly in shaders, on the neural accelerator.

Where to start: download Xcode and explore the Metal 4 sample code. If your app has
real-time requirements (pro-app viewports, games), **adopt MetalFX Denoising and
Upscaling first**. Then try training a neural tone mapper for your own post-processing
chain, and experiment with small specialized networks via the tensor API.
