---
title: "Explore distributed inference and training with MLX — Full Digest"
session: WWDC26 · 233
url: https://developer.apple.com/videos/play/wwdc2026/233/
duration: 22m
speakers: Tatiana (MLX team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 233 — Explore distributed inference and training with MLX

## TL;DR

Local LLMs keep outgrowing a single machine — longer contexts, harder tasks, bigger
weights eventually blow past one Mac's memory, compute, or bandwidth. This session
shows how to chain multiple Macs into a cluster and run **distributed inference and
fine-tuning** entirely on hardware you own. Five headline themes:

1. **A full Apple-Silicon stack for distributed work.** New in **macOS 26.2**: **RDMA over Thunderbolt 5** moves data straight from one machine's memory to another's, bypassing most CPU/OS overhead. On top sits **JACCL**, Apple's open-source collective communication library, then **MLX**, then **MLX LM**.
2. **Cluster setup is a wiring + topology problem.** Four M3 Ultras connected with Thunderbolt 5; **mesh vs. ring** trades latency for bandwidth/scalability. JACCL can auto-route per message size. A System Settings toggle enables RDMA.
3. **One command to go distributed.** `mlx.distributed_config` generates the JSON hostfile; `mlx.launch` SSHes into every node and runs the executable. Wrapping `mlx_lm.chat` / `mlx_lm.lora` is almost identical to the single-device command.
4. **Real speedups and new capabilities.** ~**3x** token rate for Qwen 3.6 (27B) inference across four Macs; a **1-trillion-parameter** Kimi 2.6 (~1 TB at 8-bit) that doesn't fit on one M3 Ultra runs across four; **>3x** fine-tuning throughput (~180 → ~600 tok/s) for Qwen 3.5 (9B).
5. **APIs at every level.** CLI for the easy path, then fine-grained **Python / Swift / C++** for sharding and collective ops — plus **JACCL standalone** for any distributed workload, even non-ML.

---

## 1. Why distributed (0:00)

Models keep getting larger and smarter, and we use them for more: longer contexts,
harder tasks, more complex workflows. Eventually **memory, compute, or bandwidth on
a single machine becomes the limit.** With multiple devices you can either run
**larger** models than fit on one Mac, or **accelerate** the ones that do — through
distributed inference and training.

The session is structured CLI-first (get models running), then Python (for
experimentation), then Swift (for embedding in apps), with C++ alongside.

## 2. The distributed-communication stack (2:09)

Two machines need a **physical interconnect** plus a **transport protocol** that
pushes bytes from one machine's memory to another's.

- **RDMA over Thunderbolt 5** (new in **macOS 26.2**) — Remote Direct Memory Access moves data directly between machines' memory, avoiding most CPU and OS overhead, giving high-bandwidth/low-latency communication. On its own it only does raw point-to-point movement between two machines.
- **JACCL** — an **open-source collective communication library built by Apple**. It leverages RDMA over Thunderbolt and provides collective primitives for sending data between machines and combining results across the whole group, without you managing the low-level transport. Not ML-specific — any distributed Apple-Silicon workload can build on it.
- **MLX** — Apple's open-source ML library for Apple Silicon. It uses JACCL for low-latency distributed communication and orchestrates distributed jobs across the cluster.
- **MLX LM** — an open-source Python package on top of MLX with CLI tools and a Python API for running LLMs locally.

(New to MLX? The talk points back to WWDC25 "Getting Started with MLX on Apple Silicon.")

## 3. Building the cluster (4:32)

Four M3 Ultras, connected with Thunderbolt 5 cables. The **topology directly affects
communication time**, so the talk first defines that time, then covers topologies,
enabling RDMA, and launching jobs.

### Communication time = latency + transfer time (5:08)

- **Latency** — fixed cost per communication op, independent of data size.
- **Transfer time** — grows with message size, depends on link bandwidth.
- **Small messages** → latency dominates. **Large messages** → transfer time dominates.
- So a workload is either **latency-bound** or **bandwidth-bound**, which favors different topologies.

### Mesh vs. ring (5:50)

- **Full mesh** — every machine connects directly to every other → lowest possible latency for group communication.
- **Ring** — each node connects only to its two neighbors. Non-adjacent nodes communicate through intermediates (higher latency), **but** it needs fewer cables/ports, scales to more nodes more easily, and the spare Thunderbolt ports can run **two or three cables per neighbor** to raise per-link bandwidth (lower transfer time).
- A mesh wiring lets you route any given communication as mesh **or** ring. **JACCL automatically picks the best** per message size and operation — mesh when latency matters, ring when bandwidth matters. For that flexibility, the demo wires all four into a **mesh**.

### Enabling RDMA (7:06)

On each machine: open System Settings, search "RDMA", click **Enable RDMA over
Thunderbolt**, enable, and **reboot**.

### Launching jobs with `mlx.launch` (7:33)

Programs are launched over the local network (Wi-Fi/Ethernet) from any machine with
SSH access — a MacBook in the demo. After launch, the cluster machines communicate
directly over the Thunderbolt links. `mlx.launch` does the orchestration: given the
executable and a **JSON hostfile**, it SSHes into each node (by hostname) and starts
the executable everywhere.

The hostfile is a JSON array, one entry per node:

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

- **`ssh`** — hostname `mlx.launch` uses to reach the machine.
- **`ips`** — local-network IP JACCL uses for initial coordination.
- **`rdma`** — RDMA device names per Thunderbolt peer connection (`null` for the node itself).

You can write it by hand, or generate it with **`mlx.distributed_config`**:

```bash
mlx.distributed_config \
    --hosts m3-ultra-0,m3-ultra-1,m3-ultra-2,m3-ultra-3 \
    --output "m3-ultra-jaccl.json" \
    --env MLX_METAL_FAST_SYNCH=1 \
    --auto-setup \
    --backend jaccl
```

- **`--env MLX_METAL_FAST_SYNCH=1`** — set on every node at launch; enables faster GPU↔CPU synchronization. Critical here because compute runs on the GPU while communication runs on the CPU.
- **`--auto-setup`** — configures the Thunderbolt network automatically: disables the Thunderbolt Bridge on all machines and configures each link for RDMA. Without it, the script just prints the commands for you to review and run yourself.
- **`--backend jaccl`** = mesh; **`jaccl-ring`** = ring.

Running it: checks all hosts are reachable over SSH → probes Thunderbolt ports to
discover the physical topology → (with `--auto-setup`) configures links for RDMA →
writes the JSON hostfile `mlx.launch` needs.

## 4. Distributed inference with MLX LM (10:33)

The easy path is the CLI. On a single Mac (as shown at WWDC25):

```bash
mlx_lm.chat --model "Qwen/Qwen3.6-27B" --max-tokens 2048
```

To run the same model across the cluster, **wrap it with `mlx.launch`**:

```bash
mlx.launch --hostfile "m3-ultra-jaccl.json" -- \
    /remote/path/to/mlx_lm.chat --model "Qwen/Qwen3.6-27B" --max-tokens 2048
```

Everything after `--` is the per-node command, using the executable's **remote path
on each node**. MLX LM shards the model and coordinates the distributed inference for
you. Caveat: all required libraries (e.g. MLX) must be installed and the executable
accessible on every Mac.

**Result:** chatting with Qwen 3.6 (27B) side by side — one M3 Ultra vs. four — the
cluster generates tokens at **nearly 3x** the single-machine rate (exact speedup
depends on model size and architecture).

But speed isn't the only reason to go distributed: **some models simply don't fit on
one machine.** Kimi 2.6 has **1 trillion** total parameters; even at 8-bit
quantization the weights alone are ~**1 TB** — too much for one M3 Ultra, but it fits
across four.

## 5. Model parallelism strategies (13:35)

MLX / MLX LM support two ways to split weights and computation:

- **Pipeline parallelism** — splits the model **by depth**. Each machine holds a group of layers; data flows through machines sequentially. **Does not speed up inference** (each token still traverses all layer groups in order), but communication is **simple** — machines only exchange activations at layer-group boundaries.
- **Tensor parallelism** — splits the model **by width**. Each machine holds part of every layer, so all machines process the same token at once. **Faster inference** via parallelized per-layer compute, but **much more frequent communication** (every layer, every token). Low latency is essential here, which is **why a mesh is crucial** — every machine reaches every other in a single hop.

Tensor parallelism is the **default** in MLX LM. Append `--pipeline` to switch (not
all models support pipeline parallelism):

```bash
# Tensor parallelism (default)
mlx.launch --hostfile "m3-ultra-jaccl.json" -- \
    /remote/path/to/mlx_lm.chat --model "moonshotai/Kimi-K2.6" --max-tokens 2048

# Pipeline parallelism
mlx.launch --hostfile "m3-ultra-jaccl.json" -- \
    /remote/path/to/mlx_lm.chat --model "moonshotai/Kimi-K2.6" --max-tokens 2048 \
                                 --pipeline
```

**Demo:** the 1-trillion-parameter Kimi 2.6 running locally across the four Macs with
tensor parallelism (no `--pipeline`), answering a prompt to implement a GPT
architecture in Python with MLX.

## 6. Distributed fine-tuning (15:53)

You can also **fine-tune** on your own hardware — fast, efficient, and fully private
(data never leaves your machines).

Single-machine training: split data into **batches**, compute gradients per batch,
update weights, repeat over the dataset until quality is reached.

**Data-parallel training** scales this out: **replicate the model on every Mac**, give
each machine a different batch, compute gradients locally, then **average the
gradients** so each update uses information from all batches. The model is replicated
while data is processed in parallel — with N machines you process data up to **N
times faster**.

CLI is again almost identical to single-device, just wrapped with `mlx.launch` and
pointing at `mlx_lm.lora` on the remote nodes. **Scale `--batch-size` by the device
count** so each machine still does the same samples-per-step:

```bash
# Single-device fine-tuning
mlx_lm.lora --model "Qwen/Qwen3.5-9B" --data "mlx-community/wikisql" \
             --train --batch-size 4

# Distributed (4 devices → batch-size ×4)
mlx.launch --hostfile "hostfile.json" -- \
    /remote/path/to/mlx_lm.lora --model "Qwen/Qwen3.5-9B" --data "mlx-community/wikisql" \
                                  --train --batch-size 16
```

**Result:** fine-tuning Qwen 3.5 (9B), a single M3 Ultra processes ~**180 tok/s**
while the cluster does ~**600 tok/s** — **>3x** for fine-tuning. A local training
cluster, no cloud.

## 7. Fine-grained Python / Swift / C++ APIs (18:34)

Beyond the CLI, MLX exposes fine-grained control over sharding and distributed ops —
experiment in Python/C++ or embed models in apps with Swift.

**Distributed inference in Python with MLX LM** — init the group, choose parallelism,
shard with `sharded_load`, then use the model exactly as on a single device:

```python
import mlx.core as mx
from mlx_lm import stream_generate
from mlx_lm.utils import sharded_load

group = mx.distributed.init(strict=True, backend="jaccl")
tensor_group, pipeline_group = group, None

model, tokenizer = sharded_load("moonshotai/Kimi-K2.6", pipeline_group, tensor_group)
for response in stream_generate(model, tokenizer, prompt, max_tokens=1024):
    if group.rank() == 0:
        print(response.text, end="", flush=True)
```

**Low-level sharding** with MLX primitives — shard a single `Linear` layer with
tensor parallelism:

```python
import mlx.core as mx
import mlx.nn as nn

group = mx.distributed.init(strict=True, backend="jaccl")
layer = nn.Linear(1024, 1024)
sharded_layer = nn.layers.distributed.shard_linear(
    layer, strategy="all-to-sharded", group=group
)
data = mx.random.normal((1, 1, 1024))
output = sharded_layer(data)
mx.eval(output)
```

**Collective ops (`all_sum` / all-reduce)** — the same distributed sum across all
Macs, in each language:

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

**JACCL standalone (no MLX)** — JACCL can be built without MLX and used for any
distributed workload, even non-ML, via its C++ API:

```cpp
#include <jaccl/jaccl.h>
#include <iostream>

int main() {
    auto group = jaccl::init();
    std::cout << "Rank " << group->rank() << " of " << group->size() << std::endl;
    float data[10] = {1,2,3,4,5,6,7,8,9,10};
    float output[10];
    group->all_sum(data, output, sizeof(data), jaccl::Float32);
    std::cout << "Result: " << output[0] << std::endl;
    return 0;
}
```

## 8. Next steps (20:45)

The whole stack — RDMA over Thunderbolt up through MLX and MLX LM — scales from one
device to many with minimal changes to single-device code, across CLI, Python, Swift,
and C++. Benefits: faster inference, the ability to run trillion-parameter models,
and faster fine-tuning. With a distributed cluster you can run local AI agents powered
entirely by MLX. Pointers: the companion WWDC26 session "Run local agentic AI on the
Mac using MLX," documentation on custom parallelism strategies and training loops, and
MLX LM's built-in **distributed server** for serving models.
