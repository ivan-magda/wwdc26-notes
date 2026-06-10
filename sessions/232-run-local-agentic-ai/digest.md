---
title: "Run local agentic AI on the Mac using MLX — Full Digest"
session: WWDC26 · 232
url: https://developer.apple.com/videos/play/wwdc2026/232/
duration: 13m
speakers: Angelos (MLX team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 232 — Run local agentic AI on the Mac using MLX

## TL;DR

You can run the *entire* agentic loop — model inference and tool execution — locally on a Mac with MLX. No cloud, no API keys, no usage costs, and your data never leaves the machine. Four themes:

1. **A four-layer open-source stack.** MLX (array framework for Apple silicon) → MLX-LM (load / quantize / fine-tune) → MLX-LM Server (OpenAI-compatible HTTP server with tool calling + reasoning) → any agent that speaks the OpenAI chat-completions protocol (Xcode, OpenCode, Pi agent, custom scripts).
2. **Three-step setup.** `pip install mlx-lm`, start `mlx_lm.server` with a tool-calling model, point the agent's base URL at `localhost`. The agent neither knows nor cares the model is local.
3. **Hardware makes it fast.** M5 **Neural Accelerators** give ~4x faster matmul (and thus prompt processing) vs M4; **continuous batching** serves concurrent subagents; **distributed inference** shards huge models across Macs over Thunderbolt (RDMA on macOS 26.2 → up to 3x with 4 nodes).
4. **Real coding demos, fully local.** Summarizing GitHub PRs via OpenCode, building a SwiftUI iPad drawing app from scratch, and fixing a bug from inside Xcode's Intelligence settings.

Everything shown is open-source and available now.

---

## 1. Chat vs. the agentic loop

Plain chat: you send a prompt, the model replies, and acting on that reply — running a command, checking a file, fixing an error — is on you.

An agent closes that loop. The agent asks the model what to do, calls **tools** to actually do it (run commands, read files, hit APIs), observes the results, and goes back to the model for the next step:

> User → agent → model → tools → observe → repeat, until the task is done.

The pitch: on Apple silicon the whole loop can run **locally** — data stays on the machine, AI is available offline anytime, and there are no usage costs.

The opening demo runs an agent on a Mac with MLX on the left and the **OpenCode** agent on the right. It fetches recent PRs from the MLX repo, summarizes the diffs, and flags what needs attention — the model runs on local hardware and only the `git`/GitHub-CLI calls touch the network.

## 2. The local agentic AI stack (four layers)

From the bottom up:

- **MLX** — open-source array framework purpose-built for Apple silicon. Handles low-level computation, Metal acceleration, memory management. The foundation.
- **MLX-LM** — the language-model layer: load, run, quantize, and fine-tune LLMs. Thousands of HuggingFace models, plus CLI tools and a Python API. (This was last year's deep-dive material.)
- **MLX-LM Server** — a persistent, **OpenAI-compatible HTTP server** exposing your local model through a standard API. Supports structured **tool calling** and **reasoning** models. A drop-in replacement for any cloud LLM API.
- **The agent** — anything that speaks the OpenAI chat-completions protocol: Xcode, OpenCode, Pi agent, a custom script. Because the server is standard, any agent framework works out of the box.

It's not only Apple building here: **Ollama, LM Studio, and vLLM** build on MLX / MLX-LM. If you use one of those, you're probably already running on MLX.

## 3. Three-step setup

```bash
# Step 1: Install MLX-LM
pip install mlx-lm

# Step 2: Start the server (use a tool-calling model; start small to test)
mlx_lm.server --model mlx-community/Qwen-3.5-4B-8bit

# Step 3: Point your agent to the server
curl -X POST \
  http://127.0.0.1:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"default_model","messages":[{"role":"user","content":"Hello!"}]}'
```

The server loads the model and accepts requests on localhost. In most agent frameworks you just set the **base URL** to the local server's address and you're done.

Concrete example — the **OpenCode** config defines a local provider, sets the URL to localhost and the expected model name, and tells OpenCode to use the local model for everything:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "mlx/default_model",
  "small_model": "mlx/default_model",
  "provider": {
    "mlx": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "MLX (local)",
      "options": {
        "baseURL": "http://127.0.0.1:8080/v1"
      },
      "models": {
        "default_model": {
          "name": "Default MLX Model"
        }
      }
    }
  }
}
```

## 4. Making agents fast — three challenges

### Prompt processing

In an agentic workflow, every time the model receives tool output it must process all that new context before reasoning about the next step — over and over through the loop. Agentic sessions often run to **hundreds of thousands of tokens**, most of which are *processed*, not *generated*.

The **M5 chip's dedicated Neural Accelerators** make matrix multiplication **4x faster on M5 vs M4**. With MLX's specialized multiplication and attention kernels this translates almost directly into ~4x faster prompt processing, so agents read a codebase or process tool results nearly 4x faster. No special arguments or code changes — MLX picks the best kernel for the hardware automatically.

### Concurrency

Agents rarely work alone: a common pattern is one agent spawning several **subagents** in parallel (one reading docs, one searching code, one writing tests). That means concurrent requests to the local model.

MLX-LM Server handles this with **continuous batching** — it dynamically groups incoming requests into batches and processes them together on the GPU, and **new requests can join an in-progress batch** without waiting for the current one to finish. Subagents don't stall in a queue; the whole workflow keeps moving.

### Model size

Sometimes one machine isn't enough — even 512 GB of RAM. The most recent **DeepSeek** model has ~**1.6 trillion parameters** and needs **>800 GB** just for weights.

MLX's **distributed** support spreads a model across multiple Macs over **Thunderbolt or Ethernet**. Two wins for agents: run larger, more capable models that wouldn't fit on one machine; and parallelize prompt processing across devices, directly speeding the loop.

```bash
mlx.launch --hostfile hosts.json \
  --backend jaccl \
  /remote/path/to/mlx_lm.server \
  --model mlx-community/Qwen-3.5-122B-A3B-8bit
```

You launch with `mlx.launch` and a **hostfile** (nodes + connection type); the model is **auto-sharded** across devices. Starting with **macOS 26.2**, **Thunderbolt RDMA** provides low-latency, high-bandwidth communication over Thunderbolt — distributed inference has seen **up to 3x with four nodes**. Deep-dive: *"Explore distributed inference and training with MLX."*

## 5. Demos — building and fixing SwiftUI apps locally

**Build from scratch (OpenCode + MLX).** Starting from a blank Xcode project, the agent is asked to build an iPad drawing app. It inspects the directory structure, makes a plan, writes the code, builds the app, and fixes errors along the way — no manual copying or building. First working version in ~2 minutes. A follow-up request ("use rounded end caps") has the agent edit and recompile until it builds cleanly. The model ran through MLX-LM Server on that Mac; the agent used standard tools like `xcodebuild` to verify its work.

**Fix a bug (Xcode locally-hosted provider).** Same project, opened in Xcode. Connect Xcode to the already-running MLX server via **Settings → Intelligence → Add Chat Provider → Locally Hosted**, set the **Port** to 8080 (or whatever the server uses). With a deliberately introduced bug, the model identifies it within seconds, inspects the surrounding code, and writes a fix to build and run. A locally running agent integrating with the normal Xcode workflow — reading project files, understanding build errors, making targeted fixes — and the code never leaves the Mac.

## 6. Next steps

Install MLX-LM, launch the server, point your favorite agent at it. The full local stack — MLX → agent — with Neural Accelerators, continuous batching, and distributed inference making it fast. All open-source and available now.
