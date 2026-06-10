---
title: "Bring an LLM provider to the Foundation Models framework — Full Digest"
session: WWDC26 · 339
url: https://developer.apple.com/videos/play/wwdc2026/339/
duration: 21m
speakers: Christopher Webb
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 339 — Bring an LLM provider to the Foundation Models framework

## TL;DR

Session 241 told *app developers* that the Foundation Models framework now swaps in
nearly any LLM. This session is the other half: it tells *model providers* how to be
one of those swappable models. The promise is that whichever model an app uses —
Apple's System model, Private Cloud Compute, Core AI, MLX, a community package, or a
frontier model from Anthropic (Claude) or Google (Gemini) — it's called the same way,
because every model conforms to the `LanguageModel` protocol.

Four steps to bring a model into the framework:

1. **Package it** as a Swift package (SPM), targeting iOS/macOS/visionOS/watchOS — and
   Linux, since the framework itself is now open source. Minimize dependencies; publish
   with a git tag.
2. **Implement the protocol** — two types: `LanguageModel` (declares capabilities,
   hands over a `Configuration`) and `LanguageModelExecutor` (does the work: prewarm,
   translate transcripts to your engine's native format, stream events back).
3. **Handle authentication** for server-based models — steer developers away from raw
   API-key strings toward token providers / sign-in, persist tokens in Keychain, use
   App Attest for device attestation.
4. **Customize** — response metadata, custom segments for new modalities (audio/video),
   and server-side tools (web search, code execution, image generation).

The architectural heart of it: a session keeps an **executor store keyed by the model's
`Configuration`** (which is `Hashable`), so identical configs reuse the same executor —
that's what lets stateful integrations preserve a KV cache or persistent connection
across calls.

---

## 1. Introduction — one protocol, many models

The framework previously gave access only to Apple's on-device language model. Now it
opens to nearly any LLM, local or server-based, so anyone from large companies to solo
developers can build a model integration on top of it.

The lineup that makes the protocol worth having:

- **System Language Model** — rebuilt from the ground up: smarter, better at
  instruction following, accepts images directly in prompts.
- **Private Cloud Compute** — the model behind many Apple Intelligence features, now
  with reasoning, a 32K-token context window, and the usual privacy guarantees.
- **Core AI** — run local models efficiently on the Apple Neural Engine (ANE).
- **MLX** — unlocks the thousands of models in the MLX-Community on Hugging Face.
- **Anthropic and Google** will soon extend the framework with Swift packages of their
  own, making Claude and Gemini available to all Swift developers.

Because they all conform to the same `LanguageModel` protocol, you call them the same
way and get all the framework features (e.g. Dynamic Profiles) for free.

```swift
import FoundationModels
import MLXFoundationModels

// On-device Apple Foundation Model
let model = SystemLanguageModel()

// Private Cloud Compute model
// let model = PrivateCloudComputeLanguageModel()

// Custom Core AI model
// let model = try await CoreAILanguageModel(resourcesAt: modelURL)

// Open-source MLX model from HuggingFace
// let model = MLXLanguageModel(modelID: "mlx-community/my-model")

let session = LanguageModelSession(model: model)
let response = try await session.respond(to: "...")
print(response.content)
```

The rest of the talk is the provider's view: how to build a `LanguageModel` package of
your own, in four steps.

## 2. Packaging

Ship as a **Swift Package** so developers add it as a dependency.

- **Platforms** — Foundation Models supports iOS, macOS, visionOS, and watchOS;
  providers should aim for the same spread. Because the framework is being released as
  **open source**, the package is also useful to developers running Swift on servers,
  so consider supporting **Linux** too.
- **Dependencies** — every dependency is bytes a developer ships to users. Be
  deliberate about what your package links.
- **Publishing** — as simple as a **git tag**. SPM is decentralized, so your repo URL
  is your distribution channel; developers paste the URL into Xcode and start
  integrating.

```swift
// Package.swift

let package = Package(
    name: "MyModel",
    platforms: [
        .macOS(.v27), .iOS(.v27), .visionOS(.v27), .watchOS(.v27)
    ],
    products: [
        .library(name: "MyModel", targets: ["MyModel"])
    ],
    dependencies: [
        .package(url: "...", .upToNextMinor(from: "1.0.0"))
    ],
    targets: [
        .target(name: "MyModelRuntime"),
        // public: LanguageModel conformance
        .target(name: "MyModel", dependencies: ["MyModelRuntime"]),
        .testTarget(name: "MyModelTests", dependencies: ["MyModel"])
    ]
)
```

(Pointer: "Creating Swift Packages".)

## 3. The protocol — the two key types

The protocol is the bridge between your model and the framework. Two pieces:

- **`LanguageModel`** — *describes* the model. Declares what it can do via
  `capabilities`, and provides the `Configuration` the framework needs to construct the
  executor. Trivial to construct.
- **`LanguageModelExecutor`** — *does* the work: an initializer that takes a
  `Configuration`, a `prewarm` for ahead-of-time resource setup, and a `respond` that
  streams generation back into the session.

```swift
public protocol LanguageModel: Sendable {
    var capabilities: LanguageModelCapabilities { get }
    var executorConfiguration: Executor.Configuration { get }
}

public protocol LanguageModelExecutor: Sendable {
    init(configuration: Configuration) throws
    func prewarm(model: Model, transcript: Transcript)
    func respond(
        to request: LanguageModelExecutorGenerationRequest,
        model: Model,
        streamingInto channel: LanguageModelExecutorGenerationChannel
    ) async throws
}
```

### Configuration is the lookup key

The `Configuration` links the two types: the model provides it, the framework uses it
to construct the executor. Each session holds an **executor store**:

- A model arrives → framework checks the store by the model's `Configuration`. No match
  → create a new executor and store it.
- A second model produces the **same** configuration → because `Configuration` is
  `Hashable`, the framework resolves to the **same** executor. *The configuration is the
  lookup key, not the model.*
- A model with a **different** configuration → its own executor.

Each unique configuration maps to exactly one executor. When the session deallocates,
the store goes with it: every executor is released, your `deinit` runs, weights are
freed and connections closed — all automatically. You write none of that teardown.

```swift
public struct MyLanguageModel: LanguageModel {
    typealias Executor = MyLanguageModelExecutor

    public var capabilities: LanguageModelCapabilities {
        LanguageModelCapabilities(capabilities: [
            .toolCalling, .guidedGeneration, .reasoning
        ])
    }

    public var executorConfiguration: Executor.Configuration {
        Executor.Configuration(/* ... */)
    }
}

public struct MyLanguageModelExecutor: LanguageModelExecutor {
    public typealias Model = MyLanguageModel
    public struct Configuration: Hashable, Sendable { /* ... */ }
    public init(configuration: Configuration) throws { /* ... */ }
    public func respond(
        to request: LanguageModelExecutorGenerationRequest,
        model: MyLanguageModel,
        streamingInto channel: LanguageModelExecutorGenerationChannel
    ) async throws { /* ... */ }
}
```

### prewarm

Before a request arrives, the developer can ask the framework to prewarm — your chance
to do expensive setup ahead of time (loading weights, opening connections). The
recommended pattern is a private helper that loads once and caches; `prewarm` calls it
eagerly. But **prewarm isn't guaranteed to run**, so the helper must also be safe to
call lazily inside `respond` — either way, weights load exactly once. For a
server-backed model with no expensive setup, prewarm can be a no-op.

```swift
struct MyLanguageModelExecutor: LanguageModelExecutor {
    private mutating func loadModelIfNeeded() throws -> LoadedWeights {
        let weights = try loadedModel ?? loadWeights()
        loadedModel = weights
        return weights
    }
    func prewarm(transcript: Transcript) {
        loadedModel = try? loadModelIfNeeded()
    }
    func respond( ... ) async throws {
        let weights = try loadModelIfNeeded()
        // ...generate with 'weights'...
    }
}
```

### Translating transcripts (in)

The session is the developer's entire interaction surface — your executor and the rest
of your package live behind it, out of sight. Behind the scenes, the framework hands
you **transcript entries**, but your inference engine only understands its native
types, so your executor sits in the middle and translates each way.

A transcript is the conversation so far, as a sequence of entries. Foundation Models
defines **six entry types**: instructions (developer), prompt (user), tool calls, tool
outputs, response, and reasoning. Your model defines its own roles; the executor maps
between them. In a typical mapping, instructions/prompt/response → system/user/assistant,
and tool calls, tool outputs, and reasoning *also* map to assistant (they're part of the
model's turn) — unless your model has a dedicated tool role, in which case route there.

```swift
let transcript = Transcript(entries: [
    .instructions( ... ),  // "You are a helpful assistant"
    .prompt( ... ),        // "What's the weather in Pittsburgh?"
    .toolCalls( ... ),     // getWeather(location: "Pittsburgh")
    .toolOutput( ... ),    // 65°F, sunny
    .response( ... ),      // "It's 65°F and sunny in Pittsburgh"
    .prompt( ... ),        // "What's the address of Apple Park?"
    .response( ... ),      // "One Apple Park Way, Cupertino, CA 95014"
])
```

Every request also carries the developer's *intent*, in two properties:

- **`ContextOptions`** — what goes into the prompt: reasoning level, response schema.
- **`GenerationOptions`** — the decoder loop: sampling strategy, temperature, maximum
  response length.

```swift
func respond( ... ) async throws {
    let reasoningLevel = request.contextOptions.reasoningLevel
    let temperature = request.generationOptions.temperature
    let maxTokens = request.generationOptions.maximumResponseTokens
}
```

### Streaming the response (out) — order matters

On the way out, everything goes as **events on the channel**: text the engine
generates, tool calls/reasoning, and metadata. Each chunk (a token, a tool-call
fragment) becomes an event — `textDelta`, `toolCallDelta`, etc. — which the framework
writes to the transcript. The one-shot and streaming APIs share one implementation:
it's always streaming, and the one-shot API just collects deltas internally.

There's a **deliberate handshake order** so the developer isn't blocked:

1. **Metadata** — model and request IDs for logging/debugging.
2. **Usage** — prompt token counts, sent up front so the developer learns the request's
   cost without waiting for the whole stream.
3. **Text deltas** — one per token, the moment it arrives, so users see the response
   appear word-by-word.

```swift
func respond( ... ) async throws {
    // 1. Report metadata
    await channel.send(.response(action: .updateMetadata([
        "modelID": "my-model-2026-06-08",
        "requestID": request.id.uuidString
    ])))
    // 2. Report prompt token usage before generating
    await channel.send(.response(action: .updateUsage(
        input: .init(totalTokenCount: promptTokens, cachedTokenCount: cachedTokens),
        output: .init(totalTokenCount: 0, reasoningTokenCount: 0)
    )))
    // 3. Stream text deltas
    for try await token in tokens {
        await channel.send(.response(action: .appendText(token)))
    }
}
```

### Preserving work across calls (KV cache)

Executors are cached by configuration; if your integration is **stateful** (a KV cache
or a persistent session), that caching is what lets you minimize network churn and
avoid redoing work. The executor receives the **full transcript on every `respond`
call**, so:

- Compare the new transcript to the one you saved last time.
- **Append-only** (a new prompt after the last response) → preserve existing state,
  process only what's new.
- **Removed or modified** entries (e.g. the developer trims old entries to save context)
  → **invalidate back to where the transcripts diverge**.

Your executor decides what counts as a match and how to handle changes.

### Approximate or throw

When your model can't do exactly what the developer asked, you have two choices:
**approximate** (be flexible, honor intent) or **throw**. When there's no honest
approximation — e.g. a token limit that can't coexist with a required-field schema —
throw. Foundation Models ships `LanguageModelError` for exactly these cases; any
developer who's used the framework already knows how to handle them.

```swift
// Honor intent where possible (greedy → temperature 0)
if request.generationOptions.sampling?.kind == .greedy {
    serviceRequest.temperature = 0
}

// Otherwise throw
if let schema = request.schema,
   let budget = request.generationOptions.maximumResponseTokens,
   budget < minimumTokens(for: schema) {
    throw LanguageModelError.unsupportedCapability(
        .init(
            capability: .guidedGeneration,
            debugDescription: "Token budget too small to satisfy this schema."
        )
    )
}
```

Built-in cases: `contextSizeExceeded`, `rateLimited`, `refusal`, `guardrailViolation`,
`unsupportedCapability`, `unsupportedTranscriptContent`, `unsupportedGenerationGuide`,
`unsupportedLanguageOrLocale`, `timeout`. Define your **own** error type only for
failures that make sense solely in your service (subscription tiers, account states) —
each custom case is one more thing developers must learn and catch, so reach for a
built-in when it fits.

```swift
public enum MyModelError: Error, LocalizedError {
    case exceededSubscriptionTierLimit
    case modelNotProvisioned
    case accountSuspended

    public var errorDescription: String? {
        switch self {
        case .exceededSubscriptionTierLimit:
            String(localized: "Your plan limit has been reached.")
        // ...
        }
    }
}
```

## 4. Authentication

Your job as a package author is to make it easy for developers to do the right thing.

- If your initializer takes an **API key as a plain string**, developers will take the
  path of least resistance. Instead, offer a **token provider or sign-in flow**.
- If your package fetches access tokens on developers' behalf, persist them securely in
  **Keychain**.
- Credential handling is half the story; **device attestation** is the other half. For
  a cloud-based package, use **App Attest** to verify the device, catch tampered builds,
  sign payloads, and use Apple's fraud signal to keep bad traffic off your service.
  (Pointer: "Secure your apps with App Attest".)

## 5. Customization

With packaging, protocol, and auth done, you have a solid package. Now differentiate
around the abilities only your model offers.

### Response metadata (lightweight)

Attach custom info to responses and give developers clear ways to read it. After
streaming completes, send things like `tokensPerSecond` and `timeToFirstToken` through
the channel. Underneath, metadata is just a dictionary of strings, numbers, and other
built-in types. Provide utilities/docs — clear keys, typed accessors — so developers can
work with it easily.

```swift
let elapsed = Date().timeIntervalSince(startTime)
let tokensPerSecond = Double(tokenCount) / elapsed
let timeToFirstToken = firstTokenTime?.timeIntervalSince(startTime) ?? 0

await channel.send(.metadataUpdate([
    "tokensPerSecond": tokensPerSecond,
    "timeToFirstToken": timeToFirstToken
]))
```

### Custom segments (new modalities)

When a dictionary isn't flexible enough — a new modality like audio or video — define a
**custom segment**. Because custom segments are `PromptRepresentable`, developers pass
them directly in prompts, just like text. Your executor receives them as `customSegment`
entries in the transcript, and emits results back through the same channel as custom
segment updates. The **segment ID** controls whether you're adding a new segment or
updating one you've already started streaming, giving full control over how results
stream into the app — and the developer never leaves `LanguageModelSession`.

```swift
public struct AudioSegment: Transcript.CustomSegment {
    public var id: String
    public var content: URL
}

let recording = AudioSegment(id: UUID().uuidString, content: URL(filePath: "/path/to/recording.m4a"))
let response = try await session.respond {
    "Where was Frank Lloyd Wright's original architecture school located?"
    recording
}

for try await event in stream {
    switch event {
    case .audioFileGenerated(let file):
        await channel.send(.response(action: .updateCustomSegment(
            AudioSegment(id: file.id, content: file.url)
        )))
    }
}
```

### Server-side tools, at three levels of visibility

Server-side tools are capabilities your model runs on its own — web search, code
execution, image generation. The model invokes them, the server runs them, and your
executor watches the results stream in. They're named, typed values on your model; the
developer constructs the model with the tools they want, and the executor receives them
through the model on every request. Using web search as the example, three visibility
levels:

1. **Private grounding** — run the tool, stream only the answer. The tool grounds the
   response but leaves no trace in the transcript.
2. **Metadata-enriched** — when a text delta carries metadata (e.g. a citation), forward
   both; the framework attaches the metadata to the text segment.
3. **Fully surfaced** — forward the tool's structured output as a **custom segment**
   alongside the text and metadata, giving apps everything the model produced along the
   way.

```swift
public struct MyLanguageModel: LanguageModel {
    public struct ServerTool: Sendable {
        public static let webSearch: ServerTool = ...
    }
    public init(serverTools: [ServerTool] = []) { }
}

let client = MyServerClient(serverTools: model.serverTools)
let response = try await client.send(prompt: .init(request))
for try await chunk in response {
    switch chunk {
    case .webSearch(let webSearch):
        await channel.send(.response(action: .updateCustomSegment(
            WebSearchSegment(url: webSearch.url, content: webSearch.html)
        )))
    case .textDelta(let textDelta):
        await channel.send(.response(action: .appendText(
            textDelta.text, tokenCount: textDelta.tokenCount
        )))
    }
}
```

## 6. Next steps — privacy and pointers

One closing note that cuts across the whole talk: whether you're **choosing** a package
or **shipping** one, make sure everyone in the chain understands the privacy
implications of the model behind it. On-device and cloud-based models have very
different privacy characteristics, and users deserve to know which they're getting.

Companion sessions:

- "Integrate On-Device AI Models into Your App Using Core AI" — bundling local models.
- "Build with the new Apple Foundation Model on Private Cloud Compute" — server-scale
  inference with Apple's privacy guarantees.
- "Build agentic app experiences with the Foundation Models framework" — dynamic
  profiles for multi-step, tool-using workflows on top of models like yours.

The stated hope: a thriving ecosystem of `LanguageModel` packages giving Swift
developers the freedom to choose the right model per app.
