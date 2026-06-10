# Session 359 — Build real-time neural rendering pipelines with Metal

- **URL:** https://developer.apple.com/videos/play/wwdc2026/359/
- **Duration:** 22m
- **Speakers:** Yulia (GPU Software Engineer, Apple)

## Description

How machine learning is moving from research into production in real-time rendering
on Apple platforms, and how to integrate it into your renderer with Metal 4. The talk
walks through three levels of ML integration — from the ready-to-use MetalFX neural
denoiser, to deploying your own pre-trained models with the Metal 4 ML command encoder,
to building tiny networks inline in shaders with the TensorOps API on the M5 / A19 Pro
neural accelerator.

## Key topics

- **Three levels of ML in the rendering pipeline:** MetalFX (black-box denoise/upscale),
  Metal 4 ML command encoder (deploy pre-trained models in your command buffer),
  TensorOps API (custom networks inline in shaders)
- **MetalFX Denoising** — combined neural upscaler + denoiser for low-latency live
  viewports; one sample-per-pixel path tracing → near-final quality. Demoed with
  Maxon's Redshift Live in Cinema 4D
- **Three MetalFX best practices:** clean auxiliary inputs (diffuse albedo is the
  strongest signal); store what the viewer sees (primary surface replacement for
  mirrors/glass via Fresnel-blended albedo); correct dejittered motion vectors
- **Auxiliary inputs:** diffuse albedo, depth, motion vectors; transparency overlay
  (upscale-only, no denoise); denoiser strength mask (0–1 per-pixel); reactive mask
  for unreliable motion (e.g. alpha-blended particles)
- **Neural tone mapper** — train offline (e.g. HDRNet, Gharbi et al. 2017) in PyTorch,
  export to `MTLPackage`, run in a Metal 4 command buffer to collapse a multi-stage
  post-processing chain into a single neural evaluation
- **TensorOps / inline MLPs** — tiny networks (≤ a few thousand params), online-trained
  per frame; sky-visibility probe (3-4-4-3 MLP) that adapts to dynamic day-night cycles
- **Cooperative tensors + SIMD-group execution scope** — keep matmul results in fast
  thread storage, avoid round trips to main memory; thread vs SIMD-group execution scope

## Related sessions to fetch (referenced in this talk)

- [ ] Go further with Metal 4 games
- [ ] Combine Metal 4 machine learning and graphics

## Chapter summary (Summary tab)

- **0:00 Introduction** — ML transforming real-time rendering on Apple platforms;
  preview of three levels of ML integration: MetalFX Denoising, custom networks with
  Metal 4, and inline networks with TensorOps.
- **2:16 MetalFX Denoising** — integrating MetalFX Denoising into a one-sample-per-pixel
  path tracer. Auxiliary inputs (albedo, depth, motion vectors), clean inputs,
  transparency overlay, denoiser strength mask, and primary surface replacement for
  mirrors and glass — illustrated with Maxon's Redshift Live.
- **9:57 Deploy custom ML networks with Metal 4** — train a neural tone mapper offline
  (e.g. HDRNet), export to MTLPackage, execute inside a Metal 4 command buffer alongside
  existing render passes to replace complex post-processing with one network.
- **13:40 Inline neural networks with tensorOps** — build and run small MLPs directly in
  Metal shaders using the TensorOps API and cooperative tensors. An online-trained sky
  visibility probe adapts to dynamic scenes each frame.
- **20:55 Next steps** — recap of the three levels; where to start: download Xcode,
  explore Metal 4 sample code, adopt MetalFX denoising for real-time apps first.

## Code

See `code.md` — one snippet from the Code tab (dejittered camera-only motion vectors),
plus spoken APIs surfaced in the transcript.
