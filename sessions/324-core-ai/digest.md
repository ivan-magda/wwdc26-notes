---
title: "Meet Core AI — Full Digest"
session: WWDC26 · 324
url: https://developer.apple.com/videos/play/wwdc2026/324/
duration: 21m
speakers: Ben (Core AI team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 324 — Meet Core AI

## TL;DR

Core AI is **the inference engine that already runs on-device Apple Intelligence**,
now exposed to third-party developers. It isn't a single API — it's a whole
lifecycle toolkit: a Python/PyTorch conversion path, a memory-safe Swift runtime, an
Xcode Instruments profiler, a visual numeric debugger, and a device-specialization
system with ahead-of-time compilation. Headline themes:

1. **Bring your own model, run it fully on-device.** Convert any PyTorch model to a
   `.aimodel` with the `coreai-torch` package; no server, no per-token cost; runs
   across CPU, GPU, and Neural Engine. Scales from tiny (speaker diarization) to a
   70B-parameter agentic LLM.
2. **A modern, memory-safe Swift runtime.** `AIModel` → `InferenceFunction` →
   `NDArray`, with non-escapable `MutableView`/`View` types that give zero-copy
   access without sacrificing safety. Progressive disclosure: simple by default,
   low-level knobs when you need them.
3. **The performance story is KV-caching as "state."** The talk's whole arc is a
   live bug — a transformer that slows down as the game's sequence grows (quadratic
   complexity) — fixed by promoting the key/value cache to a Core AI **state**
   (read + written in place each inference).
4. **Real tooling.** A dedicated Core AI **Instrument**, a **Debugger** that traces
   tensor values back to your original Python source, an Xcode **debug gauge** for
   streaming activity, and **specialization/caching** controls (incl. AOT
   compilation on your dev machine) so the first-run cost never lands in a user-
   interactive flow.

The running example is a two-player Snake game where one snake is driven by a small
on-device transformer — deliberately trivial so the lifecycle, not the model, is the
star.

---

## What Core AI is

Core AI is positioned as "the next evolution of on-device AI execution across Apple
platforms" — the same inference framework behind Apple Intelligence, now available
to developers. It's more than a runtime: it covers the **full model deployment
lifecycle**, from optimization and conversion through debugging and integration.

- Leverages **all of Apple Silicon** — CPU, GPU, and Neural Engine.
- **Modern Swift API**, memory-safe but performance-oriented.
- Reuses **familiar Python and PyTorch** foundations for authoring, optimization,
  and conversion.
- Supports deep customization: fine-grained inference management, model
  specialization, and **custom GPU kernels (Metal 4)**.
- A new toolchain: **ahead-of-time compilation**, a dedicated **Core AI Instrument**,
  and a **visual Debugger** that traces tensors back to your Python source.
- Designed to scale: small speaker-diarization models, larger vision-language models,
  up to a **70-billion-parameter** agentic LLM — all local, **no server, no cost per
  token.**

## Model conversion (coreai-torch)

The demo builds a `SnakeTransformer` in PyTorch (with help from an AI coding
assistant) and trains it on data from a naive game simulation. Converting it:

```python
import torch
import coreai_torch

pt_model = SnakeTransformer().load_checkpoint("snake.pt")
example  = torch.randn(1, 5, 16)

seq_len  = torch.export.Dim("seq_len", min=1, max=256)
exported = torch.export.export(
    pt_model, args=(example,),
    dynamic_shapes={"features": {1: seq_len}},
)
exported = exported.run_decompositions(coreai_torch.get_decomp_table())

ai_program = coreai_torch.TorchConverter().add_exported_program(
    exported, input_names=["features"], output_names=["logits"],
).to_coreai()

ai_program.save_asset("SnakeTransformer.aimodel")
```

Key points:

- **`dynamic_shapes`** marks the sequence-length dimension dynamic so the model isn't
  traced with the static sample length of 5.
