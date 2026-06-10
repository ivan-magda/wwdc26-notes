# Code samples — Session 325

Extracted from the session's Code tab. Timestamps map to the transcript chapters.

## 4:33 — Define a PyTorch model and export it

```python
import torch
import torch.nn as nn

# Define a simple model
class MLP(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(256, 512)
        self.fc2 = nn.Linear(512, 10)

    def forward(self, x):
        return self.fc2(torch.relu(self.fc1(x)))

# Export with torch.export
model = MLP().eval()
example_input = (torch.randn(1, 256),)
exported_program = torch.export.export(model, example_input)
```

## 5:02 — Convert, optimize, save, and run from Python

```python
import coreai
import coreai_torch
from coreai.runtime import NDArray

# Convert to Core AI
converter = coreai_torch.TorchConverter()
converter.add_exported_program(
    exported_program,
    input_names=["features"], output_names=["logits"])
core_ai_program = converter.to_coreai()

# Optimize and save to .aimodel
core_ai_program.optimize()
asset = core_ai_program.save_asset("mlp.aimodel")

# Run inference
specialized_model = await AIModel.load("mlp.aimodel")
specialized_function = specialized_model.load_function("main")
result = await specialized_function({"features": NDArray(example[0].numpy())})
```

## 21:12 — Define a custom Metal kernel (SiLU) alongside a PyTorch reference

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

## 22:05 — Use the custom kernel in a model and convert with it registered

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

---

## Spoken APIs / facts surfaced by the talk (no Code tab snippet)

- `pip install coreai-torch` installs both `coreai` and `coreai-torch`.
- `coreai-opt` presets: `presets.w4` → 4-bit per-channel symmetric quantization in one line. `ExecutionMode.EAGER` for weight compression, `GRAPH` mode for activations. `Quantizer` API: init with config, pass example inputs, finalize. `KMeansPalettizer` for 4-bit palettization (prepare → finalize).
- `coreai-opt` supports int4 / int8 / FP4 / FP8 weight compression and 16-bit-float casting helpers; calibration-data quantization or quantization-aware training (QAT).
- Custom decomposition table preserves high-level semantics (e.g. attention) in the exported program.
- Save-intermediates API: executes a PyTorch model and captures intermediate tensors per op, used to build a reference Intermediates File for the debugger's sync-point comparison (default metric: PSNR).
- Re-authoring: one `TorchConverter`, multiple exported programs each with its own entrypoint name (`image_encode`, `text_encode`, `detect`) → one `.aimodel` with three callable functions. SAM3 input image size dropped from 1008px to 336px for iPhone; encoders 4-bit palettized, detector left uncompressed.
