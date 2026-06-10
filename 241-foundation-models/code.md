# Code samples — Session 241

Extracted from the Code tab via Playwright. Timestamps map to the transcript.

## 2:46 — Context size and token counting

```swift
let model = SystemLanguageModel()
print(model.contextSize)
// 8192

let count = try await model.tokenCount(for: "What are the Japanese characters for origami?")
print(count)
```

## 3:39 — Image attachments (Vision)

```swift
// Attachable image types
let response = try await session.respond {
    "What animal is this?"
    Attachment(UIImage(...))
}
```

## 8:51 — Inspecting usage (tokens / cache / reasoning)

```swift
let response = try await session.respond(
    to: "Recommend a craft that doesn't require scissors.",
    contextOptions: ContextOptions(reasoningLevel: .light)
)

print(response.usage.input.totalTokenCount)
print(response.usage.input.cachedTokenCount)

print(response.usage.output.totalTokenCount)
print(response.usage.output.reasoningTokenCount)
```

## 11:51 — Manual routing between modes (the "boilerplate" approach)

```swift
@Observable
final class AppStates {
    var mode: Mode
}

let appStates: AppStates
var session: LanguageModelSession?

func updateSession() {
    let originalTranscript = session?.transcript.dropFirstInstructions() ?? Transcript()

    // Create a new session with new instructions and tools
    switch appStates.mode {
    case .craftAnalysis:
        session = LanguageModelSession(
            tools: [
                RecordImageAnalysisTool(),
                SwitchModeTool(states: appStates)
            ],
            instructions: "Analyze the user's craft project...",
            transcript: originalTranscript
        )
    case .brainstorm:
        session = LanguageModelSession(
            tools: [
                RecordBrainstormTool(),
            ],
            instructions: "Brainstorm some ideas...",
            transcript: originalTranscript
        )
    }
}

struct SwitchModeTool: Tool {
    let description = "Switch to a different mode."
    let states: AppStates

    @Generable
    struct Arguments {
        let mode: Mode
    }

    func call(arguments: Arguments) async throws -> some PromptRepresentable {
        appStates.mode = arguments.mode
        return "Successfully switched to \(arguments.mode)."
    }
}

// If mode changes, update the session
withObservationTracking {
    appStates.mode
} onChange: {
    updateSession()
}
```

## 12:39 — Simplest DynamicProfile

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

let session = LanguageModelSession(
    profile: CraftProfile()
)
```

## 14:31 — DynamicProfile branching with model + reasoning modifiers

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
            .model(states.privateCloudCompute)
            .reasoningLevel(.deep)
        }
    }
}
```

## 18:25 — Foundation Models SDK for Python

```python
import apple_fm_sdk as fm

model = fm.SystemLanguageModel()

# Check the model's availability
is_available, reason = model.is_available()

if is_available:

    # Create a session
    session = fm.LanguageModelSession(model=model)

    # Generate a response
    response = await session.respond(prompt="Hello!")
    print(response)
```

---

## Useful API facts surfaced by the code

- On-device `SystemLanguageModel().contextSize` == **8192**
- `reasoningLevel` has at least `.light` and `.deep`
- Usage tree: `response.usage.input.{totalTokenCount, cachedTokenCount}` and `response.usage.output.{totalTokenCount, reasoningTokenCount}`
- Manual mode switching uses `session.transcript.dropFirstInstructions()` to carry history forward
- `DynamicProfile` modifiers: `.model(_)`, `.reasoningLevel(_)`
