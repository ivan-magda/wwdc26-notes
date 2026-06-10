# Code samples — Session 232

Extracted from the Code tab of the session page. Three snippets.

## Three-step setup: install, serve, and call

```bash
# Step 1: Install MLX-LM
pip install mlx-lm

# Step 2: Start the server
mlx_lm.server --model mlx-community/Qwen-3.5-4B-8bit

# Step 3: Point your agent to the server
curl -X POST \
  http://127.0.0.1:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"default_model","messages":[{"role":"user","content":"Hello!"}]}'
```

## OpenCode config — local MLX provider

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

## Distributed inference — launch with a hostfile

```bash
mlx.launch --hostfile hosts.json \
  --backend jaccl \
  /remote/path/to/mlx_lm.server \
  --model mlx-community/Qwen-3.5-122B-A3B-8bit
```

---

## Useful facts surfaced by the code + transcript

- The server is **OpenAI chat-completions compatible** — endpoint `/v1/chat/completions`, default port **8080**, host `127.0.0.1`.
- OpenCode points at the local server via `@ai-sdk/openai-compatible` with `baseURL` set to the local `/v1` endpoint; `model` + `small_model` both target the local model so everything runs locally.
- Distributed launch uses `mlx.launch` with a `--hostfile` (nodes + connection type) and `--backend jaccl`; the model is auto-sharded across devices.
- The large distributed example targets a `122B-A3B` (MoE-style) Qwen model — too big for one machine.
- Xcode integrates via **Settings → Intelligence → Add Chat Provider → Locally Hosted**, set Port to the MLX server port (e.g. 8080) — no code, config-only.
