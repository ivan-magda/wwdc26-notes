# Session 324 — Meet Core AI

- **URL:** https://developer.apple.com/videos/play/wwdc2026/324/
- **Duration:** 21m
- **Speakers:** Ben (Core AI team engineer)

## Description

Core AI is the inference framework that powers on-device Apple Intelligence, now
opened up to developers. It's a complete set of technologies covering the full
model deployment lifecycle — conversion, integration, profiling, optimization,
debugging, and specialization — running across all of Apple Silicon (CPU, GPU,
Neural Engine) with a modern memory-safe Swift API and a Python/PyTorch authoring
toolchain. The session walks the lifecycle end-to-end by building a two-player
snake game where one snake is driven by an on-device transformer model.

## Key topics

- **Core AI overview** — inference framework behind Apple Intelligence; leverages CPU/GPU/ANE; no server, no per-token cost; scales from small (speaker diarization) to large (70B-parameter agentic LLM) models
- **Model conversion** — `coreai-torch` Python package; `torch.export` with `dynamic_shapes`; `run_decompositions` with Core AI's decomp table; `TorchConverter().to_coreai()`; save `.aimodel`; numerical-correctness check via Python bindings
- **App integration (CoreAI Swift framework)** — `AIModel`, `InferenceFunction`, `NDArray`; Xcode model viewer (size, op distribution, function signatures, `?` = dynamic dim); progressively-disclosing, non-escapable, memory-safe API
- **Profiling** — new Core AI instrument in Xcode; spotting inference latency growing over time (quadratic transformer complexity vs. sequence length)
- **Optimization with KV cache as state** — `register_buffer` in PyTorch → mutable buffers → Core AI **states**; re-convert with `state_names`; pass `MutableViews` of caches at inference; removes need to feed full history
- **Additional tooling** — Python authoring with Core AI APIs + Metal 4 custom kernels; **Core AI Debugger** (trace tensors back to Python source); **Core AI debug gauge** in Xcode
- **Specialization** — source model → device-specific specialized+cached artifacts; `AIModelCache`, `SpecializationOptions`, `AIModel.specialize`; cache sharing across an app group; ahead-of-time (AOT) compilation on the dev machine
- **Low-level inference APIs** — optimal NDArray memory layout, pre-allocated outputs, async values for pipelining
- **Core AI Models repository** — convert-and-optimize popular models, AI skills, a Swift package of model-family libraries, and a Core AI Language Model API that plugs into Foundation Models

## Related sessions to fetch (referenced in this talk)

- [ ] Dive into Core AI model authoring and optimization
- [ ] Managing model specialization and caching (developer.apple.com article)
- [ ] Compiling Core AI models ahead of time (developer.apple.com article)

## Chapter summary (Summary tab)

- **0:00 Introduction** — intro to Core AI; agenda: model conversion, app integration, performance optimization, additional features.
- **0:33 What is Core AI** — the inference framework powering on-device Apple Intelligence, now for developers; full deployment lifecycle; all of Apple Silicon (CPU/GPU/ANE); modern Swift API, Python tooling, dedicated toolchain.
- **4:57 Model conversion** — convert a PyTorch model to Core AI with `coreai-torch`: `torch.export`, dynamic shapes, run the converter, verify numerical correctness.
- **6:16 App integration** — load and run a Core AI model with the CoreAI Swift framework: inspect in Xcode's model viewer, init an `AIModel`, prepare `NDArray` inputs, run inference, extract outputs.
- **10:48 Profiling with Instruments** — new Core AI instrument to profile latency; growing inference times from quadratic transformer complexity.
- **11:15 Optimizing performance** — eliminate slowdowns with a KV cache as a stateful input: author the cache in PyTorch, re-convert with `state_names`, pass `MutableViews` at inference.
- **14:13 Additional features** — Python authoring, the Core AI Debugger (numeric debugging), and the Core AI debug gauge in Xcode.
- **15:34 Specialization** — what specialization does, managing it with programmatic cache access + `SpecializationOptions`, and AOT compilation to shift work off-device.
- **20:07 Next steps** — recap (on-device inference across all Apple Silicon, Python tooling, debugging tools); invitation to explore the Core AI Models repository.

## Code

See `code.md` — 13 snippets extracted from the Code tab.
