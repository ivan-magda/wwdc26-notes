# Session 241 — What's new in the Foundation Models framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/241/
- **Duration:** 21m
- **Speakers:** Erik, Zhen
- **Audio:** English. Subtitles: English, Chinese, Chinese (Taiwan), French, Japanese, Korean, Portuguese (Brazil), Spanish (Latin America), Vietnamese.

## Description

Explore what's new in the Foundation Models framework. Learn how to access Private
Cloud Compute, integrate third-party and open source models, and work with vision
capabilities. Discover context management APIs, built-in semantic search, and
powerful primitives for creating agentic experiences in your apps.

## Key topics

- New on-device model (rebuilt; better logic + tool calling); context-size / token-count APIs (iOS 26.4); guardrail false-positive reductions
- Vision capabilities on-device — image attachments in prompt builders (UIImage, NSImage, CGImage, Core Image, CVPixelBuffer, file URLs)
- `PrivateCloudComputeLanguageModel` — 32k context, reasoning, `reasoningLevel` via `contextOptions`; no API keys; watchOS 27; free under 2M downloads
- Model abstraction layer — new `LanguageModel` protocol; `CoreAILanguageModel`, `MLXLanguageModel`; Anthropic + Google Swift packages; `usage` property (tokens, cache reads, reasoning tokens)
- System tools — `BarcodeReaderTool`, `OCRTool` (Vision); Spotlight-backed search tool for local RAG
- `DynamicProfile` — declarative agentic API; profile/model/reasoningLevel modifiers; single active Profile at a time
- Evaluations framework (new Swift framework)
- `fm` CLI (macOS 27) + Python SDK
- Open source: core framework + Foundation Models framework utilities package

## Related sessions to fetch (referenced in this talk)

- [ ] Building with Private Cloud Compute
- [ ] Bring an LLM provider to the Foundation Models framework
- [ ] What's new in image understanding
- [ ] LLM search using Core Spotlight
- [ ] Build agentic app experiences with Foundation Models framework
- [ ] Evaluations framework (sessions)
- [ ] Build AI-powered scripts with the fm CLI and Python SDK

## Chapter summary (Summary tab)

- **0:00 Introduction** — Erik Hornberger & Zhen Li; this year goes open source with a new utilities package. Agenda: models, system tools, dynamic profiles, evaluations, tooling.
- **2:34 New on-device model** — rebuilt; better reasoning + tool calling; iOS 26.4 context-size/token-count APIs; refined guardrails (fewer false positives).
- **3:21 Vision** — on-device model gains vision; image attachments accept UIImage/NSImage/CGImage/Core Image/CVPixelBuffer/file URLs, any size (larger = more tokens).
- **4:20 Private Cloud Compute** — `PrivateCloudComputeLanguageModel`, 32K context, reasoning levels, no account/auth/keys, private, on watchOS 27.
- **6:46 Model abstraction layer** — `LanguageModel` protocol backs `LanguageModelSession`; open-source `CoreAILanguageModel` + `MLXLanguageModel` (ANE/GPU).
- **7:32 Partner models** — Anthropic & Google Swift packages; swap via SPM, downstream unchanged; OAuth + Keychain; per-token usage (cache + reasoning tokens).
- **9:40 System tools** — `BarcodeReaderTool`, `OCRTool` (Vision); Spotlight-powered search tool for local RAG.
- **10:57 Dynamic Profiles** — declarative agentic primitive; Crafts app swaps instructions/tools between modes via `DynamicProfile`.
- **13:46 Composing models/configs** — modifiers vary model + reasoning per branch; SystemLanguageModel for quick, PCC + deep reasoning for brainstorm; history preserved; one active profile at a time.
- **15:30 Evaluations framework** — new Swift framework; quantify accuracy as prompts change.
- **16:02 `fm` CLI** — macOS 27; on-device + PCC in terminal; `fm chat`, pipe into shell scripts.
- **17:13 Python SDK** — same on-device model; check availability + structured responses in a few lines.
- **17:55 Open source + utilities** — utilities package (transcript management, skill API, chat-completions); core framework open-sourced, runs anywhere Swift runs incl. Linux.
- **19:24 Next steps** — sample app; dynamic profiles + Evaluations; deep-dive sessions.

## Code

See `code.md` — 7 snippets extracted from the Code tab via Playwright.
