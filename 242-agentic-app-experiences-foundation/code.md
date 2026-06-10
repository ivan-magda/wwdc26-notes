# Code samples — Session 242

Extracted from the Code tab of the session page. Labels derived from each snippet's
content; ordering follows the talk.

## DynamicInstructions — composable instructions + tools

```swift
struct BrainstormFacilitator: DynamicInstructions {
    var orchestrator: CraftOrchestrator
    var body: some DynamicInstructions {
        Instructions {
            "You are a warm and friendly expert crafting brainstorm facilitator."
        }
        // Tools
        GenerateProjectTitle()
        // Conditionally include Origami knowledge
        if orchestrator.techniques.contains(.origami) {
            OrigamiExpert()
        }
    }
}
```

## DynamicProfile — switching agents per phase

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    var orchestrator: CraftOrchestrator
    var body: some DynamicProfile {
        switch orchestrator.mode {
        case .brainstorming:
            Profile { BrainstormFacilitator(orchestrator: orchestrator) }
                .model(orchestrator.pccLanguageModel)
                .temperature(1)
        case .planning:
            Profile { TutorialAuthor(orchestrator: orchestrator) }
                .model(orchestrator.pccLanguageModel)
                .reasoningLevel(.deep)
        case .reviewing:
            Profile { CraftCoach() }
                .model(orchestrator.systemLanguageModel)
        }
    }
}
```

## Initializing a session with a dynamic profile

```swift
let session = LanguageModelSession(profile: CraftProfile(orchestrator: orchestrator))
```

## Transcript management — historyTransform on a profile

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    var orchestrator: CraftOrchestrator
    var body: some DynamicProfile {
        switch orchestrator.mode {
        case .reviewing:
            Profile { CraftCoach() }
                .model(orchestrator.systemLanguageModel)
                .historyTransform { history in
                    // Update the history for your profile
                    guard let latestResponseIndex = lastResponseEntryIndex(history) else {
                        return history
                    }
                    let filteredHistory = history[0..<latestResponseIndex].filter { entry in
                        isToolCallsOrToolOutput(entry)
                    }
                    return filteredHistory + history[latestResponseIndex...]
                }
        }
    }
}
```

## Custom modifier — wrap a historyTransform for reuse

```swift
struct DroppingToolCallsProfileModifier: LanguageModelSession.DynamicProfileModifier {
    func body(content: Content) -> some DynamicProfile {
        content
            .historyTransform { history in
                guard let latestResponseIndex = lastResponseEntryIndex(history) else {
                    return history
                }
                let filteredHistory = history[0..<latestResponseIndex].filter { entry in
                    isToolCallsOrToolOutput(entry)
                }
                return filteredHistory + history[latestResponseIndex...]
            }
    }
}

extension LanguageModelSession.DynamicProfile {
    func droppingCompletedToolCalls() -> some DynamicProfile {
        self.modifier(DroppingToolCallsProfileModifier())
    }
}
```

## Ready-made history modifiers from the utilities package

```swift
import FoundationModelsUtilities

struct CraftProfile: LanguageModelSession.DynamicProfile {
    var orchestrator: CraftOrchestrator
    var body: some DynamicProfile {
        switch orchestrator.mode {
        case .reviewing:
            Profile { CraftCoach() }
                // Keep the most recent 10 entries
                // after dropping finished tool calls
                .rollingWindow(size: .entries(10))
                .droppingCompletedToolCalls()
        }
    }
}
```

## Lifecycle modifier — onResponse to trim history

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    @SessionProperty(\.history) var history
    var orchestrator: CraftOrchestrator
    var body: some DynamicProfile {
        switch orchestrator.mode {
        case .planning:
            Profile { TutorialAuthor(orchestrator: orchestrator) }
                .model(orchestrator.pccLanguageModel)
                .reasoningLevel(.deep)
                .onResponse {
                    // Update history
                    if history.count > 50, let responseIndex = lastResponseIndex(history) {
                        history = history[responseIndex...]
                    }
                }
        }
    }
}
```

## Session properties — declaring a custom property

```swift
extension SessionPropertyValues {
    @SessionPropertyEntry var summary: String?
}
```

## Session properties — summarize-and-reclaim-context on response

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    @SessionProperty(\.history) var history
    @SessionProperty(\.summary) var summary
    var orchestrator: CraftOrchestrator
    var body: some DynamicProfile {
        switch orchestrator.mode {
        case .planning:
            Profile {
                TutorialAuthor(orchestrator: orchestrator)
                if let summary {
                    Instructions { "Summary: \(summary)" }
                }
            }
            .onResponse {
                if history.count > 50, let responseIndex = lastResponse(history.prefix(40)) {
                    summary = try await summarize(history[0..<responseIndex])
                    history = history[responseIndex...]
                }
            }
        }
    }
}
```

