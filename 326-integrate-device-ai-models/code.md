# Code samples — Session 326

Extracted from the session's Code tab. Timestamps map to the transcript.

## 11:01 — Load SAM 3 and run text-prompted segmentation

```swift
import CoreAIImageSegmenter

// Load
let segmenter = try await ImageSegmenter(resourcesAt: sam3ModelURL)

// Use
let response = try await segmenter.segment(image: inputImage, prompt: "flower")
let mask = response.segments.first?.mask
```

## 11:30 — Load Qwen and reuse the Foundation Models session API

```swift
import FoundationModels
import CoreAILanguageModels

// Create model instance
let model = try await CoreAILanguageModel(resourcesAt: qwen3ModelURL)

// Create session using the model
let session = LanguageModelSession(model: model)

// Generate response
let response = try await session.respond(to: "...")
```

## 12:29 — Guided generation with a @Generable vocab card

```swift
import FoundationModels
import CoreAILanguageModels

@Generable
struct VocabCard {
    let chineseWord: String
    let englishMeaning: String
    let exampleSentence: String
}

let model = try await CoreAILanguageModel(resourcesAt: modelURL)
let session = LanguageModelSession(model: model)
let response = try await session.respond(
    to: "Create a vocab card for flower",
    generating: VocabCard.self
)
let card: VocabCard = response.content
```

## 17:22 — Ahead-of-time compilation with coreai-build

```bash
$ xcrun coreai-build compile MyModel.aimodel --platform iOS
```

---

## Useful API facts surfaced by the code

- Segmentation: `ImageSegmenter(resourcesAt:)` loads from a local `.aimodel` URL; `segment(image:prompt:)` returns a `response` whose `segments` carry a `.mask`.
- Language model: `CoreAILanguageModel(resourcesAt:)` is a one-line load (asset, engine, tokenizer abstracted). It backs the standard `LanguageModelSession` from `FoundationModels`.
- Same Foundation Models ergonomics apply to a custom model: `session.respond(to:)`, streaming, and structured output via `generating: SomeGenerable.self`.
- `@Generable` typed output works identically with a custom Core AI model — `response.content` is the typed struct.
- AOT compilation: `xcrun coreai-build compile <model>.aimodel --platform <platform>` produces a compiled model that still specializes on-device but much faster.

> Note: the segmentation import is shown as both `CoreAIImageSegmenter` (Code tab) and
> `CoreAISegmentation` (the library name selected as the package product in the
> transcript). See Open Questions in `digest.md`.