- **`run_decompositions`** with Core AI's decomposition table lowers the program to
  ops Core AI understands.
- The output is a portable **`.aimodel`** asset.

Before leaving Python, verify the converted model matches PyTorch numerically using
the Core AI framework's **Python bindings**:

```python
from coreai.runtime import AIModel, NDArray

ai_model = await AIModel.load("SnakeTransformer.aimodel")
function = ai_model.load_function("main")

with torch.no_grad():
    pytorch_logits = pt_model(torch.from_numpy(features)).numpy()[0, -1]

result = await function({"features": NDArray(data=features)})
coreai_logits = result["logits"].numpy()[0, -1]

assert np.max(np.abs(pytorch_logits - coreai_logits)) < 0.01
```

(The "sufficiently small delta" is your call — `0.01` here is use-case specific.)

## App integration (CoreAI Swift framework)

Open the `.aimodel` in Xcode's **model viewer**: it shows model size, op
distribution, metadata, and a **Functions tab** with each function's exact
signature. A `?` in an NDArray dimension marks a **dynamic shape** — matching the
dynamic sequence length from conversion.

The runtime is a small, progressively-disclosing surface built on three types:

- **`AIModel`** — created from a `.aimodel` URL; inspects and loads functions.
- **`InferenceFunction`** — a single loaded compute graph; usually just one `main`.
- **`NDArray`** — holds multi-dimensional input/output data; `.run(...)` executes.

```swift
import CoreAI

let model = try await AIModel(contentsOf: modelURL)
let mainFunction = try model.loadFunction(named: "main")!

let inputNDArray: NDArray = nextInput()
var outputs = try await mainFunction.run(inputs: ["input": inputNDArray])

guard let outputNDArray = outputs.remove("output")?.ndArray else { /* … */ }
```

`AIModel` and `InferenceFunction` are typically built once (e.g. at app init). The
demo wraps this in a `ModelPlayer` conforming to a `SnakePlayer` protocol whose
`chooseAction(game:)` writes board features into an `NDArray`, runs inference, and
reads back logits:

```swift
mutating func chooseAction(game: SnakeGame) async throws -> Direction {
    var inputFeatures = NDArray(shape: [game.stepCount, hiddenDim], scalarType: .float32)
    writeFeatures(of: game, into: inputFeatures.mutableView())

    var outputs = try await nextActionFunction.run(inputs: ["features": inputFeatures])
    guard let logits = outputs.remove("logits")?.ndArray else { throw ModelError.missingOutput }

    return predictedDirection(from: logits.view())
}
```

`NDArray.MutableView` (and read-only `View`) are **non-escapable** types — safe,
efficient, zero-copy access to the NDArray's backing storage. The input features per
step encode normalized wall distances, nearest-food X/Y, a one-hot direction,
distance to the other snake, and the opponent's one-hot direction.

## Profiling: the slowdown

Running the game with both snakes on the model works — but **gets slower over time**.
The new **Core AI instrument** in Xcode shows inference intervals growing turn over
turn. The cause: transformers have **quadratic time complexity in sequence length**,
and the game's sequence grows with every move (the full history is fed in each step).

## Optimizing: KV cache as a Core AI "state"

The fix is the standard transformer trick — cache the per-element key/value
embeddings instead of recomputing them — expressed in Core AI through **states**:
inputs that are both **read and updated in-place** during inference.

Two wins at once: (1) no recomputation of past keys/values, and (2) no need to pass
the full game history, since older steps live in the state.

Author the caches in PyTorch with `register_buffer` (so they export as mutable
buffers Core AI converts to states):

```python
class SnakeTransformerStateful(nn.Module):
    def __init__(self, ...):
        super().__init__()
        self.register_buffer("k_cache", torch.zeros(N_LAYERS, 1, MAX_SEQ_LEN, D_MODEL))
        self.register_buffer("v_cache", torch.zeros(N_LAYERS, 1, MAX_SEQ_LEN, D_MODEL))
```

