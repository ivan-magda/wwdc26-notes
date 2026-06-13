# Session 8011 — Apple Intelligence Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8011/
- **Duration:** 01:03:49 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + engineering panel answering submitted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so attributions below are inferred from self-introductions and host routing.

## Panel

Names and roles are taken from the opening self-introductions only. whisper has no diarization, so first names later in the transcript are attributed by who the host hands off to, and surnames are not reliable.

- **Ernie Sveala** — host; AI/machine learning evangelist at Apple. (Surname is whisper's best guess; spelling unverified.)
- **Matt** — works on the Foundation Models framework.
- **James** — works on App Intents.
- **Rob / Robert** — works on the Evaluations framework.
- **Louis** — works on the Foundation Models framework.
- **Dan** — works on Siri and App Intents.

The host notes that a larger group of engineers is answering questions off-stage in parallel.

## Description

A live, on-demand Apple Intelligence group lab. After quick introductions, the host reads submitted developer questions (attributed to forum-style usernames) and routes them to the panel. The bulk of the hour is App Intents and the new App Schemas for the redesigned "Siri AI," plus the Foundation Models framework (on-device model, the new Private Cloud Compute server model, the language model protocol, dynamic profiles), the Evaluations framework, Image Playground, and how all of these compose. Recurring throughline: adopt **App Schemas** to let Apple do the heavy lifting (sample phrases, Spotlight keys, localization, safety confirmations) and use **App Shortcuts / system.search** to fill the gaps. The panel repeatedly points to the recorded sessions and asks developers to file Feedback for enhancement requests.

## Key topics

- **App Schemas** — pick and choose Apple-designed, fine-tuned schemas that match your app's capabilities; required to integrate with the new Siri AI; mix and match across domains; combine with custom App Intents for anything outside a schema.
- **system.search and system.open** — generic schemas in the "system" domain for e-commerce/food-ordering search deep links and for opening donated Spotlight entities.
- **App Shortcuts** — the older phrase-based API; still the fallback when no schema fits; works on HomePod where the new Siri AI does not.
- **System orchestrator** — new this year; takes actions from App Intents across many apps. No third-party-to-third-party direct invocation; cross-app data sharing goes through the new **Transferable** API (bidirectional import/export, progressive fidelity).
- **Indexed Entity vs schema** — schema defines the shape of content; Indexed Entity puts it in the semantic index so Siri AI can retrieve it. Retrieval is sandboxed to the donor app.
- **Semantic index / intent donation** — new donation system builds a temporary transcript so Siri learns which app you use for which contact.
- **Foundation Models framework** — on-device model (4K context, no rate limit in foreground, throttling + retriable rate-limit error in background) and the new PCC server model (32K context, quota signals + errors). Image input on both; "vision" capability via the language model protocol; reasoning levels (light/moderate/deep) on the PCC model.
- **Language model protocol + custom segments** — bring any model (including web models via Core ML) into Foundation Models; extend to custom modalities like video. Framework is open source this year.
- **Dynamic profiles** — switch profiles within one transcript (agent-like), with declarative history transforms; Foundation Models utilities package for summarizing/compacting transcripts.
- **Evaluations framework** — model-judge evaluators, safety rubrics, tool-call evaluator, synthetic data, hill climbing, Xcode/Instruments integration, JSON reports. Works on any stochastic system, not just LLMs.
- **Image Playground** — `imagePlaygroundStyle.all` for photorealistic generation; now runs on PCC, so it requires an internet connection and does not fall back to on-device.
- **Guardrail violations** — use a model-judge safety rubric (optionally the larger PCC model) to diagnose unexpected guardrail errors.
- **On-screen context** — one of the three pillars of the new Siri AI; NSUserActivity integration plus new ViewAnnotations API. Not extended to CarPlay (safety/road-focus rationale).
- **Platform availability** — new Siri AI on iPhone, iPad, Mac, visionOS; not HomePod. Encouragement to test across devices including AirPods, using full vs supporting dialogue modes.
- **AppIntentsTesting** — new framework to unit/UI/Spotlight test App Intents in CI.

## Related sessions (referenced on-air — matched to workspace by title/topic)

The lab never reads session numbers aloud. These are inferred from the panel's topic and author references and matched to the workspace.

- [x] 240 — Build intelligent Siri experiences with App Schemas (the App Schemas / Siri AI throughline)
- [x] 241 — What's new in the Foundation Models framework (language model protocol, image input, PCC model, open source)
- [x] 242 — Build agentic app experiences with the Foundation Models framework (dynamic profiles, agents)
- [x] 246 — LLM search using Core Spotlight (semantic index / indexed entity)
- [x] 295 — Validate your App Intents adoption with AppIntentsTesting (the "Venkatesh" testing talk; "spacesuit" demo)
- [x] 298 — Meet the Evaluations framework (intro)
- [x] 299 — Create robust evaluations for agentic apps (advanced; tool-call evaluator, hill climbing, synthetic data)
- [x] 319 — Build with the new Apple Foundation Model on Private Cloud Compute (server model, reasoning, one-line connect)
- [x] 339 — Bring an LLM provider to the Foundation Models framework (Rob's "bring any model from the web," Core ML)
- [x] 343 — Explore advanced App Intents features for Siri and Apple Intelligence (the "Antonio" talk; intent dialogue, entity ownership, confirmations)
- [x] 345 — Discover new capabilities in the App Intents framework (donation, schemas, system domain)
- [x] 237 — What's new in image understanding (the "image understanding with the Vision framework" plug, for video→text tooling)
- [ ] Image Playground session — referenced ("great video this year") but no exact title given; not matched to a workspace session.

## Chapter arc (no published chapters; rough timestamps)

- 00:00 — Welcome and panel introductions
- 01:36 — Q&A begins (learning resources; App Schemas vs custom intents)
- 07:19 — Mapping non-schema entities into agentic Siri; cross-app orchestration and Transferable
- 13:31 — Foundation Models background-work budget, throttling, and error types
- 16:16 — Guardrail violations and the Evaluations framework
- 17:43 — Multimodal input (images, video via custom segments), language model protocol, open source
- 21:49 — App schemas vs Siri "advantage"; intent donation and consistent cross-app experiences
- 26:05 — Image Playground photorealism on PCC
- 26:59 — Indexed Entity vs schema; Spotlight donation
- 30:04 — Evaluations for academic research
- 33:14 — On-screen context, ViewAnnotations, and CarPlay
- 35:42 — App Intents across HomePod, watchOS, AirPods; full vs supporting dialogue
- 38:25 — Image Playground internet requirement
- 39:03 — "What are you most excited about?" round (the book toss)
- 45:11 — Why hard-coded schemas instead of a dynamic Markdown/skills approach
- 49:10 — Evaluations tool-call visibility; dynamic profiles
- 51:08 — Semantic entropy / context size between on-device and PCC models
- 53:00 — Do you need Evaluations with Foundation Models?
- 53:48 — Cross-app retrieval sandboxing; Transferable between same-developer apps
- 55:28 — Schema-bound entity interchange (file vs photo) via Transferable
- 57:34 — Mapping an imperfect schema match (contacts → messages person)
- 59:57 — Token budgets with image input (about 200 tokens/image; 4K on-device, 32K PCC); hill climbing
- 01:02:16 — Wrap-up and thanks

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API and feature names are captured in `digest.md`.
