# Session 325 — Dive into Core AI model authoring and optimization

- **URL:** https://developer.apple.com/videos/play/wwdc2026/325/
- **Duration:** 29m
- **Speakers:** Sachin (Core AI team), Nicole (Core AI Debugger demo)

## Description

A deep dive into Core AI's Python ecosystem for getting models running efficiently
on Apple Silicon. Covers the full deployment lifecycle: the `coreai-models`
open-source repository and Core AI Skills for coding agents; converting PyTorch
models with `coreai-torch`; compressing models with `coreai-opt`; the new
standalone Core AI Debugger app; and advanced authoring (kernel fusion, custom
Metal kernels, and full model re-authoring) — demonstrated end-to-end on
Segment Anything Model (SAM3).

## Key topics

- `coreai-models` open-source repository — ready-to-go model architectures (incl. LLMs), reusable components, a Swift package for running LLMs in-app, and installable **Core AI Skills** for coding assistants
- Python conversion workflow: `torch.export` → `TorchConverter` → `.aimodel` asset → inference from Python with numpy inputs (`pip install coreai-torch`)
- `coreai-opt` config-driven compression — int4/int8/FP4/FP8 weight compression, flexible granularity, presets (e.g. `presets.w4`), calibration-data quantization or QAT; EAGER vs GRAPH execution modes; per-channel symmetric quantization
- SAM3 case study (850M params): image encoder + text encoder = 96% of params; detector ≈ 4%; int4 quantization shrinks 3GB → 430MB but breaks an occluded-flower detection
- **Core AI Debugger** — standalone app: navigator (PyTorch module hierarchy), structure viewer (op graph), source viewer (original Python), inspector (tensor details), on-device runtime execution, intermediates capture, sync-point comparison with PSNR metric
- Advanced authoring: operation fusion into a single kernel dispatch; pre-packaged fast kernels (Scaled Dot Product Attention)
- Custom Metal 4 kernels embedded in the `.aimodel` — `TorchMetalKernel` registered with a PyTorch reference (SiLU example) + MSL source
- Model re-authoring for iOS — split into independent functions (image_encode / text_encode / detect), convolutional projections, channels-first layouts, static shapes, KV-cache patterns, 4-bit palettization; cached image embeddings → 76% faster second inference

## Related sessions to fetch (referenced in this talk)

- [ ] Meet Core AI
- [ ] Optimize custom machine learning operations with Metal tensors

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of Core AI's complete Python ecosystem for model deployment on Apple Silicon; the lifecycle from optimization and conversion through debugging and app integration.
- **1:49 Models and skills** — the `coreai-models` open-source repository: ready-to-go architectures, reusable components, and agent skills you install into your coding assistant for Core AI best practices from day one.
- **3:27 Python workflow** — convert a PyTorch model with `coreai-torch`: export with `torch.export`, run `TorchConverter` with input/output names, save as `.aimodel`, run inference from Python with numpy inputs.
- **5:54 Model optimization** — compress with `coreai-opt`'s config-driven library; SAM3 (850M) with int4 per-channel symmetric quantization presets, 3GB → 430MB, and the trade-offs of aggressive uniform compression.
- **10:40 Core AI Debugger** — standalone app to inspect models: navigator (module hierarchy), structure viewer (op graph), source viewer (Python), inspector (tensor details), and running on-device to inspect intermediate tensors.
- **19:27 Advanced authoring** — beyond end-to-end conversion: fusing operations into a single kernel dispatch and leveraging pre-packaged fast kernels for heavy ops like SDPA.
- **20:43 Custom Metal kernels** — embed custom MSL kernels into a Core AI asset: a PyTorch reference alongside an MSL kernel, register a `TorchMetalKernel` with `TorchConverter`, ship it inside the `.aimodel`.
- **23:01 Model re-authoring** — re-author a PyTorch model from scratch for power-efficient iOS: split SAM3 into three functions (image_encode, text_encode, detect), convolutional projections, channels-first layouts, 4-bit palettization of encoders, cached image embeddings for a faster second inference.
- **28:46 Next steps** — convert with `coreai-torch`, optimize with `coreai-opt`, debug with Core AI Debugger, build on `coreai-models`, use Core AI Skills in your coding agent.

## Code

See `code.md` — 4 snippets extracted from the Code tab.
