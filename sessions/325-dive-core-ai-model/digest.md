---
title: "Dive into Core AI model authoring and optimization — Full Digest"
session: WWDC26 · 325
url: https://developer.apple.com/videos/play/wwdc2026/325/
duration: 29m
speakers: Sachin, Nicole
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 325 — Dive into Core AI model authoring and optimization

## TL;DR

This is the deep-dive on **Core AI's Python ecosystem** — the toolchain for taking an
arbitrary PyTorch model and shipping it efficiently on Apple Silicon. Five threads run
through the talk, all demonstrated on **SAM3** (Segment Anything Model, 850M params):

1. **`coreai-models` + Core AI Skills** — an open-source model repository plus agent
   skills you install into your coding assistant so it converts/optimizes models "like
   an expert from day one." Apple says most of the talk's code was co-developed with an
   agent using these skills.
2. **Convert** — `torch.export` → `TorchConverter` → `.aimodel` asset → inference from
   Python with numpy inputs. Feels familiar if you've used Core ML tools. Install with
   `pip install coreai-torch`.
3. **Optimize** — `coreai-opt`, a config-driven compression library (int4/int8/FP4/FP8,
   presets, calibration or QAT). int4 takes SAM3 from **3GB → 430MB**, but uniform
   aggressive quantization breaks a detection.
4. **Debug** — the new **Core AI Debugger** standalone app diagnoses exactly *which*
   layers the quantization broke, via on-device execution and PSNR sync-point comparison
   against a PyTorch reference. The fix: stop compressing the detector (only 4% of params).
5. **Author** — go beyond end-to-end conversion: fuse ops into a single kernel dispatch,
   embed **custom Metal kernels** in the asset, or fully **re-author** the model for iOS
   (split functions, conv projections, channels-first layouts, palettization). Re-authored
   SAM3's second inference is **76% faster** by caching image embeddings.

---

## 1. Models and skills

At the front of the ecosystem is the **`coreai-models`** open-source repository:

- A Swift package for running LLMs in your app.
- Ready-to-go model architectures, including cutting-edge LLMs.
- Examples engineered for various use-cases/constraints, plus reusable components for
  bringing your own models to Core AI.
- **Core AI Skills** — agent skills you install into your coding assistant. They translate
  high-level goals into a deployment plan, ask clarifying questions (target model, hardware
  families, constraints), and give the agent access to Apple engineers' best practices —
  from PyTorch code changes through conversion, optimization, and running.

Apple notes most code in the talk was co-developed with an agent actively using these skills.

## 2. Python conversion workflow

`pip install coreai-torch` pulls in both the `coreai` package and the `coreai-torch`
library on top. You hand `coreai-torch` a PyTorch **exported program** and it converts
directly to a Core AI model. It supports assembling multiple models into one artifact,
registering custom lowerings, inlining Metal 4 kernels, and specializing into optimized
assets that run natively on Apple Silicon — all from Python.

```python
import torch
import torch.nn as nn

class MLP(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(256, 512)
        self.fc2 = nn.Linear(512, 10)

    def forward(self, x):
        return self.fc2(torch.relu(self.fc1(x)))

model = MLP().eval()
example_input = (torch.randn(1, 256),)
exported_program = torch.export.export(model, example_input)
```

The `exported_program` captures the full computational graph — weights, operations, shapes.
Then the Core AI side:

```python
import coreai
import coreai_torch
from coreai.runtime import NDArray

converter = coreai_torch.TorchConverter()
converter.add_exported_program(
    exported_program,
    input_names=["features"], output_names=["logits"])
core_ai_program = converter.to_coreai()

core_ai_program.optimize()
asset = core_ai_program.save_asset("mlp.aimodel")

specialized_model = await AIModel.load("mlp.aimodel")
specialized_function = specialized_model.load_function("main")
result = await specialized_function({"features": NDArray(example[0].numpy())})
```

Inference is just a dictionary mapping input names → numpy tensors. Conversion,
optimization, and execution all happen from Python.

## 3. Model optimization with coreai-opt

The driving example is **SAM3**, an 850M-parameter prompt-based image segmentation model
with three parts:

- **Image encoder** — processes the image.
- **Text encoder** — handles the user's prompt.
- These two are **96% of the model's parameters** — getting them right is key.
- **Detector** — a DEtection TRansformer + mask decoder producing the segmentation mask
  (≈ 4% of params).

