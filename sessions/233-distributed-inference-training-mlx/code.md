# Code samples — Session 233

Extracted from the Code tab. Timestamps map to the transcript chapters.

## Cluster hostfile (JSON)

One entry per node. `ssh` is the hostname `mlx.launch` uses to reach the machine;
`ips` is the local-network IP JACCL uses for initial coordination; `rdma` is the
per-peer list of RDMA device names (one slot per other node, `null` for self).

```json
[
  {
    "ssh": "m3-ultra-0",
    "ips": ["192.168.1.10"],
    "rdma": [null, "rdma_en5", "rdma_en4", "rdma_en3"]
  },
  {
    "ssh": "m3-ultra-1",
    "ips": ["192.168.1.11"],
    "rdma": ["rdma_en5", null, "rdma_en4", "rdma_en3"]
  },
  {
    "ssh": "m3-ultra-2",
    "ips": ["192.168.1.12"],
    "rdma": ["rdma_en5", "rdma_en4", null, "rdma_en3"]
  },
  {
    "ssh": "m3-ultra-3",
    "ips": ["192.168.1.13"],
    "rdma": ["rdma_en5", "rdma_en4", "rdma_en3", null]
  }
]
```

## Generating the hostfile with `mlx.distributed_config`

`--auto-setup` disables the Thunderbolt Bridge and configures each link for RDMA;
`--backend jaccl` selects mesh (use `jaccl-ring` for a ring). Embedded env vars are
set on every node at launch.

```bash
mlx.distributed_config \
    --hosts m3-ultra-0,m3-ultra-1,m3-ultra-2,m3-ultra-3 \
    --output "m3-ultra-jaccl.json" \
    --env MLX_METAL_FAST_SYNCH=1 \
    --auto-setup \
    --backend jaccl
```

## Distributed LLM inference (`mlx_lm.chat` via `mlx.launch`)

```bash
# Single-device LLM inference
mlx_lm.chat --model "Qwen/Qwen3.6-27B" --max-tokens 2048

# Distributed LLM inference across the cluster
mlx.launch --hostfile "m3-ultra-jaccl.json" -- \
    /remote/path/to/mlx_lm.chat --model "Qwen/Qwen3.6-27B" --max-tokens 2048
```

## Tensor vs. pipeline parallelism

Tensor parallelism is the default; append `--pipeline` to switch to pipeline
parallelism (not all models support it).

```bash
# Tensor parallelism (default)
mlx.launch --hostfile "m3-ultra-jaccl.json" -- \
    /remote/path/to/mlx_lm.chat --model "moonshotai/Kimi-K2.6" \
                                 --max-tokens 2048

# Pipeline parallelism — append --pipeline flag
mlx.launch --hostfile "m3-ultra-jaccl.json" -- \
    /remote/path/to/mlx_lm.chat --model "moonshotai/Kimi-K2.6" \
                                 --max-tokens 2048 \
                                 --pipeline
```

## Distributed fine-tuning (`mlx_lm.lora`, data-parallel)

Scale `--batch-size` by the number of devices so each machine still processes the
same number of samples per step.

```bash
# Single-device fine-tuning
mlx_lm.lora --model "Qwen/Qwen3.5-9B" \
             --data "mlx-community/wikisql" \
             --train --batch-size 4

# Distributed fine-tuning (scale --batch-size by number of devices)
mlx.launch --hostfile "hostfile.json" -- \
    /remote/path/to/mlx_lm.lora --model "Qwen/Qwen3.5-9B" \
                                  --data "mlx-community/wikisql" \
                                  --train --batch-size 16
```

## Python API — distributed inference with `sharded_load`

```python
import mlx.core as mx
from mlx_lm import stream_generate
from mlx_lm.utils import sharded_load

# Initialise distributed backend
group = mx.distributed.init(strict=True, backend="jaccl")
# Define parallelism
tensor_group, pipeline_group = group, None

# Shard the model
model, tokenizer = sharded_load("moonshotai/Kimi-K2.6", pipeline_group, tensor_group)
for response in stream_generate(model, tokenizer, prompt, max_tokens=1024):
    if group.rank() == 0:
        print(response.text, end="", flush=True)
```

## Python API — low-level layer sharding with `shard_linear`

```python
import mlx.core as mx
import mlx.nn as nn

# Initialise distributed backend
group = mx.distributed.init(strict=True, backend="jaccl")

# Define layer and shard it column-wise
layer = nn.Linear(1024, 1024)
sharded_layer = nn.layers.distributed.shard_linear(
    layer, strategy="all-to-sharded", group=group
)
data = mx.random.normal((1, 1, 1024))
output = sharded_layer(data)
mx.eval(output)
```

## All-reduce (`all_sum`) — Python, Swift, C++

The same collective sum across all Macs, expressed in each language. Swift's
`DistributedGroup(strict: .ring)` selects the ring backend.

```python
# Python
import mlx.core as mx
world = mx.distributed.init(strict=True, backend="jaccl")
data = mx.full((4,), float(world.rank()), dtype=mx.float32)
result = mx.distributed.all_sum(data, group=world)
mx.eval(result)
```

```swift
// Swift
let group = try DistributedGroup(strict: .ring)
let data = rank == 0
    ? MLXArray(converting: [1.0, 2.0, 3.0])
    : MLXArray(converting: [5.0, 6.0, 7.0])
let result = try group.allSum(data)
```

```cpp
// C++
namespace mx = mlx::core;
auto world = mx::distributed::init(/* strict */ true, "jaccl");
mx::array data = mx::full({4}, static_cast<float>(world.rank()), mx::float32);
mx::array result = mx::distributed::all_sum(data, world);
mx::eval(result);
```

## Standalone JACCL (C++, no MLX)

JACCL can be built without MLX and used for any distributed workload, including
non-ML applications.

```cpp
#include <jaccl/jaccl.h>
#include <iostream>

int main() {
    // Initialize JACCL group
    auto group = jaccl::init();
    std::cout << "Rank " << group->rank() << " of " << group->size() << std::endl;
    // Perform all-reduce sum
    float data[10] = {1.0f, 2.0f, 3.0f, 4.0f, 5.0f, 6.0f, 7.0f, 8.0f, 9.0f, 10.0f};
    float output[10];
    group->all_sum(data, output, sizeof(data), jaccl::Float32);
    std::cout << "Result: " << output[0] << std::endl;
    return 0;
}
```

---

## Useful API / config facts surfaced by the code

- `MLX_METAL_FAST_SYNCH=1` enables faster GPU↔CPU synchronization — important because compute runs on the GPU while communication runs on the CPU.
- `--backend jaccl` = mesh; `--backend jaccl-ring` = ring.
- `mlx.launch --hostfile <json> -- <remote/path/to/executable …>` — everything after `--` is the per-node command, using the executable's path on the nodes.
- Tensor parallelism is the MLX LM default; `--pipeline` switches to pipeline parallelism.
- Distributed fine-tuning scales `--batch-size` by device count to keep per-device samples-per-step constant.
- Python init: `mx.distributed.init(strict=True, backend="jaccl")`; sharded inference via `mlx_lm.utils.sharded_load(model, pipeline_group, tensor_group)`.
- Low-level sharding: `nn.layers.distributed.shard_linear(layer, strategy="all-to-sharded", group=…)`.
