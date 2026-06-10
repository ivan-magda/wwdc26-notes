# Session 326 — Integrate on-device AI models into your app using Core AI

- **URL:** https://developer.apple.com/videos/play/wwdc2026/326/
- **Duration:** 24m
- **Speakers:** Carina (Core AI team)

## Description

A hands-on walkthrough of building a language-learning app with Core AI, Apple's new
set of technologies for running advanced AI capabilities entirely on device — no
server, no cost per token, no cloud latency. The talk pairs two task-specific
on-device models — SAM 3 (a vision-transformer for promptable image segmentation) and
Qwen (a multilingual reasoning LLM) — to turn a camera photo plus a prompt into a
vocab card with a segmented graphic, translation, and example sentence. It covers model
discovery, the Core AI Models repository, Swift integration via the Foundation Models
`LanguageModelSession` API, diagnosing first-run specialization latency, a deliberate
deployment strategy (first-run experience + Background Assets + ahead-of-time
compilation), and reusing the exact same code on macOS with larger model variants.

## Key topics

- Core AI value prop: on-device, private, no server / no cost per token / no cloud latency
- Decomposing a problem into two small (<1B param) task-specific models: SAM 3 (segmentation) + Qwen 0.6B (multilingual reasoning LLM)
- Three model-selection requirements: content coverage, multilingual support, device storage/memory constraints
- Getting models: PyTorch → Core AI conversion (Core AI PyTorch extensions + optimization packages) vs. the `coreai-models` GitHub repo with ready-made export recipes
- `.aimodel` files inspected in Xcode: size, platform targets (iOS 27.0 / macOS 27.0), Functions tab (tensor shapes, data types), e.g. SAM 3 = 623 MB exposing `imageEncode` / `detect`
- `coreai-models` Swift package runtime libraries (`CoreAILM`, `CoreAISegmentation`) abstract pre/post-processing — clean Swift API instead of raw tensors
- Loading + running SAM 3 via `CoreAIImageSegmenter` / `ImageSegmenter`; loading Qwen in one line via `CoreAILanguageModel`
- Reusing the Foundation Models `LanguageModelSession` API with a custom model — same `respond(to:)`, streaming, structured output, `@Generable` guided generation
- Model specialization: the on-device compile/cache step that drives first-run latency; diagnosed with the new Core AI Instruments template
- Deployment strategy: first-run experience, keeping >1 GB of models out of the app bundle, on-demand download via Background Assets
- Ahead-of-time (AOT) compilation with `coreai-build` to produce device-architecture-specific compiled assets that cut on-device specialization time
- Multiplatform: identical Swift code on macOS; batch processing, larger model variant (Qwen3 8B), longer context for pinyin + curriculum generation

## Related sessions to fetch

- [ ] Meet Core AI
- [ ] Dive into Core AI model authoring and optimization
- [ ] Discover Apple-Hosted Background Assets (WWDC25)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of Core AI: advanced on-device AI with no server, no cost per token, no cloud latency.
- **1:16 App concept: camera-based vocab learning** — iOS language-learning app; point camera at real-world objects to generate vocab cards (translation, example sentence, segmented image) on device.
- **2:52 Model discovery** — Define AI requirements (content, language, device constraints); select SAM 3 (text-prompted segmentation) + Qwen 0.6B (119-language reasoning model).
- **7:40 Getting models with the Core AI models repository** — Use the `coreai-models` GitHub repo to find popular models with ready-made export recipes; run export scripts for SAM 3 and Qwen to get optimized `.aimodel` files.
- **8:37 Integration** — Inspect `.aimodel` files in Xcode (size, platform targets, function signatures, tensor shapes); add the `coreai-models` Swift package; select `CoreAILM` and `CoreAISegmentation` as dependencies.
- **10:55 Writing the Swift integration code** — Load SAM 3 + run text-prompted segmentation; load Qwen with one `CoreAILanguageModel` line; use the familiar `LanguageModelSession` API with `@Generable` structured output for typed vocab card fields.
- **13:05 Diagnosing model specialization latency** — New Core AI Instruments template reveals first-run latency comes from specialization (compiling a Core AI model for the specific device); plan for it.
- **14:40 Deployment** — Deliberate strategy: first-run experience to introduce the feature, keep models out of the bundle to avoid bloating update size, trigger on-demand download via Background Assets only when the user opts in.
- **17:00 Ahead-of-time (AOT) compilation** — `coreai-build` performs compilation on the dev machine, generating device-architecture-specific compiled assets that dramatically reduce on-device specialization time.
- **18:03 iOS demo** — Fast model preparation with AOT, SAM 3 segmenting rocks/wood/sunflower, Qwen generating Mandarin vocab cards; seamless subsequent inferences from the cached model.
- **19:57 Multiplatform** — Same Swift code runs on macOS unchanged; add batch processing, step up to Qwen3 8B for higher-quality reasoning + pinyin, use longer context for curriculum generation; live demo turning road-trip photos into a lesson plan.
- **23:06 Next steps** — Core AI gives you everything for private, multi-platform on-device AI: no server, no cost per token, no cloud latency.

## Code

See `code.md` — 4 snippets extracted from the Code tab.