## Orchestration — baton-pass (toggle the active profile via a tool)

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    var orchestrator: CraftOrchestrator
    var body: some DynamicProfile {
        switch orchestrator.mode {
        case .brainstorm:
            Profile {
                BrainstormInstructions()
                BatonPassTool()
            }
            .onToolCall { orchestrator.mode = .tutorial }
            .model(orchestrator.serverModel)
        case .tutorial:
            Profile {
                TutorialInstructions()
                BatonPassTool()
            }
            .onToolCall { orchestrator.mode = .brainstorm }
            .model(orchestrator.systemModel)
        }
    }
}
```

## Orchestration — phone-a-friend (spawn an isolated child session)

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    var body: some DynamicProfile {
        Profile {
            BrainstormInstructions()
            PhoneFriendTool(
                name: "generate_title",
                description: "Generate a creative project title",
                profile: TitleProfile()
            )
        }
    }
}

struct PhoneFriendTool<P: LanguageModelSession.DynamicProfile>: Tool {
    func call(arguments: GeneratedContent) async throws -> String {
        let session = LanguageModelSession(profile: profile())
        let response = try await session.respond(to: arguments)
        return response.content
    }
}
```

## The Skills pattern (utilities package)

```swift
struct CraftingSkills: LanguageModelSession.DynamicInstructions {
    var activations: SkillActivations
    var body: some DynamicInstructions {
        Skills(activations: activations) {
            Skill(
                name: "origami_folds",
                description: "Details about specific types of folds",
                prompt: """
                    Valley Fold: Paper is folded toward you, creating a V-shaped crease
                    Mountain Fold: Paper is folded away from you, creating an inverted V
                    ...
                    """
            )
            Skill(...)
            Skill(...)
        }
    }
}
```

## Tool calling mode — as a modifier or a generation option

```swift
public struct ToolCallingMode: Sendable {
    public static let allowed: ToolCallingMode
    public static let disallowed: ToolCallingMode
    public static let required: ToolCallingMode
}

// Pass tool calling mode as a profile modifier
struct OrigamiExpert: LanguageModelSession.DynamicProfile {
    var body: some LanguageModelSession.DynamicProfile {
        Profile {
            Instructions("You are an origami expert")
            QueryOrigamiDatabaseTool()
            ShowDirectionsTool()
        }
        .toolCallingMode(.required)
    }
}

// Or pass it as a generation option
let response = try await session.respond(
    to: "Write out the instructions for folding a paper crane.",
    options: GenerationOptions(toolCallingMode: .required)
)
```

## Escaping a required-tool-call loop with a conditional mode

```swift
struct OrigamiExpert: LanguageModelSession.DynamicProfile {
    let state: OrigamiAppState

    var body: some LanguageModelSession.DynamicProfile {
        Profile {
            Instructions("Answer questions about how to fold origami")
            QueryOrigamiDatabaseTool()
        }
        .toolCallingMode(state.queriedDatabase ? .disallowed : .required)
        .onToolCall { state.queriedDatabase = true }
    }
}
```

## Escaping the loop with a final-answer tool that throws

```swift
    var output: String?

    @Generable struct Arguments {
        var answer: String
    }

    func call(arguments: Arguments) async throws -> Never {
        output = arguments.answer
        throw CancellationError()
    }
}
```

## Transcript error handling policy

```swift
// Specify transcript behavior on a profile
struct OrigamiExpert: LanguageModelSession.DynamicProfile {
    let state: OrigamiAppState

    var body: some LanguageModelSession.DynamicProfile {
        Profile {
            Instructions("Answer questions about how to fold origami")
            QueryOrigamiDatabaseTool()
        }
        .transcriptErrorHandlingPolicy(.preserveTranscript)
    }
}

// Or specify it on a session
let session = LanguageModelSession()
session.transcriptErrorHandlingPolicy = .preserveTranscript

// Policy options
extension LanguageModelSession {
    public struct TranscriptErrorHandlingPolicy: Sendable {
        // Roll the transcript back to its previous state
        public static let revertTranscript: TranscriptErrorHandlingPolicy
        // Keep the transcript in state following an error
        public static let preserveTranscript: TranscriptErrorHandlingPolicy
    }
}
```

## Mutable transcript — the new session surface

```swift
public final class LanguageModelSession: Sendable {
    public var transcriptErrorHandlingPolicy: TranscriptErrorHandlingPolicy { get set }

    // Transcript is now settable
    public var transcript: Transcript { get set }

    // But you must not modify it during a response!
    public var isResponding: Bool { get }
}
```

---

## Useful API facts surfaced by the code

- `DynamicProfile` body resolves to one active `Profile` per prompt; `switch` over app mode chooses it.
- Profile config modifiers: `.model(_)`, `.temperature(_)`, `.reasoningLevel(_)`, `.historyTransform { }`, `.onResponse { }`, `.onToolCall { }`, `.toolCallingMode(_)`, `.transcriptErrorHandlingPolicy(_)`, `.modifier(_)`.
- Utilities-package modifiers seen: `.rollingWindow(size: .entries(10))`, plus user-defined `.droppingCompletedToolCalls()`.
- Session properties: built-in `@SessionProperty(\.history)`; declare custom ones with `@SessionPropertyEntry` on `SessionPropertyValues`, then read via `@SessionProperty(\.summary)`. All are mutable and require an initial value.
- `ToolCallingMode`: `.allowed` (default), `.disallowed`, `.required`. Settable via profile modifier or `GenerationOptions(toolCallingMode:)`.
- A final-answer tool can return `Never` and `throw CancellationError()` to break out of a required-tool loop.
- `transcriptErrorHandlingPolicy`: `.revertTranscript` (default) / `.preserveTranscript`; `session.transcript` is now `get set` but only mutable while `isResponding == false`.
- `PhoneFriendTool` is generic over `P: LanguageModelSession.DynamicProfile` and creates its own `LanguageModelSession(profile:)` inside `call`.
