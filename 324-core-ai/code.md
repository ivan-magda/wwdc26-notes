# Code samples — Session 324

Extracted from the Code tab. Timestamps map to the transcript. A few snippets
arrived with OCR noise from the extraction (e.g. mangled list comprehensions and
stray spaces); those have been lightly normalized to valid syntax where the intent
is unambiguous and flagged inline.

## 4:57 — Convert a PyTorch model to Core AI (coreai-torch)

```python
import torch
import coreai_torch

# Load trained snake model and sample input for tracing
pt_model = SnakeTransformer().load_checkpoint("snake.pt")
example  = torch.randn(1, 5, 16)

# Export the torch program including dynamic shape for input sequence
seq_len  = torch.export.Dim("seq_len", min=1, max=256)
exported = torch.export.export(
    pt_model, args=(example,),
    dynamic_shapes={"features": {1: seq_len}},
)
exported = exported.run_decompositions(coreai_torch.get_decomp_table())

# Convert torch graph → Core AI graph
ai_program = coreai_torch.TorchConverter().add_exported_program(
    exported, input_names=["features"], output_names=["logits"],
).to_coreai()

# Save as a .aimodel asset the runtime can load
ai_program.save_asset("SnakeTransformer.aimodel")
```

## 5:44 — Verify converted model numerics (Python bindings)

```python
import torch
import numpy as np
from coreai.runtime import AIModel, NDArray

# Load models
pt_model = SnakeTransformer().load_checkpoint("snake.pt")
ai_model = await AIModel.load("SnakeTransformer.aimodel")
function = ai_model.load_function("main")

# Assemble input sample — 10 frames of 16-dim game features, shape (1, 10, 16)
features = np.array(
    [extract_features(game) for _ in range(10)],
    dtype=np.float32,
)[np.newaxis]

# PyTorch reference
with torch.no_grad():
    pytorch_logits = pt_model(torch.from_numpy(features)).numpy()[0, -1]

# Core AI inference
result = await function({"features": NDArray(data=features)})
coreai_logits = result["logits"].numpy()[0, -1]

# Validate
max_diff = np.max(np.abs(pytorch_logits - coreai_logits))
assert max_diff < 0.01
```

> Note: the raw extraction garbled the list comprehension and a couple of method
> calls (`lextract_features(game) for - in range (10)`, `numpy )` etc.); restored
> above to the obvious intended form.

## 6:16 — Core types: load a model and run inference (Swift)

```swift
// Core types within Core AI
import CoreAI

// Load the '.aimodel' file
let model = try await AIModel(contentsOf: modelURL)

// Load the main inference function
let mainFunction: InferenceFunction = try model.loadFunction(named: "main")!

// Construct the n-dimensional input data
let inputNDArray: NDArray = nextInput()

// Run inference
var outputs = try await mainFunction.run(inputs: ["input": inputNDArray])

guard let outputNDArray = outputs.remove("output")?.ndArray else {
  // Handle unexpected missing output
}
```

## 8:32 — ModelPlayer initialization

```swift
// Initialize the player by loading the AIModel and InferenceFunction
struct ModelPlayer {
  let nextActionFunction: InferenceFunction

  init(modelURL: URL) async throws {
    let model = try await AIModel(contentsOf: modelURL)
    self.nextActionFunction = try model.loadFunction(named: "main")!
  }
}
```

## 8:49 — ModelPlayer.chooseAction (SnakePlayer conformance)

```swift
extension ModelPlayer: SnakePlayer {

  mutating func chooseAction(game: SnakeGame) async throws -> Direction {

    // Create an NDArray for the next input and write board features into it
    var inputFeatures = NDArray(shape: [game.stepCount, hiddenDim], scalarType: .float32)
    writeFeatures(of: game, into: inputFeatures.mutableView())

    // Run inference and extract the expected logits output NDArray
    var outputs = try await nextActionFunction.run(inputs: ["features": inputFeatures])
    guard let logits = outputs.remove("logits")?.ndArray else {
      throw ModelError.missingOutput
    }

    return predictedDirection(from: logits.view())
  }

  func writeFeatures(of game: SnakeGame, into view: consuming NDArray.MutableView<Float>) { … }
  func predictedDirection(from logits: NDArray.View<Float>) -> Direction { … }
}
```

## 10:02 — Input features per time step

```swift
// Features at each time step
var features = [Float]()

// Distance to wall in all directions, normalized between [0, 1]
features += [dWallUp, dWallDown, dWallLeft, dWallRight]

// Distance to nearest food, normalized between [-1, 1]
features += [dFoodX, dFoodY]

// Direction encoded as one-hot: [1,0,0,0]=up, [0,1,0,0]=down, etc.
features += dir.oneHotEncoding

// Distance to the other snake, normalized to [-1, 1]
features += [dUserX, dUserY]

// Direction of the opponent snake
features += dirU.oneHotEncoding
```