Read previous K/V from the cache in `forward`, write the new step's K/V back, then
re-convert — now adding **`state_names`**:

```python
ai_program = coreai_torch.TorchConverter().add_exported_program(
    exported,
    input_names=["features", "position_ids"],
    state_names=["keyCache", "valueCache"],
    output_names=["logits"],
).to_coreai()
```

On the Swift side, store the cache `NDArray`s on the `ModelPlayer` (sized for a fixed
maximum context length), then pass them as **`MutableViews`** in the `states:`
argument:

```swift
var stateViews = InferenceFunction.MutableViews()
stateViews.insert(&keyCache, for: "keyCache")
stateViews.insert(&valueCache, for: "valueCache")

var outputs = try await nextActionFunction.run(
    inputs: ["features": inputFeatures],
    states: stateViews)
```

Result: steady frame rate, and Instruments confirms inference latency now grows much
more slowly.

## Additional features

- **Advanced Python authoring** — beyond direct PyTorch conversion, the Core AI
  Python package supports authoring models directly with Core AI APIs, optimizing for
  Apple Silicon, and **custom Metal 4 kernels**. Deep dive: *"Dive into Core AI model
  authoring and optimization."*
- **Core AI Debugger** — visualize the converted model, inspect intermediate tensor
  values, and **trace ops back to the Python source line** that introduced them.
- **Core AI debug gauge** — streaming Core AI activity in Xcode while the app runs; a
  quick way to spot perf issues before reaching for Instruments.

## Specialization & caching

A shipped `.aimodel` is a **source representation** that runs on any Apple device, but
to actually run it must be **specialized** for the specific device — then cached.
First specialization can be **slow for large models**; later loads hit the cache and
are fast. Recommendation: **keep specialization out of user-interactive flows.**

Controls Core AI provides:

- **Programmatic cache access** — `AIModelCache.default`, `cache.model(for:options:)`
  returns nil if not yet specialized, so you can gate features or warn the user:

```swift
guard let model = try AIModelCache.default.model(for: modelURL, options: .default) else {
    Task { @MainActor in informUser("Preparing AI features. This may take a while…") }
}
```

- **Explicit, ahead-of-time specialization** — independent of loading, e.g. after an
  asset download or when the user opts into a feature:

```swift
try await AIModel.specialize(contentsOf: modelURL)
```

- **`SpecializationOptions`** to configure inference optimization; `AIModelCache`
  also lets you delete entries, set persistence policy, and **share a cache across
  apps in the same app group**.

Under the hood, specialization is two transforms: **(1) compilation** (segment, plan,
optimize compute) and **(2)** generating **executable artifacts** for the compute
units — artifacts tied to the device and OS version. Compilation dominates the
latency, so the toolchain supports **ahead-of-time compilation on your dev machine**,
shipping a pre-compiled model that still specializes per-device but finishes much
faster. See the articles *"Managing model specialization and caching"* and
*"Compiling Core AI models ahead of time."*

## Low-level inference knobs

For tight loops or complex pipelines:

- Query the **optimal memory layout** of NDArray args and allocate to match, avoiding
  layout conversions at inference time.
- **Pre-allocate output values** for the framework to write into.
- Use **asynchronous values** to pipeline multiple inference functions.

Higher-level APIs cover most cases; these are there when you're squeezing a hot path.

## Core AI Models repository

A starting point that bundles:

- A **collection of popular models**, each "a single command away" from being
  converted and optimized.
- **AI skills** expert in Core AI authoring, optimization, and conversion.
- A **Swift package** of per-model-family libraries with low-level inference
  optimizations already built in.
- An API for creating a **Core AI Language Model** that **plugs into the Foundation
  Models framework** — bring your own custom models and token-sampling strategies.

(That last point ties directly back to Session 241's model abstraction layer, where
`CoreAILanguageModel` backs a `LanguageModelSession`.)
