# Code samples — Session 319

Extracted from the Code tab of the session page. Labels derived per snippet; timecodes
map to the transcript chapters.

## On-device model — the 3-line baseline

```swift
import FoundationModels

let session = LanguageModelSession()
let response = try await session.respond(to: "Summarize this article: \(article)")
```

## Switch to PCC — change one line

```swift
import FoundationModels

let session = LanguageModelSession(
    model: PrivateCloudComputeLanguageModel()
)
let response = try await session.respond(to: "Summarize this article: \(article)")
```

## Same unified API — Generable + Tools on PCC

```swift
import FoundationModels

@Generable
struct ArticleSummary {
    let oneLineSummary: String
    let keyPoints: [String]
}

struct FindRelatedArticlesTool: Tool {

}

let session = LanguageModelSession(
    model: PrivateCloudComputeLanguageModel(),
    tools: [FindRelatedArticlesTool.self]
)

let response = try await session.respond(
    to: "Summarize this article: \(article)",
    generating: ArticleSummary.self
)
```

## Check availability before using PCC

```swift
import FoundationModels

struct ArticleSummarizationView: View {
    private var model = PrivateCloudComputeLanguageModel()

    var body: some View {
        if model.isAvailable {
            // Show UI for making request
        } else {
            // Fall back
        }
    }
}
```

## Set the reasoning level

```swift
let response = try await session.respond(
    to: prompt,
    contextOptions: ContextOptions(reasoningLevel: .light)
)
// Reasoning levels: .light, .moderate, .deep
```

## Read the context size

```swift
SystemLanguageModel().contextSize
// 4096 on 26.0
// 8192 on 27.0 (newer devices)

PrivateCloudComputeLanguageModel().contextSize
// 32768
```

## Handle usage limits in the UI

```swift
struct ArticleSummarizationView: View {
    private var model = PrivateCloudComputeLanguageModel()

    var body: some View {
        if case .belowLimit(let info) = model.quotaUsage.status {
            if info.isApproachingLimit {
                Text("Nearing usage limit.")
                    .foregroundStyle(Color.orange)
            }
        }
        if model.quotaUsage.isLimitReached {
            Text("Usage limit exceeded.")
                .foregroundStyle(Color.red)
        }
        if let suggestion = model.quotaUsage.limitIncreaseSuggestion {
            Button("Show options") {
                suggestion.show()
            }
        }
    }
}
```

---

## Useful API facts surfaced by the code

- One-line model swap: pass `model: PrivateCloudComputeLanguageModel()` to `LanguageModelSession(...)`; everything downstream (Generable, Tools) is unchanged.
- `contextSize` lives on both `SystemLanguageModel` and `PrivateCloudComputeLanguageModel`. On-device: 4096 (26.0) / 8192 (27.0, newer devices). PCC: 32768.
- `reasoningLevel` enum: `.light`, `.moderate`, `.deep` — passed via `ContextOptions(reasoningLevel:)` to `respond`.
- `model.isAvailable` gates the UI; PCC is Apple-Intelligence-only.
- Quota: `model.quotaUsage` exposes `.status` (`.belowLimit(info)` with `info.isApproachingLimit`), `.isLimitReached`, and `.limitIncreaseSuggestion` (call `.show()` to surface the upgrade flow).