## 12:25 — Add KV cache buffers to the torch module

```python
# Update torch module to include key and value caches
# Use register_buffer to later make the exported torch program treat them as mutable

class SnakeTransformerStateful(nn.Module):
    def __init__(self, ...):
        super().__init__()
        self.register_buffer(
            "k_cache", torch.zeros(N_LAYERS, 1, MAX_SEQ_LEN, D_MODEL))
        self.register_buffer(
            "v_cache", torch.zeros(N_LAYERS, 1, MAX_SEQ_LEN, D_MODEL))
        # …
```

## 12:43 — Read/write KV caches in forward()

```python
# During forward pass, read/write KV caches

class SnakeTransformerStateful(nn.Module):

    def forward(self, features, position_ids):
        new_k, new_v = [], []
        for i, block in enumerate(self.blocks):
            # read previous keys/values from caches
            k_prev = self.k_cache[i]
            v_prev = self.v_cache[i]
            # ... compute q/k/v for the new token, attend over valid prefix ...
            new_k.append(k_updated)
            new_v.append(v_updated)

        # Update key/value caches
        self.k_cache.copy_(torch.stack(new_k))
        self.v_cache.copy_(torch.stack(new_v))

        return self.action_head(self.ln_final(x))
```

## 12:59 — Re-convert with state_names

```python
# Updated coreai-torch conversion code using key/value cache states
import torch
import coreai_torch

exported = torch.export.export(
    stateful_model,
    args=(example_features, example_position_ids),
    dynamic_shapes={"position_ids": {1: seq_len}},
)
exported = exported.run_decompositions(coreai_torch.get_decomp_table())

ai_program = coreai_torch.TorchConverter().add_exported_program(
    exported,
    input_names=["features", "position_ids"],
    state_names=["keyCache", "valueCache"],
    output_names=["logits"],
).to_coreai()

ai_program.save_asset("SnakeTransformer.aimodel")
```

## 13:16 — Store cache NDArrays on ModelPlayer

```swift
// Add stored properties for the key and value caches
struct ModelPlayer {
    let nextActionFunction: InferenceFunction

    var keyCache: NDArray
    var valueCache: NDArray

    init(modelURL: URL) async throws {
        let model = try await AIModel(contentsOf: modelURL)
        self.nextActionFunction = try model.loadFunction(named: "main")!

        self.keyCache = NDArray(shape: [layers, maxContext, hiddenDim], scalarType: .float32)
        self.valueCache = NDArray(shape: [layers, maxContext, hiddenDim], scalarType: .float32)
    }
}
```

## 13:38 — Pass MutableViews of caches as states at inference

```swift
extension ModelPlayer: SnakePlayer {
    mutating func chooseAction(game: SnakeGame, snakeID: Int) async throws -> Direction {
        // …

        var stateViews = InferenceFunction.MutableViews()
        stateViews.insert(&keyCache, for: "keyCache")
        stateViews.insert(&valueCache, for: "valueCache")

        // Run inference and extract the expected logits output NDArray
        var outputs = try await nextActionFunction.run(
            inputs: ["features": inputFeatures],
            states: stateViews)
        // …
    }
}
```

## 15:58 — Check model cache, gate the feature on specialization

```swift
// Check if your model can be loaded from the cache
let cache = AIModelCache.default

guard let model = try cache.model(for: modelURL, options: .default) else {
    Task { @MainActor in
        informUser("Preparing AI features. This may take a while…")
    }
}
```

## 16:42 — Request specialization explicitly (ahead of time)

```swift
// Explicitly request specialization
try await AIModel.specialize(contentsOf: modelURL)
```

---

## Useful API facts surfaced by the code

- **Conversion pipeline:** `torch.export.export(...)` → `run_decompositions(coreai_torch.get_decomp_table())` → `coreai_torch.TorchConverter().add_exported_program(...).to_coreai()` → `save_asset(".aimodel")`.
- **Dynamic shapes** are declared with `torch.export.Dim("seq_len", min=…, max=…)` and surface in Xcode's model viewer as `?` dimensions.
- **Swift runtime types:** `AIModel` (load/inspect) → `InferenceFunction` (`loadFunction(named:)`, `.run(inputs:states:)`) → `NDArray` (+ non-escapable `MutableView` / `View`).
- **States = mutable in-place inputs:** authored as `register_buffer` tensors in PyTorch, named via `state_names=` at conversion, passed at inference as `InferenceFunction.MutableViews` (`stateViews.insert(&buf, for: "name")`).
- **Specialization/caching:** `AIModelCache.default`, `cache.model(for:options:)` (returns nil if not yet specialized), `AIModel.specialize(contentsOf:)`, plus `SpecializationOptions` and app-group cache sharing.
