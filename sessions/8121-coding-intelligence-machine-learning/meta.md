# Session 8121 — Coding Intelligence, Machine Learning & AI Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8121/
- **Duration:** 01:02:21 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + engineering panel answering submitted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization.

## Panel

Names are from on-air self-introductions only. Roles as stated by each panelist.

- **Shashank** ("Shank") — host; evangelism team at Apple.
- **Kevin** — works on Xcode (agentic coding, coding intelligence).
- **Eric** — Foundation Models framework.
- **Steven** — evaluations framework team.
- **Raziel** — Core AI (and Core ML).
- **Angelos** — MLX team. (whisper renders the spoken name variously as "Angelus," "Angelo," "Andres"; treat as one MLX panelist; exact spelling unverified.)

A wider team of experts triaged questions off-camera. Note: whisper inserted several hallucinated speaker labels mid-transcript ("FRANCESC CAMPOY," "MARK MANDEL," "FILIP HRACEK," "FELIPE HOFFA"). These are transcription artifacts, **not** panelists, and lines tagged with them are attributed by routing/context or left unattributed below.

## Description

A live, on-demand group lab for the WWDC26 coding-intelligence / ML / AI track. After a warm-up round where each panelist names the one thing they're most excited about, the panel answers submitted developer questions spanning: how Core AI vs Core ML vs MLX vs Foundation Models fit together; the on-device model's context window; running Foundation Models in the background; bring-your-own-LLM via the language model protocol and Dynamic Profiles; teaching coding agents a codebase's style; local models in Xcode; UI testing with agents and the simulator; Foundation Models vs Vision/Translate framework boundaries; context-window management; guardrails and refusals; the team's philosophy of evaluation-driven development; and why models can't be shared across apps. Recurring throughline: **start with Foundation Models + the system on-device model, use the evaluations framework to prove whether it covers your use case, and only step down to Core AI / MLX or out to PCC / a third-party model when you have to.**

## Key topics

- **Framework map** — Foundation Models (top of stack) → Core AI (custom/downloaded models, SLAs) → MLX (research, distributed, on-device training). Core ML stays for "traditional ML" (decision trees etc.); everything new with neural networks moves to Core AI.
- **On-device context window** — 4096 tokens, shared input+output, unchanged from last year. PCC is 32K shared. Plug in MLX/Core AI/server packages to go up to ~1M.
- **Language model protocol** — plug any inference backend (MLX, Core AI, Google package, Anthropic package shipped that morning, your own) into Foundation Models. Open source; Foundation Models framework itself going open source "very soon."
- **Dynamic Profiles** — declarative API to route across system model / PCC / third-party model in one agentic flow. Two handoff patterns: **baton pass** (full shared context) and **phone a friend** (ephemeral, privacy-preserving tool-call style). Profile modifiers for stateless transcript transforms.
- **Background execution** — Foundation Models runs in background tasks; may hit rate-limit errors (system-busy vs too-many-requests, distinct error kinds). Foreground macOS local model is not rate limited.
- **Coding agents & codebase style** — search + learning; AGENTS.md / CLAUDE.md kept short with references to on-disk markdown; have the agent document crashes/assumptions as memory; Xcode searches Apple documentation so older models learn new APIs; "the rigging around the model" improved more than the models.
- **Local models in Xcode** — new **ACP (Agent Client Protocol)** support in Xcode 27 to plug local providers (LM Studio, Ollama) as full agents, beyond chat completions. Example used a Qwen 3 model.
- **Agentic UI testing** — agent drives the simulator (tap/swipe/type), reads back accessibility tree + screenshots, can run for hours hunting bugs, then writes durable UI tests. Test pyramid: many unit tests, fewer integration, a handful of UI.
- **Vision vs Foundation Models** — new Foundation Models tools powered by Vision (barcode reader, OCR). Use Vision for well-understood detection; step up to Foundation Models for semantic / natural-language / novel cases. Foundation Models "like a 3D printer," a baked specialized model "like a production line."
- **Context management APIs** — `response.usage` (input/output/cached/reasoning token counts; highly requested); token-counting + context-size symbols added in iOS 26.4; **foundation-models-utilities** open-source repo with a summarize-history modifier; drop spent tool calls; split work into separate sessions. KV-cache invalidation vs accuracy tradeoff → resolve with evaluations.
- **Guardrails & refusals** — `permissibleContentTransformations` (transcript-approx name) lets emotionally charged input through; refusal error (model's own alignment response) is distinct from a guardrail error (separate moderation model on in/out). Guardrails retrained this year, fewer false positives.
- **Evaluation philosophy** — evaluation as the *living specification* of a feature; evaluation-driven development; model-judge evaluator; synthetic data-set expansion; `compare` across configurations / dynamic profiles.
- **Model sharing across apps** — not supported on iPhone (sandboxing, security, quantization/use-case specificity). Core AI model caching can share in-memory resources within an app group; weights can be shared on disk only between apps from the same developer.

## Related sessions (referenced on-air, by title)

Session numbers were not spoken; these titles were named on-air. Numbers below are not asserted.

- "Building agentic experiences with Foundation Models" (Dynamic Profiles, baton pass / phone a friend; named repeatedly).
- "What's new in image understanding" (Vision; barcode/OCR/segmentation; named, with an on-air title correction from "what's new in Vision").
- An MLX session on connecting four Mac Studios to run a trillion-parameter model with local agentic loops.
- Core AI sessions on bringing PyTorch models into apps, PCC, and dynamic profiles.
- A session on building your own LLM provider / conforming to the language model protocol.
- An Apple Intelligence group lab "later this week" (panel deferred the Siri waitlist question to it).

## Chapter summary

No published chapters (group lab). Rough arc: intros + warm-up (00:00–03:28) → framework map (03:30) → context window & open source (07:40) → background execution (11:19) → Apple Intelligence waitlist / AFM (13:02) → bring-your-own provider, Dynamic Profiles, baton pass vs phone a friend (14:24) → speech personalization (20:05) → teaching agents your code style, local models, ACP (22:57) → agentic UI testing & simulator (32:30) → Vision vs Foundation Models vs Translate (35:38) → context-window management & evaluations (39:32) → guardrails/refusals (51:36) → evaluation philosophy (54:16) → model sharing across apps (57:20) → wrap-up (01:00:55).

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API/feature names are captured in `digest.md` (garbled ones flagged).
