---
title: "What's New in the Foundation Models Framework — Full Digest"
session: WWDC26 · 241
url: https://developer.apple.com/videos/play/wwdc2026/241/
duration: 21m
speakers: Erik Hornberger, Zhen Li
sources: transcript.md, code.md, meta.md, screenshots/
compiled: 2026-06-10
---

# WWDC26 · 241 — What's New in the Foundation Models Framework

## TL;DR

Year two of the Foundation Models framework. Five headline themes:

1. **Open source** — the core framework *and* several new APIs are open-sourced, plus a separate fast-moving `Foundation Models framework utilities` package. Runs anywhere Swift runs, including Linux.
2. **More models** — a rebuilt on-device model (now with **vision**), a new **`PrivateCloudComputeLanguageModel`** (32K context + reasoning), and a **model abstraction layer** (`LanguageModel` protocol) that lets local, open-source, and third-party server models (Anthropic, Google) back the same session API.
3. **System tools** — built-in `BarcodeReaderTool`, `OCRTool` (Vision), and a Spotlight-backed search tool for fully **local RAG**.
4. **Agentic primitives** — **Dynamic Profiles**, a declarative way to swap instructions / tools / model / reasoning level within a single session while preserving history.
5. **Tooling** — a new **Evaluations** Swift framework (Xcode-integrated), the **`fm` CLI** (macOS 27), and a **Python SDK**.

The recurring demo throughout is a "Crafts" journaling app that analyzes origami photos and then brainstorms project ideas.

---

## 1. New on-device model

- Rebuilt from the ground up; better at **logic and tool calling**.
- **Context-size & token-counting APIs** (shipped in **iOS 26.4**) — inspect the model's context window and count tokens in instructions, prompts, and transcripts so you can adapt to the hardware. `[shot: 15.42.41]` badge "26.4".
- **Guardrails** refined to cut false positives (already in 26.4, more in iOS 27).

```swift
let model = SystemLanguageModel()
print(model.contextSize)
// 4096   ← slide shows 4096; Code tab text said 8192 (see Open Questions)

let count = try await model.tokenCount(for: "What are the Japanese characters for origami?")
print(count)
// 9
```

## 2. Vision on-device

The on-device model now accepts **image attachments** in the prompt builder — "a natural extension of the existing prompt builders." `[shot: 15.46.46]`

```swift
// Attachable image types
let response = try await session.respond {
    "What animal is this?"
    Attachment(UIImage(...))
}
```

- Accepted types: **`UIImage`, `NSImage`, `CGImage`, Core Image (`CIImage`), `CVPixelBuffer`, and file URLs.**
- Any size / aspect ratio — no cropping or padding required. Larger images = more tokens + latency.
- Live demo `[shot: 15.43.09]`: prompt *"In as few words as possible, what is in this image?"* over a jpeg attachment → **"Pink origami crane"**, Input ≈ 323 tokens, Response 9 tokens.
- Deep-dive: **"What's new in image understanding."**

## 3. Private Cloud Compute model

`PrivateCloudComputeLanguageModel` — the same server model that powers Apple Intelligence. `[shot: 15.47.14]`

- **32,000-token** context window.
- **Reasoning** capability (model thinks before answering).
- No account setup, no auth, and no API keys to manage. Prompts are never stored; independently verifiable.
- Brings Foundation Models to **watchOS 27**.
- **Pricing:** no cloud API cost for developers with **< 2M first-time downloads**. End users get a daily PCC allowance; **iCloud+** subscribers get a higher limit.
- Needs an **entitlement** — see **"Building with Private Cloud Compute."**

```swift
let session = LanguageModelSession(
    model: PrivateCloudComputeLanguageModel()
)

let response = try await session.respond(
    to: "How many folds are in a paper crane?",
    contextOptions: ContextOptions(reasoningLevel: .deep)
)
```

- `reasoningLevel` is set via the new **`contextOptions`** argument. Known levels: **`.light`** and **`.deep`**. Deeper = better answers, more compute.
- Demo `[shot: 15.47.37]`: the `.deep` answer reasons through the origami process and concludes ≈ 20 folds. **Input 46 tokens · Response 1,238 tokens · 9.6 s** — a concrete illustration that reasoning is token- and latency-heavy.

## 4. Model abstraction layer

A new **`LanguageModel` protocol** lets any model back a `LanguageModelSession`. `[shot: 15.48.35]` `[shot: 15.50.15]`

