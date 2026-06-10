# Session 232 — Run local agentic AI on the Mac using MLX

- **URL:** https://developer.apple.com/videos/play/wwdc2026/232/
- **Duration:** 13m
- **Speakers:** Angelos (engineer, MLX team)

## Description

How to build and run agentic AI workflows entirely on a Mac using MLX — no cloud,
no API keys, just local hardware. Covers the four-layer local stack (MLX, MLX-LM,
MLX-LM Server, the agent), a three-step setup, and how MLX makes agents fast through
M5 Neural Accelerators, continuous batching, and distributed inference. Ends with live
demos building and fixing SwiftUI apps on-device via OpenCode and Xcode.

## Key topics

- The agentic loop (user → agent → model → tools → observe → repeat) running fully locally
- Four-layer local stack: **MLX** (array framework) → **MLX-LM** (load/quantize/fine-tune) → **MLX-LM Server** (OpenAI-compatible HTTP server, tool calling, reasoning) → **agent** (Xcode, OpenCode, Pi agent, custom scripts)
- Ecosystem already on MLX/MLX-LM: **Ollama, LM Studio, vLLM**
- Three-step setup: `pip install mlx-lm` → `mlx_lm.server --model ...` → point agent at `localhost`
- Making agents fast — three challenges:
  - **Prompt processing** — M5 Neural Accelerators make matmul 4x faster vs M4 → ~4x prompt-processing speedup, no code changes
  - **Concurrency** — continuous batching serves multiple subagent requests on the GPU; new requests join in-flight batches
  - **Model size** — distributed inference shards a model across Macs over Thunderbolt/Ethernet; Thunderbolt RDMA (macOS 26.2) gives up to 3x with 4 nodes
- Live demos: PR summary via OpenCode + GitHub CLI; SwiftUI drawing app from scratch; Xcode locally-hosted chat provider fixing a bug
- Everything shown is open-source and available now

## Related sessions to fetch (referenced in this talk)

- [ ] Explore distributed inference and training with MLX

## Chapter summary (Summary tab)

- **0:00 Introduction** — building and running agentic AI entirely on Mac with MLX; no cloud, no API keys.
- **0:32 The chat and agentic loop** — chat (prompt → response, you act) vs the agentic loop (model decides, calls tools, observes, iterates); runs locally for privacy, offline, and zero usage cost.
- **2:42 Local agentic AI stack** — four layers: MLX, MLX-LM, MLX-LM Server (OpenAI-compatible), and the agent; popular tools Ollama, LM Studio, vLLM build on MLX.
- **4:36 Setting up your own agent** — three steps: `pip install mlx-lm`, start the server with a tool-calling model, configure the agent to the local endpoint.
- **5:39 Making agents fast** — prompt processing over large contexts; M5 Neural Accelerators speed prompt processing ~4x.
- **6:53 Concurrency and distributed inference** — continuous batching for concurrent multi-agent requests; distributed inference across Macs over Thunderbolt.
- **9:20 More examples** — live SwiftUI demos: OpenCode + MLX generates a full project from a description; Xcode agentic coding builds and fixes a SwiftUI app, all local.
- **13:01 Next steps** — recap of the stack and the install/launch/connect steps; all tools open-source and available now.

## Code

See `code.md` — 3 snippets from the Code tab (setup curl, OpenCode config, distributed launch).
