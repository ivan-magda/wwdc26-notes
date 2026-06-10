# Code samples — Session 347

Extracted from the Code tab. Timestamps map to the transcript. A few OCR artifacts in
the raw extraction were cleaned up: stray `γ` glyphs before `entries` in the
`.historyTransform` closures, and a smart quote in one tool description. The brace
layout of the `delimit`/`redactPII` examples is left as Apple presented it (the segment
remapping reads as if it sits just inside the `entries.map` closure).

## 12:49 — Defining tools (Tool protocol)

```swift
struct OrderTeaTool: Tool {
  let name = "orderTeaTool"
  let description: String = "Orders a particular quantity of a tea from the store."
  // Arguments
  // Implementation
}

struct PostAndFetchPublicFeedTool: Tool {
  let name = "postAndFetchPublicFeedTool"
  let description: String = "Posts a message to the public feed."
  // Arguments
  // Implementation
}
```

## 13:13 — Profile with instructions + tools + model

```swift
class LooseLeafAgent {
  struct DefaultProfile: LanguageModelSession.DynamicProfile {
    var body: some DynamicProfile {
      Profile {
        Instructions("You are a helpful, tea-loving assistant ... ")

        OrderTeaTool()
        PostAndFetchPublicFeedTool()
      }
      .model(SystemLanguageModel())
    }
  }
}
```

## 13:28 — Instantiating the LanguageModelSession from the profile

```swift
class LooseLeafAgent {
  struct DefaultProfile: LanguageModelSession.DynamicProfile {
    var body: some DynamicProfile {
      Profile {
        Instructions("You are a helpful, tea-loving assistant ... ")

        OrderTeaTool()
        PostAndFetchPublicFeedTool()
      }
      .model(SystemLanguageModel())
    }
  }

  let session: LanguageModelSession

  public init() {
    self.session = LanguageModelSession(profile: DefaultProfile())
  }
}
```

## 14:46 — User confirmation via .onToolCall

```swift
var body: some DynamicProfile {
  Profile {
    Instructions("You are a helpful, tea-loving assistant ... ")

    OrderTeaTool() // Financial impact; risky tool.
    // Other Tools
  }

  .onToolCall { call in
    guard call.toolName == "orderTeaTool" else {
      return
    }
    guard ConfirmationAction.confirmWithUser() else {
      throw LooseLeafError.userConfirmationDenied
    }
  }
}
```

## 16:14 — Spotlighting untrusted tool output via .historyTransform

```swift
var body: some DynamicProfile {
  Profile {
    Instructions("You are a helpful, tea-loving assistant ... ")

    PostAndFetchPublicFeedTool() // Returns untrusted data; requires spotlighting
    // Other Tools
  }

  .historyTransform { entries in
    entries.map { entry in
      guard case .toolOutput(var toolOutput) = entry,
        toolOutput.toolName == "postAndFetchPublicFeedTool"
      else {
        return entry
      }
    }
    toolOutput.segments = toolOutput.segments.map { segment in
      delimit(segment: segment,
              startDelimiter: "<<UNTRUSTED>>",
              endDelimiter: "<</UNTRUSTED>>")
    }
    return .toolOutput(toolOutput)
  }
}

func delimit(segment: Transcript.Segment,
             startDelimiter: String,
             endDelimiter: String) -> Transcript.Segment
```

## 16:48 — PII redaction via .historyTransform

```swift
var body: some DynamicProfile {
  Profile {
    Instructions("You are a helpful, tea-loving assistant ... ")

    PostAndFetchPublicFeedTool() // Returns untrusted data; requires spotlighting
    // Other Tools
  }

  .historyTransform { entries in
    entries.map { entry in
      guard case .toolOutput(var toolOutput) = entry,
        toolOutput.toolName == "postAndFetchPublicFeedTool"
      else {
        return entry
      }
    }
    toolOutput.segments = toolOutput.segments.map { segment in
      redactPII(segment: segment,
                placeHolder: "[REDACTED]")
    }
    return .toolOutput(toolOutput)
  }
}

func redactPII(segment: Transcript.Segment,
               placeHolder: String) -> Transcript.Segment
```

## 23:15 — App Intent authentication policy (custom intent)

```swift
struct DeletePhotoIntent: DeleteIntent {
    var entities: [LooseLeafPhoto]

    static var authenticationPolicy: IntentAuthenticationPolicy = .requiresAuthentication

    func perform() async throws -> some IntentResult {
        // Implementation
    }
}
```

## 23:27 — Schema-derived authentication policy (@AppIntent adopting a schema)

```swift
@AppIntent(schema: .photos.deleteAssets)
struct DeletePhotoIntent {
    var entities: [LooseLeafPhoto]

    // Example: Schema default authentication policy is .requiresAuthentication

    func perform() async throws -> some IntentResult {
        // Implementation
    }
}
```

---

## Useful API facts surfaced by the code

- **`.onToolCall { call in ... }`** — profile modifier; throwing inside it blocks the tool. Branch on `call.toolName`.
- **`.historyTransform { entries in ... }`** — profile modifier returning rewritten transcript entries; match `case .toolOutput(var toolOutput)` and remap `toolOutput.segments`. Transform is scoped to the current inference iteration only.
- **`Transcript.Segment`** — the unit a `delimit`/`redactPII` helper operates on.
- **`@SessionProperty`** — persist expensive transforms across iterations (mentioned in transcript, not in a code snippet).
- **App Intents:** `static var authenticationPolicy: IntentAuthenticationPolicy = .requiresAuthentication` on a custom intent; when adopting a schema (`@AppIntent(schema: .photos.deleteAssets)`) the policy is inherited from the schema default and can only be overridden *stricter* (weaker → build error).