```
        LanguageModelSession
                │  (backed by)
            LanguageModel  ← protocol
          ┌─────┴───────────────────────┐
SystemLanguageModel   PrivateCloudComputeLanguageModel   YourLanguageModel
```

- `SystemLanguageModel` and `PrivateCloudComputeLanguageModel` already conform.
- Apple is **open-sourcing two more implementations**:
  - **`CoreAILanguageModel`** — runs local models on the Apple Neural Engine.
  - **`MLXLanguageModel`** — runs local models on the Mac GPU.
- CoreAI demo `[shot: 15.49.46]` exposes `engine`, `tokenizer`, `modelIdentifier` (**`qwen3_0_6b_4bit`** — Qwen3 0.6B, 4-bit), and `samplingConfig`; prompt *"Tell me about the history of origami."*

### Third-party / frontier server models

- **Anthropic** and **Google** publish **Swift packages** for their latest models.
- Swap models via Swift Package Manager — **everything downstream stays the same.**

```swift
let session = LanguageModelSession(model: someThirdPartyModel)
```

- You handle **auth + billing**: never store keys in the binary; fetch tokens via **OAuth**, store in **Keychain**.
- **Usage tracking** — sessions and responses expose a `usage` property `[shot: 15.50.05]`:

```swift
print(response.usage.input.totalTokenCount)
print(response.usage.input.cachedTokenCount)     // how many input tokens were cache hits
print(response.usage.output.totalTokenCount)
print(response.usage.output.reasoningTokenCount) // how many output tokens were reasoning
```

- Deep-dive: **"Bring an LLM provider to the Foundation Models framework"** (authoring your own `LanguageModel` package).

## 5. System tools

Built-in tools that plug into a `LanguageModelSession`:

- **`BarcodeReaderTool`** — read barcodes. `[shot: 15.50.36]`
- **`OCRTool`** — extract structured text from images.
  - Both Vision-backed → deep-dive **"What's new in image understanding."**
- **Spotlight search tool** for fully **local Retrieval-Augmented Generation (RAG)** — a top-requested feature. `[shot: 15.50.48]`

RAG flow shown on the slide:

```
Prompt: "How many folds does it take to make a paper crane?"
  → ToolCall  SpotlightSearchTool   {"query": "origami crane", "limit": 5}   → Spotlight index
  ← ToolCall  [Craft]               [{"title": "basic paper crane", …}, …]    ← results
Response: "Somewhere between 15 and 20 depending on which pattern you choose to follow."
```

- Deep-dive: **"LLM search using Core Spotlight."**

## 6. Dynamic Profiles (agentic primitive)

The problem: building agentic flows by hand means spinning up multiple sessions and writing a lot of orchestration boilerplate, especially if the model should switch modes autonomously `[shot: 15.54.11]`.

**Dynamic Profiles** are a **declarative** API: conform a struct to `LanguageModelSession.DynamicProfile`, expose a `body` that resolves to a single active `Profile` (instructions + tools), and initialize the session with it. The framework handles transitions.

### Simplest form `[shot: 15.53.41]`

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    var body: some DynamicProfile {
        Profile {
            Instructions {
                """
                You are an expert crafting assistant. \
                Record craft project image analyses   \
                using the recordImageAnalysis tool.
                """
            }
            RecordImageAnalysisTool()
        }
    }
}

let session = LanguageModelSession(profile: CraftProfile())
```

### Branching on app state `[shot: 15.53.56]`

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    let states: CraftProjectStates

    var body: some DynamicProfile {
        switch states.mode {
        case .craftAnalysis:
            Profile {
                Instructions { /* ... */ }
                RecordImageAnalysisTool()
                SwitchModeTool(states: states)
            }
        case .brainstorm:
            Profile {
                Instructions { /* ... */ }
                BrainstormRecordTool()
            }
            .model(states.privateCloudCompute)   // modifier: pick the model
            .reasoningLevel(.deep)                // modifier: pick reasoning depth
        }
    }
}
```

- Modifiers **`.model(_)`** and **`.reasoningLevel(_)`** vary model + config per branch while **preserving conversation history**. `[shot: 15.54.49]`
- Key mental model: a `DynamicProfile` **resolves to exactly one active `Profile` at a time**; conditionals choose which.
- In the Crafts app: quick **craft analysis** uses `SystemLanguageModel`; **brainstorm** switches to PCC + deep reasoning, carrying the full analysis context forward.

