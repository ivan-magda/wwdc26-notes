# Code samples — Session 246

Extracted from the Code tab. Timestamps map to the transcript chapters.

## ~1:00 — Broad question with an ungrounded session

```swift
let response = try await session.respond(to: "What are some nice hikes near water?")
```

## 4:20 — Create SpotlightSearchTool (default + file source)

```swift
// Set up SpotlightSearchTool
import CoreSpotlight
import FoundationModels

// In one line, the tool is ready to search your app's Core Spotlight index
let tool = SpotlightSearchTool()

// Or provide a custom configuration — e.g. search file paths in your app's sandbox
let fileTool = SpotlightSearchTool(
    configuration: .init(
        sources: [
            .files
        ]
    )
)
```

## 4:50 — Add the tool to a LanguageModelSession

```swift
// Add SpotlightSearchTool to a session
import CoreSpotlight
import FoundationModels

let tool = SpotlightSearchTool()

let session = LanguageModelSession(model: model, tools: [tool], instructions: instructions)

let response = try await session.respond(to: "What hikes have I gone on?")
```

## 6:24 — Index delegate: recover full searchable items

```swift
// Implement an index delegate
import CoreSpotlight

class IndexDelegate: NSObject, CSSearchableIndexDelegate {

    // Called when the index requests searchable items for the provided identifiers
    func searchableItems(forIdentifiers identifiers: [String]) async -> [CSSearchableItem] {
        let entries = await mystore.fetchEntries(ids: identifiers)
        return entries.map { makeSearchableItem(from: $0) }
    }
}
```

## 7:20 — Read batched search replies, refresh on query token

```swift
// Track the query token for refresh
import CoreSpotlight
import FoundationModels

let tool = SpotlightSearchTool()

for await reply in tool.searchResults {

    if reply.queryToken != currentToken {
        // New query — start a new display section
        currentToken = reply.queryToken
    }

    switch reply.content {
    case .items(let searchItems):
    }
}
```

## 8:53 — Guidance profiles: dynamic vs. focused

```swift
// Set a dynamic guidance profile
import CoreSpotlight
import FoundationModels

let profile = SpotlightSearchTool.GuidanceProfile(
    textMatch: true,
    dates: true,
    people: false,
    attributes: [.title, .altitude, .completionDate]
)

let tool = SpotlightSearchTool(
    configuration: .init(
        guide: .init(level: .dynamic(profile))
    )
)

// On-device models have smaller context — prefer focused guidance
let focusedTool = SpotlightSearchTool(
    configuration: .init(
        guide: .init(level: .focused(.items))
    )
)
```

## 11:06 — ContactResolver for reference resolution

```swift
// Implement a ContactResolver
import CoreSpotlight
import FoundationModels

struct MyContactResolver: ContactResolver {

    func userIdentity() -> ResolvedContact {
        // Pull from whatever identity source your app has —
        // account profile, Contacts framework, sign-in session, etc.
        var contact = ResolvedContact(displayName: "Jane Doe")
        contact.emailAddresses = ["jane@example.com", "jdoe@work.com"]
        contact.names = ["Jane", "JD"]
        return contact
    }
}

tool.contactResolver = MyContactResolver()
```

## 11:58 — Custom @Generable pipeline stage

```swift
// Define a custom stage
import CoreSpotlight
import FoundationModels

@Generable
struct HappinessStage: CustomStage {
    static var name = "happiness"
    static var description = "Scores hike by how happy the author was"
    static var inputTypes: [SearchPipelineDataType] = [.items]
    static var outputTypes: [SearchPipelineDataType] = [.scoredItems]

    @Guide(description: "Minimum happiness score (0.0-1.0) to include in results")
    var threshold: Double?

    func execute(on input: SearchPipelineData) async throws -> SearchPipelineData {
        return SearchPipelineData(payload: .scoredItems(sorted))
    }
}

// Register the stage by adding it to the tool's configuration
let tool = SpotlightSearchTool(configuration: .init(
    customStages: [.happinessBoost(threshold: 0.5)])
)
```

## 12:17 — Handle the full set of reply data types

```swift
// Handle a reply data types
import CoreSpotlight
import FoundationModels

for await reply in tool.searchResults {

    let label = reply.label
    case .items(let searchItems):
    case .scoredItems(let scored):
    case .groupedItems(let groups):
    case .count(let count):
    case .table(let table):
    case .statistic(let statistic):
    case .text(let text):
        continue
    }
}
```

## 13:47 — Evaluations: ModelSampleProtocol dataset

```swift
// Evaluations
import Evaluations

struct TrailRequest: ModelSampleProtocol {

    typealias ExpectedValue = String                    // sample response
    typealias Expectation   = TrajectoryExpectation

    var input:  ModelSampleInput
    var output: ModelSampleOutput<String, TrajectoryExpectation>

    var expectedIdentifiers: [String]
}
```

## 15:06 — Evaluations: TrajectoryExpectation

```swift
// Evaluations
import Evaluations

TrajectoryExpectation(
    unordered: [
        ToolExpectation("searchSpotlight", arguments: [.keyOnly(argumentName: "query")])
    ]
)
```

## 15:24 — Evaluations: assert result coverage in a test

```swift
@Test("Trail search evaluation meets quality thresholds")
func trailSearchEval() async throws {

    let items = try Self.loadItems()
    let samples = try Self.loadSamples()

    try await Self.indexDelegate.indexSearchableItems(items)
    let tool = Self.makeSearchTool()

    let evaluation = TrailSearchEvaluation(
        tool: tool,
        dataset: ArrayLoader(samples: samples)
    )

    let result = try await evaluation.run()
    let coverageMean = result.aggregateValue(.mean(of: Metric("ResultCoverage")))
    #expect(coverageMean >= 0.5, "Result coverage should be at least 50% across queries")
}
```

---

## Useful API facts surfaced by the code

- `SpotlightSearchTool()` works in one line; `configuration:` takes `sources` (`.files`), a `guide`, and `customStages`.
- `tool.searchResults` is an async sequence of replies; each reply carries a `queryToken`, an LLM-generated `label`, and `content`.
- Reply `content`/data types: `.items`, `.scoredItems`, `.groupedItems`, `.count`, `.table`, `.statistic`, `.text`.
- New delegate hook is spelled `searchableItems(forIdentifiers:)` (the transcript narrates it as `searchableItems (forIdentifiers:)`).
- `GuidanceProfile` fields seen: `textMatch`, `dates`, `people`, `attributes: [.title, .altitude, .completionDate]`; guide levels `.dynamic(profile)` and `.focused(.items)`.
- Custom stages conform to `CustomStage`, are `@Generable`, declare `inputTypes`/`outputTypes` as `[SearchPipelineDataType]`, and `execute(on:) -> SearchPipelineData`.
- Evaluations: `ModelSampleProtocol` (with `expectedIdentifiers`), `TrajectoryExpectation` + `ToolExpectation`, and a result metric `Metric("ResultCoverage")`.

> Note: a few Code-tab snippets are illustrative fragments (the reply-`switch` blocks omit case bodies and one is missing its `switch`/`{`). Reproduced as extracted; clean up before compiling.

## Spoken APIs (named in the transcript)

- `SpotlightSearchTool`, `CSSearchableIndex`, `CSSearchableItem`, `CSSearchableIndexDelegate`
- `LanguageModelSession`, `SystemLanguageModel`, Model Provider APIs
- `Tool` protocol, `@Generable`, `@Guide`
- `ModelSampleProtocol`, Sample Generation APIs, Evaluations framework, result coverage metric
