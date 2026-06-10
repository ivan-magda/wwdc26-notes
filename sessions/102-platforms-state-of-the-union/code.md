# Code — Session 102

No Code tab for this session (keynote-style). No code blocks were shown on slides in
extractable form; the items below are API/symbol names spoken in the transcript.

## Spoken APIs

**Foundation Models framework**
- `LanguageModelSession` — the existing session type Dynamic Profiles attach to
- `LanguageModel` protocol — any model provider's Swift package conforms to it
- `SystemLanguageModel` — on-device model (used for the small "explain the jargon" task)
- Private Cloud Compute language model — server-run Apple Foundation Model (used for brainstorm + tutorial profiles)
- Dynamic Profiles / `Profile` — declarative result-builder body that resolves to one active profile; modifiers shown verbally: pick the PCC model, `temperature` (cranked up for creativity), `reasoningLevel` set to `deep`
- OCR tool / barcode reader tool (Vision-backed)
- Core Spotlight RAG tool (private to the app)
- Foundation Models instrument (Instruments)
- `fm` command-line tool ("FM command line tool")
- Python SDK

**Core AI**
- Core AI framework (on-device model runtime; PyTorch conversion + optimization tools, AOT compilation, Core AI instruments, tensor visual debugger)

**MLX**
- MLX array framework (Metal 4, GPU Neural Accelerators, RDMA over Thunderbolt)

**App Intents / Siri**
- App Intents framework
- `IndexedEntity` protocol
- `@AppEntity` macro
- `@AppIntent` macro
- entity schemas / intent schemas; `sendMessage` schema
- Spotlight semantic index
- View Annotations API (view modifier mapping rows → e.g. `MessageEntity`)

**SwiftUI**
- `.reorderable()` (on `ForEach`) + `.reorderContainer()` (on parent)
- `.swipeActions()` (on row) + `.swipeActionsContainer()` (on scrollable container)
- `@State` (now backed by a macro; lazy initialization)
- `AsyncImage` (automatic HTTP caching)
- `visibilityPriority` toolbar modifier; toolbar overflow menu container; `topBarPinnedTrailing` placement
- prominent tab role
- new document infrastructure (first-class file URL access, observable configuration)
- Spatial Preview framework (Apple Vision Pro)

**Swift 6.4**
- `anyAppleOS` availability shorthand
- scoped warning suppression / promote-warnings-to-errors
- `async` calls allowed inside `defer`

**SwiftUI macros/values referenced**
- `show borders` environment value (now on macOS 27, like iOS)
- scroll edge effect APIs

> Note: no full snippets were captured — these are names only. For concrete code see
> the deep-dive sessions in `meta.md` (e.g. session 241 already in this workspace).