`coreai-opt` is config-driven: you describe what to compress and what to leave alone.
It supports multiple schemes (so you can optimize differently for macOS vs iOS),
int4/int8/FP4/FP8 weight compression with flexible granularity, and quantization with a
small calibration set or full quantization-aware training (QAT). The pipeline gains one
step: run the model through `coreai-opt` (with a config or a preset) before conversion.

Conversion details worth noting:
- A custom **decomposition table** preserves high-level semantics Core AI supports (e.g.
  attention) in the graph.
- A `coreai-opt` helper can cast the program to 16-bit float.

The baseline 32-bit SAM3 asset is **over 3GB**; on a flower image it detects all flowers.
Then compression:

- `presets.w4` → **4-bit per-channel symmetric quantization** in one line.
- `ExecutionMode.EAGER` for weight compression (use `GRAPH` mode for activations).
- Init `Quantizer` with the config, pass example inputs, finalize.

Result: the model drops to **~430MB**, but **one occluded flower is no longer detected.**
Aggressive uniform compression was applied to every layer, and not every layer handles it
equally. The output alone doesn't show which layers broke; you need to inspect the
model internally. That's what the Core AI Debugger does.

## 4. Core AI Debugger

A **new standalone application** for inspecting models on Apple platforms. It can visualize
model structure as a graph, execute the model on specific hardware for true runtime results,
and validate inference correctness against a reference run — all in one place.

**The workspace:**
- **Navigator** (left) — a structured list of operations grouped by PyTorch module
  (powerful for large models like SAM3; navigation feels familiar). Selecting a module,
  e.g. the detector decoder, highlights its nodes in the structure viewer.
- **Structure viewer** (top) — graphical model representation: operation connectivity,
  execution order, data dependencies.
- **Source viewer** (bottom) — keeps you grounded in the original Python code, down to the
  specific line.
- **Inspector** (right) — per-operation description plus input/output details.

**Runtime analysis:** click *device*, pick a target (e.g. the Mac), specify inputs
(pixel values, input_IDs, attention_mask), and Run. The model is specialized for the device;
the structure viewer updates to show it exactly as it runs, and clicking any operation shows
its **output tensor directly in the inspector** with no code changes. Scrolling to the final
op and previewing the mask confirms the missing flower — matching the notebook.

**Comparison against a reference:** a **new save-intermediates API** executes a PyTorch model
and captures intermediate tensors at each op. Run it on the int4 model alongside the original
SAM3 to produce an Intermediates File. In the debugger, start a comparison: the current
configuration on the left, a reference run (different Target / Compute Unit, or an
Intermediates File) on the right.

- The navigator fills with **sync points** — paired operations where the specialized model's
  output should match the PyTorch result. The debugger finds these automatically.
- Each sync point carries a similarity metric. Default is **PSNR** (peak signal-to-noise
  ratio), changeable to whatever suits the model. Green nodes = similar, red = significant
  difference; yellow = moderate divergence.
- Sort by similarity, walk the worst sync points with the arrow key. The pattern emerges:
  the **vast majority of low-PSNR sync points come from the detector decoder.** The
  quantization mildly corrupted the detector.

The fix: since the detector is only ~4% of params, compressing it buys almost nothing. Change
the scheme to **ignore the detector**, re-export, and quality returns to baseline — all
flowers detected, model still a fraction of original size. The talk's framing: hours of manual
tensor comparison turned into a visual diagnosis "in minutes."

## 5. Advanced authoring — operation fusion and fast kernels

Beyond single end-to-end conversion, you can zoom into the PyTorch source (a graph of
computations) and tune how it runs on hardware. The simplest move: **fuse a group of ops into
one**, replacing several steps with a single kernel dispatch. Core AI already ships
**pre-packaged fast kernels** and primitives for heavy operations like **Scaled Dot Product
Attention** (common in transformers); examples of leveraging them live in `coreai-models`.

## 6. Custom Metal 4 kernels

For cutting-edge customization, embed your own **Metal Shading Language (MSL)** kernel into
the model asset. You add a second input to `coreai-torch` — the kernel source — and the
converter bundles the PyTorch model and the kernel into a single asset with the MSL embedded.

