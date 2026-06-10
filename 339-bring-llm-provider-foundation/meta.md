# Session 339 — Bring an LLM provider to the Foundation Models framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/339/
- **Duration:** 21m
- **Speakers:** Christopher Webb (engineer, Machine Learning Research)

## Description

The Foundation Models framework, previously the door to Apple's on-device language
model, now opens to nearly any LLM — local or server-based. This session is the
provider-facing companion to "What's new in the Foundation Models framework": it
shows model providers (companies and individual developers alike) how to author a
`LanguageModel` Swift package so their model can back the same `LanguageModelSession`
API every Foundation Models developer already uses. Four steps: package it, implement
the protocol (`LanguageModel` + `LanguageModelExecutor`), handle authentication for
server-based models, and customize with response metadata, custom segments (new
modalities), and server-side tools.

## Key topics

- The model lineup that motivates the protocol: rebuilt on-device System Language Model (smarter, image input), Private Cloud Compute (reasoning, 32K context), Core AI (local, ANE), MLX (Hugging Face MLX-Community models). Anthropic (Claude) and Google (Gemini) Swift packages coming.
- **Packaging** — Swift Package Manager; `Package.swift` platform targets (iOS, macOS, visionOS, watchOS, and Linux because the framework is open source); minimize dependencies (every dep = shipped bytes); publish via a git tag (repo URL is the distribution channel).
- **The two protocol types** — `LanguageModel` (declares `capabilities`, provides `executorConfiguration`) and `LanguageModelExecutor` (`init(configuration:)`, `prewarm`, `respond` streaming into a channel).
- **Executor store / Configuration as lookup key** — each session caches executors keyed by the model's `Configuration` (Hashable); identical config → same executor; teardown is automatic on session dealloc.
- **prewarm** — eager, optional, one-time weight loading; no-op for server-backed models.
- **Transcript translation** — six Foundation Models entry types (instructions, prompt, response, tool calls, tool outputs, reasoning) mapped to the model's native roles; `ContextOptions` (reasoning level, schema) vs `GenerationOptions` (sampling, temperature, max tokens).
- **Response handshake ordering** — metadata update (model/request IDs) → usage update (prompt token counts) → text deltas. One-shot API is streaming under the hood.
- **KV-cache reuse across calls** — diff the new transcript against the saved one; append-only → preserve state and process only what's new; removed/modified entries → invalidate back to the divergence point.
- **Approximate or throw** — honor developer intent where possible; otherwise throw a built-in `LanguageModelError` (contextSizeExceeded, rateLimited, refusal, guardrailViolation, unsupportedCapability, etc.); define custom errors only for service-specific failures.
- **Authentication** — guide developers toward token providers / sign-in over raw API-key strings; persist tokens in Keychain; App Attest for device attestation on cloud services.
- **Customization** — response metadata (`tokensPerSecond`, `timeToFirstToken`); custom segments for new modalities (audio/video) via `Transcript.CustomSegment` + `PromptRepresentable`; server-side tools (web search, code execution, image generation) at three visibility levels (private grounding, metadata-enriched, fully surfaced via custom segments).
- **Privacy** — on-device vs cloud models differ; everyone in the chain should know which they're getting.

## Related sessions to fetch

- [ ] What's new in the Foundation Models framework (session 241 — already in workspace)
- [ ] Secure your apps with App Attest
- [ ] Creating Swift Packages
- [ ] Integrate On-Device AI Models into Your App Using Core AI
- [ ] Build with the new Apple Foundation Model on Private Cloud Compute
- [ ] Build agentic app experiences with the Foundation Models framework

## Chapter summary (Summary tab)

- **0:00 Introduction** — Christopher Webb. The framework opens to nearly any LLM. Rebuilt on-device System Language Model; three new model options (PCC, Core AI, MLX); Anthropic + Google partner packages coming; code preview showing any model swapped into a `LanguageModelSession` via the same Swift API.
- **3:37 Packaging** — package your provider as a Swift package; `Package.swift` platform targets (iOS, macOS, visionOS, watchOS, Linux); be deliberate about dependencies (shipped bytes); publish a release via git tag that developers paste into Xcode.
- **4:48 Protocol** — the two core types bridging your model to the framework: `LanguageModel` (capabilities + Configuration) and `LanguageModelExecutor` (prewarm, transcript→native translation, ContextOptions/GenerationOptions, metadata-first streaming). Executor caching by configuration; KV-cache reuse across calls; approximate unsupported options or throw `LanguageModelError`.
- **14:50 Authentication** — credential handling best practices: initializers that steer toward secure usage over raw API-key strings; Keychain for token persistence; App Attest for device attestation.
- **15:51 Customization** — custom response metadata (`tokensPerSecond`, `timeToFirstToken`); custom segment types for new modalities (audio, video); server-side tools (web search, code execution, image generation) at three visibility levels.
- **19:47 Next steps** — privacy considerations for on-device vs cloud; pointers to companion sessions on Core AI integration, Private Cloud Compute, and building agentic app experiences.

## Code

See `code.md` — 15 snippets extracted from the Code tab.
