# Session 233 — Explore distributed inference and training with MLX

- **URL:** https://developer.apple.com/videos/play/wwdc2026/233/
- **Duration:** 22m
- **Speakers:** Tatiana (research scientist, MLX team)

## Description

As local LLMs grow larger and tackle harder problems, a single machine eventually
runs out of memory, compute, or bandwidth. This session takes a deep dive into
scaling across multiple Macs with MLX: wiring four M3 Ultras into a cluster,
enabling RDMA over Thunderbolt 5, and running distributed LLM inference and
fine-tuning. It walks the full stack — RDMA, the open-source JACCL collective
communication library, MLX, and MLX LM — and shows the workflow from the command
line first, then via the Python, Swift, and C++ APIs for embedding distributed
work in your own apps.

## Key topics

- **Why distributed:** longer contexts, harder tasks, and bigger models exhaust a single machine's memory/compute/bandwidth
- **The stack:** RDMA over Thunderbolt 5 (macOS 26.2+) → JACCL (open-source collective communication library by Apple) → MLX → MLX LM
- **Cluster setup:** four M3 Ultras wired with Thunderbolt 5; mesh vs. ring topology trade-offs; enabling RDMA in System Settings + reboot
- **Communication time = latency + transfer time:** small messages are latency-bound (favor mesh), large messages are bandwidth-bound (favor ring); JACCL auto-picks per message size/op
- **Orchestration:** `mlx.distributed_config` to generate the JSON hostfile, `mlx.launch` to SSH into each node and run the executable
- **Distributed inference:** wrap `mlx_lm.chat` with `mlx.launch`; ~3x token rate for Qwen 3.6 (27B) on 4 Macs vs. one
- **Model parallelism:** tensor parallelism (split by width, faster, frequent comms, default) vs. pipeline parallelism (split by depth, simpler comms, `--pipeline` flag); 1-trillion-param Kimi 2.6 (~1 TB at 8-bit) runs across four Macs
- **Distributed fine-tuning:** data-parallel training via `mlx_lm.lora`; replicate model, parallel batches, average gradients; ~180 → ~600 tok/s (>3x) for Qwen 3.5 (9B)
- **APIs:** fine-grained Python / Swift / C++ for `init`, `sharded_load`, `shard_linear`, and `all_sum`/all-reduce primitives; JACCL usable standalone (even non-ML) via its C++ API

## Related sessions to fetch (referenced in this talk)

- [ ] Run local agentic AI on the Mac using MLX (WWDC26)
- [ ] Getting Started with MLX on Apple Silicon (WWDC25)
- [ ] Explore large language models on Apple Silicon with MLX (WWDC25)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Why distributed AI becomes necessary as models grow; preview of CLI tools, Python API, and Swift for embedding distributed workflows.
- **2:09 Distributed communication** — The hardware/software stack: RDMA over Thunderbolt 5 for low-latency data movement, JACCL (open-source collective communication library), and MLX tying it together.
- **4:32 Setting up your cluster** — Connecting four M3 Ultras; latency vs. bandwidth trade-offs; mesh vs. ring; enabling RDMA in System Settings; `mlx.distributed_config` and `mlx.launch` to configure and orchestrate.
- **10:33 Distributed inference and fine-tuning** — Distributed LLM inference with one CLI command: wrap `mlx_lm.chat` with `mlx.launch` to shard a 27B Qwen model across four M3 Ultras, ~3x the single-machine token rate.
- **13:35 Model parallelism strategies** — Tensor parallelism (split by width, faster inference) vs. pipeline parallelism (split by depth, simpler comms); demo of the 1-trillion-param Kimi 2.6 across four Macs.
- **15:53 Distributed fine-tuning** — Data-parallel training: replicate the model, process different batches in parallel, average gradients; fine-tuning Qwen 3.5 (9B) at >3x throughput vs. a single M3 Ultra.
- **18:34 CLI, Python, Swift, and C++ APIs** — Fine-grained APIs: init a distributed group, shard with tensor parallelism, use low-level `all_reduce`/`all_sum` primitives, and JACCL standalone for non-ML workloads.
- **20:45 Next steps** — Recap of the full stack; companion session on local agentic AI, docs on custom parallelism strategies, and the built-in MLX LM distributed server.

## Code

See `code.md` — 9 snippets extracted from the Code tab (hostfile JSON, `mlx.distributed_config`, distributed `mlx_lm.chat`/`mlx_lm.lora`, tensor/pipeline parallelism, Python `sharded_load` + `shard_linear`, cross-language `all_sum`, and standalone JACCL C++).
