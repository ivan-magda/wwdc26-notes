# Session 102 — Platforms State of the Union

- **URL:** https://developer.apple.com/videos/play/wwdc2026/102/
- **Duration:** 61m
- **Speakers (named/handed-off on stage):** Josh (host), Richard & Mary Beth (Foundation Models / Origami demo), Lori (App Intents / Siri), Cindy (Liquid Glass design), Franck (SwiftUI), Holly (Swift), Ken (Xcode experience), Kevin (Xcode agentic coding).
- **Format:** Keynote-style. No Summary tab, no Code tab — chapters below are derived from the transcript.

## Description

Apple's annual developer keynote walking through the year's headline platform changes
across three pillars: Apple Intelligence (Foundation Models framework gaining image
input and server models, App Intents/Siri integration, the new Core AI on-device
framework), platform improvements (Liquid Glass design refinements, iOS app
resizability, SwiftUI speed/interaction/document updates, Swift 6.4), and developer
productivity (agentic coding in Xcode 27, Device Hub, themes, Xcode Cloud, game tools).
Framed around an "Origami" sample app used throughout the demos.

## Key topics

- **Foundation Models framework** — multimodal prompts (text + images); Vision-backed OCR and barcode tools; server models (Claude, Gemini, more) via a `LanguageModel` protocol; single API across on-device / Private Cloud Compute / third-party
- **Private Cloud Compute access** — developers with < 2M first-time App Store downloads get Apple Foundation Models on PCC with **no cloud API cost**; iCloud+ subscribers get expanded end-user access
- **Dynamic Profiles** — new declarative Foundation Models API; one active `Profile` at a time over a shared continuous transcript; swap model / instructions / tools / reasoning level per turn; recomputes every model turn
- **Open-source Swift package** for Foundation Models — pre-built tools, skills, context-management utilities; framework itself goes open source later this summer (run same Swift APIs on your server)
- **App Intents + Siri** — entity/intent **schemas**, Spotlight semantic index, `IndexedEntity`, `@AppEntity` / `@AppIntent` macros, `sendMessage` schema, new **View Annotations API** for on-screen reference ("this photo", "the second message")
- **Core AI** — brand-new on-device model framework; memory-safe Swift API, custom GPU kernels, PyTorch conversion tools, AOT compilation, tensor visual debugger back to Python source; scales iPhone vision model → multi-billion-param Mac LLM; powers Siri
- **MLX** — open source, now Metal 4, GPU Neural Accelerators, multi-Mac training via RDMA over Thunderbolt
- **Liquid Glass design** — diffusion/readability tuning, darkened edge + brighter specular highlights, clear→tinted personalization slider, sidebar/edge updates, `Icon Composer` layered glass + refraction; old design support being removed in Xcode 27
- **iOS app resizability** — apps auto-opt-in on rebuild; resizable simulator + Previews; coding-agent skill to fix resizability issues; iPad / iPhone Mirroring
- **SwiftUI** — `.reorderable()` / `.reorderContainer()`, `.swipeActions()` / `.swipeActionsContainer()` in any container, richer text selection, lazy `@State` (now a macro), `AsyncImage` HTTP caching, `visibilityPriority` / toolbar overflow / `topBarPinnedTrailing`, prominent tab role, new document infrastructure, Spatial Preview framework for Vision Pro
- **Swift 6.4** — `anyAppleOS` availability shorthand, scoped warning suppression / warnings-as-errors, async in `defer`, fewer "unable to type-check in reasonable time" failures; Swift in kernel/WebKit/QUIC/TrueType
- **Apple silicon only** — Intel Mac support ended with Tahoe; ship Apple-silicon-only Mac App Store binaries
- **Xcode 27** — agentic coding (`/plan`, simulator interaction, localization, crash-fixing via Organizer), MCP, **Agent Client Protocol** (any compatible agent), Anthropic/OpenAI/Google built-in agents, plugins/skills, Device Hub (replaces Simulator), iCloud settings sync, themes, zero-setup new project, faster builds, Xcode Cloud
- **Game tools** — Reality Composer Pro 3 (RealityKit), Game Porting Toolkit with AI skills, Metal command-line tools for agents

## Related sessions to fetch (referenced or implied in this talk)

- [ ] What's new in the Foundation Models framework (session 241 — already in this workspace)
- [ ] Bring a server / LLM provider to the Foundation Models framework
- [ ] Building / getting started with Private Cloud Compute
- [ ] Foundation Models: image input, OCR & barcode tools (image understanding)
- [ ] Local RAG with the Core Spotlight tool
- [ ] Dynamic Profiles / building agentic experiences with Foundation Models
- [ ] Evaluations framework for intelligence features
- [ ] The `fm` command-line tool + Python SDK
- [ ] Meet Core AI (on-device model framework)
- [ ] What's new in MLX (Metal 4, multi-Mac training)
- [ ] App Intents schemas + View Annotations API for Siri
- [ ] What's new in SwiftUI (resizability, toolbars, document API)
- [ ] SwiftUI document infrastructure deep dive
- [ ] Spatial Preview framework for Apple Vision Pro
- [ ] What's new in Swift (6.4)
- [ ] Liquid Glass design updates / Icon Composer
- [ ] What's new in Xcode 27 (Device Hub, agentic coding, plugins/ACP)
- [ ] Reality Composer Pro 3 / RealityKit
- [ ] Game Porting Toolkit + Metal command-line tools