The example is a **SiLU** (Sigmoid Linear Unit) activation. You write a PyTorch reference
(what `torch.export` sees during tracing) and the actual element-wise MSL kernel (one thread
per element):

```python
import torch
from coreai_torch.dsl import TorchMetalKernel, MetalParameter

def silu_torch(x):
    return x * torch.sigmoid(x)

SILU_MSL = """
float val = float(x[gid]);
float sig = 1.0f / (1.0f + exp(-val));
y[gid] = TYPE(val * sig);
"""

silu_kernel = TorchMetalKernel(
    name="fused_silu",
    input_names=["x"],
    result_names=["y"],
    src=SILU_MSL,
    torch_defn=silu_torch,
    metal_params=[MetalParameter("gid", "uint", "thread_position_in_grid")],
    template_dtypes={"x": "TYPE"},
)
```

Note the input/output names `x` and `y` are reused inside the MSL. Then use it like any
Python function, passing the thread grid and `result_shapes` (so Core AI can bake in output-
shape computation for dynamic-shaped inputs), and register it on the converter:

```python
class MyModel(torch.nn.Module):
    def __init__(self):
        super().__init__()
        self.linear = torch.nn.Linear(256, 256)

    def forward(self, x):
        h = self.linear(x)
        n = h.numel()
        return silu_kernel(
            h,
            threads_per_grid_size=(n, 1, 1),
            threads_per_thread_group=(min(n, 256), 1, 1),
            result_shapes=[h.shape],
        )

exported_program = torch.export.export(MyModel(), (torch.randn(1, 256),))

converter = coreai_torch.TorchConverter()
converter.register_custom_kernels([silu_kernel])
converter.add_exported_program(exported_program,
                               input_names=["x"], output_names=["y"])
deployable = converter.to_coreai()  # MSL integrated into asset
```

The MSL ships inside the `.aimodel` — the kernel travels with the model. For efficient kernel
writing and an optimized kernel running live on SAM3, the talk points to **"Optimize custom
machine learning operations with Metal tensors."**

## 7. Model re-authoring for iOS

For aggressive on-device optimization — especially iOS — fusion isn't enough; you rewrite the
whole model for a target. **Re-authoring** can mean different operations, novel tensor layouts,
and changed interfaces: a completely different source implementation. Techniques mentioned:

- **Predefined PyTorch patterns** that tell Core AI about a concept so it maps to an optimized
  runtime implementation — e.g. **in-place KV-cache updates** for LLMs.
- For iOS specifically: **static tensor shapes, channels-first layouts, convolutional op
  patterns** — these let Core AI map to native hardware primitives and meet on-device constraints.
- **Rigorous module- and model-level testing** (unit + integration). `coreai-models` ships
  reusable components and best-practice examples to start from, and Core AI Skills help the
  agent write Apple-Silicon-optimized PyTorch from day one.

**Re-authored SAM3:**

- Split into **three separate functions** in the Core AI model — `image_encode`, `text_encode`,
  `detect` — instead of one. Each runs at a different cadence (e.g. process one prompt once,
  reuse it across many images), has a clean interface, and can be compressed/authored
  independently.
- The image encoder's attention block is rewritten with **convolutional projections** instead
  of Linear layers (a pattern that lets Core AI use native hardware primitives on the right
  compute unit). The text encoder gets similar treatment; the small decoder stays mostly
  unchanged (minimal payoff).
- Compression: **4-bit palettization with per-channel scales** on the two encoders (a
  lookup-table compression well-suited to iOS power efficiency; a preset exists, but the demo
  uses the lower-level `KMeansPalettizer` — construct, prepare, finalize). The **detector stays
  uncompressed** (known to be compression-sensitive from the debugger exercise).
- Input image size dropped from **1008px to 336px** for iPhone; all three modules cast to
  half-precision via `torch.export`.
- One `TorchConverter`, three exported programs, each with its own entrypoint name →
  **one asset with three callable functions.**
- **Payoff:** first run segments all flowers; swapping the prompt to "butterfly" only re-runs
  the **text encoder + detector** (cached image embeddings), making the **second inference 76%
  faster even after warmup.**

## 8. Next steps

Convert with Core AI's Python libraries → optimize with `coreai-opt` → debug with Core AI
Debugger → build on `coreai-models` examples → plug Core AI Skills into your AI agent.