### The manual alternative (what Dynamic Profiles replace)

Before this API you'd swap sessions yourself, carrying history with `transcript.dropFirstInstructions()` and triggering rebuilds via `withObservationTracking`:

```swift
@Observable final class AppStates { var mode: Mode }

func updateSession() {
    let originalTranscript = session?.transcript.dropFirstInstructions() ?? Transcript()
    switch appStates.mode {
    case .craftAnalysis:
        session = LanguageModelSession(
            tools: [RecordImageAnalysisTool(), SwitchModeTool(states: appStates)],
            instructions: "Analyze the user's craft project...",
            transcript: originalTranscript)
    case .brainstorm:
        session = LanguageModelSession(
            tools: [RecordBrainstormTool()],
            instructions: "Brainstorm some ideas...",
            transcript: originalTranscript)
    }
}

struct SwitchModeTool: Tool {
    let description = "Switch to a different mode."
    let states: AppStates
    @Generable struct Arguments { let mode: Mode }
    func call(arguments: Arguments) async throws -> some PromptRepresentable {
        appStates.mode = arguments.mode
        return "Successfully switched to \(arguments.mode)."
    }
}

withObservationTracking { appStates.mode } onChange: { updateSession() }
```

### Things to weigh when using profiles `[shot: 15.55.01]`

The "Explore context management" slide lists **four** considerations (note the transcript only said three — it omits KV Cache):

- **Privacy** (boundaries between on-device and cloud)
- **Cost**
- **Capability** (model strength)
- **KV Cache**

- Deep-dive: **"Build agentic app experiences with Foundation Models framework."**

## 7. Evaluations framework

A new Swift framework to measure the quality of intelligence features so you can quantify accuracy as you tweak prompts and understand the **statistical impact** of changes. `[shot: 15.55.24]`

- **Xcode-integrated** — the demo ("BookTracker" / `BookTaggingEvaluation`) runs like a test target: Insights, Coverage, Tests, Evaluations, Log, Build in the navigator.
- Surfaces per-criterion **Pass/Fail** (Tag Count, Tag Total, Word Count, Has Genre Tag) and aggregate metrics like **Tag Quality average 3.07**, with a Compare view across runs.
- Deep-dive: dedicated Evaluations sessions.

## 8. `fm` CLI (macOS 27)

Foundation Models in the terminal for everyday productivity. `[shot: 15.55.48]`

```
USAGE
  % fm <command> [options]

COMMANDS
  respond       Generate a response to a prompt
  chat          Start an interactive chat session
  token-count   Count tokens in a prompt or instructions
  schema        Generate a JSON generation schema
  serve         Start a Chat Completions API server
  available     Check model availability
  quota-usage   Check model quota usage

MODELS
  system   On-device Apple Foundation Model (default)
  pcc      Apple Foundation Model on Private Cloud Compute

EXAMPLES
  % fm respond 'What is Swift?'
  % fm respond --model pcc --stream 'Summarize this article'
  % fm chat --instructions 'You are a coding assistant'
  % fm token-count 'Hello world'
  % fm schema object --name Person --string name --int age
```

- `fm chat` for interactive use; pipe `fm` into shell scripts to summarize/extract/generate (demo: generating a descriptive filename for `IMG_1234` from its image content).
- `fm serve` exposing a **Chat Completions API server** is notable for local tooling integration.

## 9. Python SDK

Same on-device model as the Swift framework, for the Python ecosystem. `[shot: 15.56.18]`

```python
import apple_fm_sdk as fm

model = fm.SystemLanguageModel()

is_available, reason = model.is_available()   # tuple: (bool, reason)

if is_available:
    session = fm.LanguageModelSession(model=model)
    response = await session.respond(prompt="Hello!")
    print(response)
```

- Deep-dive: **"Build AI-powered scripts with the fm CLI and Python SDK."**

## 10. Open source & the utilities package

- The **core framework** is open-sourced → great general-purpose LLM client "everywhere Swift runs," **including Linux servers**.
- New **`Foundation Models framework utilities`** package — updated *between* OS releases for emerging/experimental building blocks. `[shot: 15.56.33]`
  - **Transcript management** (profile modifiers)
  - **Skill API** for procedural knowledge loading
  - A **Chat Completions** standard language model (talk to OpenAI-style servers)
- Together with Anthropic/Google packages + CoreAI/MLX: "run any model, anywhere."