## Chapter summary (derived — no Summary tab)

- **0:19 Opening** — Josh welcomes; year's two big themes recap: the new design with Liquid Glass and Apple Intelligence. Agenda: (1) Apple Intelligence, (2) platform improvements, (3) developer productivity.
- **2:23 Apple Intelligence overview** — Apple Foundation Models built with Google's Gemini technologies, adapted for on-device + PCC; Foundation Models framework gains image input and server models; < 2M-download devs get PCC at no API cost; App Intents connects apps to system intelligence (Spotlight semantic index, app toolbox, system orchestrator).
- **4:46 Foundation Models framework (Richard & Mary Beth)** — Origami sample app tour; multimodal prompts; Vision tools (OCR, barcode); server models (Claude, Gemini) via `LanguageModel` protocol; PCC at no cloud cost; open-source Swift package of pre-built tools/skills/utilities.
- **9:00 Dynamic Profiles** — declarative API; one active Profile at a time over a shared transcript; swap model/tools/instructions/reasoning per turn; body recomputes each model turn; profiles as adaptable agent-like building blocks.
- **11:52 Tooling + open source** — Evaluations framework, upgraded Foundation Models instrument, `fm` CLI, Python SDK, image tool calling, Core Spotlight RAG tool; framework goes open source later this summer (run same Swift APIs on server).
- **13:08 Core AI** — brand-new on-device model framework; memory-safe Swift API, GPU kernels, PyTorch conversion, AOT compilation, tensor debugger; scales from iPhone vision model to multi-billion-param Mac LLM; powers Siri.
- **14:38 App Intents & Siri (Lori)** — entity/intent schemas, Spotlight indexing, `IndexedEntity`, `@AppEntity` / `@AppIntent`, `sendMessage` schema, live Siri demo over Origami chat; new View Annotations API for on-screen reference.
- **19:21 Intelligent platform recap (Josh)** — Siri + Foundation Models + Core AI together; MLX (open source, Metal 4, GPU Neural Accelerators, multi-Mac RDMA-over-Thunderbolt training).
- **20:53 Platform improvements intro** — rebuild on new SDK → faster launches; refinements across media, search, accessibility, Swift, SwiftUI, and especially design.
- **22:26 Liquid Glass design (Cindy)** — readability diffusion tuning, darkened edge + specular highlights, clear→tinted slider, sidebar/edge/toolbar updates, colored sidebar icons, tighter macOS corner radius, Icon Composer layered glass + refraction.
- **24:59 App adaptability** — iOS app resizability in iPhone Mirroring + iPad; auto opt-in on rebuild; resizable simulator/Previews; coding-agent skill for resizability fixes.
- **27:05 SwiftUI (Franck)** — interactions (`.reorderable()`, `.swipeActions()` in any container, richer text selection); speed (unified AppKit/UIKit/SwiftUI foundation, ~2× faster layout, lazy `@State` macro, `AsyncImage` HTTP caching); capabilities (toolbar `visibilityPriority`/overflow/`topBarPinnedTrailing`, prominent tab role, new document infrastructure, Spatial Preview framework).
- **35:13 Swift (Holly)** — Swift across the stack (Linux/Windows/Android/web, server); Swift-Java/WebAssembly interop case studies; Swift in WebKit, QUIC, TrueType, kernel; Swift 6.4 (`anyAppleOS`, scoped warnings, async `defer`, better type-check diagnostics).
- **40:28 Transitions (Josh)** — Apple-silicon-only Mac binaries; removing old-design opt-out; Xcode 27 rebuild → automatic Liquid Glass.
- **41:29 Developer productivity intro** — AI coding in Xcode; faster release cadence; coding agents, MCP, Figma/GitHub integration; Anthropic/OpenAI/Google agents; ACP + Gemini shipping in an Xcode 26 update today.
- **42:48 Xcode experience (Ken)** — faster project loading, fixed crashes, 30% smaller Apple-silicon-only Xcode 27; iCloud settings sync; zero-setup new project; toolbar customization; themes; Xcode Cloud (2× faster, Vision Pro + Metal); Previews variants; Device Hub (replaces Simulator, drives physical devices).
- **49:08 Xcode agentic coding (Kevin)** — agents woven into every layer, grounded in Swift/SwiftUI/frameworks; `/plan` flow with diagram; implement → validate (tests, playgrounds, Previews, simulator interaction) → improve (localization, crash fixing via Organizer); plugins/skills as "specialists", ACP brings any agent.
- **57:52 Other tools & games (Josh)** — Reality Composer Pro 3 (RealityKit, character animation, Mac Virtual Display previews); Game Porting Toolkit AI skills; Metal command-line tools for agents.
- **58:46 Wrap** — recap of the three pillars; 100+ sessions; Developer app / website / YouTube / Bilibili; Group Labs, forums, Developer Centers (Cupertino, Shanghai, Singapore, Bengaluru, new Berlin this fall).

## Code

See `code.md` — keynote-style session with no Code tab; only spoken API names captured.
