---
title: "LLM search using Core Spotlight — Full Digest"
session: WWDC26 · 246
url: https://developer.apple.com/videos/play/wwdc2026/246/
duration: 16m
speakers: Jennifer (Spotlight engineering)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 246 — LLM search using Core Spotlight

## TL;DR

`SpotlightSearchTool` is the concrete payoff of the "Spotlight-backed search tool"
teased in session 241: a ready-made Foundation Models `Tool` that lets a language
model search your app's **Core Spotlight index** for fully **local RAG** — no server,
no embeddings pipeline, no hand-written queries. Headline themes:

1. **One-line grounding.** Donate searchable content to Core Spotlight (a prereq you
   may already meet), then `SpotlightSearchTool()` and hand it to a
   `LanguageModelSession`. The model decides when to search, generates the query,
   Spotlight runs it, the model reasons over the results.
2. **Two display surfaces.** The session `response` is a concise assistant-style
   summary; the tool's `searchResults` async sequence streams the raw
   `CSSearchableItem`s in batches for a list UI. `queryToken` tells you when to
   refresh, because the model may search more than once per answer.
3. **Recover what the index hides.** Some donated metadata (text, HTML) is stored
   compactly — searchable but not LLM-readable. A new index-delegate method
   `searchableItems(forIdentifiers:)` rehydrates full items on demand and is the
   place to attach extra, model-only attributes.
4. **Tune for small models.** `GuidanceProfile` scopes which capabilities/attributes
   the model is guided on; `.focused` guidance keeps on-device context budgets sane.
   A `ContactResolver` supplies the user's identity for "who did I hike with?".
5. **Compute, not just retrieve.** Register `@Generable` **custom pipeline stages**
   so the model can run search-plus-computation (counts, tables, scored items) and
   even return computed data back to the app.
6. **Measure it.** The Evaluations framework scores tool-calling and response quality
   (e.g. result coverage) so you can iterate on content + guidance profiles.

The running example throughout is a **hiking trails app**: browse state parks/trails,
store personal notes on completed hikes, then ask the model about them.

The closing line is the thesis: *"We're not writing search queries anymore. We're
providing the content, and letting intelligence do the rest."*

---

## 1. Introduction — from world knowledge to app knowledge (0:00)

A bare `LanguageModelSession` will answer "what are some nice hikes near water?" from
the model's own world knowledge:

```swift
let response = try await session.respond(to: "What are some nice hikes near water?")
```

But the app only wants answers about *its* hikes — the ones it has already indexed
into Core Spotlight. Spotlight is the bridge. The mechanism is Foundation Models
**tool-calling**: a tool declares its arguments, output, and instructions; when the
model decides it needs the tool, it generates the call arguments and reasons over the
returned output. (Background: "Deep dive into the Foundation Models framework.")

## 2. Grounding with SpotlightSearchTool (1:41)

`SpotlightSearchTool` adopts the `Tool` protocol to let the model search the app's
Core Spotlight content directly. **Available on iOS, iPadOS, macOS, and visionOS.**

Prerequisite: the app must already **donate searchable content** to Core Spotlight,
or index entities for Apple Intelligence. (Background: the past session "Supporting
semantic search with Core Spotlight" covers donating content, managing donations with
a delegate + reindex extension, structured search over attributes, and the semantic
index.)

Three things to get right when adopting the tool: **configure** it for the kind of
search you want, **add context** while the search is active, and **display** results.

## 3. Configure and add the tool (4:00)

Configuration looks a lot like a normal Spotlight query. Import both frameworks; one
line gives you a working tool. A custom `configuration` can add sources — e.g.
`.files` to search file paths in the app sandbox:

```swift
import CoreSpotlight
import FoundationModels

let tool = SpotlightSearchTool()

let fileTool = SpotlightSearchTool(
    configuration: .init(sources: [.files])
)
```

Pick a model — `SystemLanguageModel` or any model via the new **Model Provider APIs**
— then add the tool to the session:

```swift
let session = LanguageModelSession(model: model, tools: [tool], instructions: instructions)
let response = try await session.respond(to: "What hikes have I gone on?")
```

**The trajectory** behind the "magic": model decides it needs the tool → invokes it
with a generated query → Spotlight executes → returns a *description of the result
set* → model reasons over that and generates the final response.

## 4. Recovering full items with an index delegate (6:00)

A subtlety: the model sometimes can't see all donated metadata. Text content and HTML
are stored in a highly compact representation that is **searchable but not recoverable
into LLM-readable form**. The fix is a new method on the index delegate you may
already own, `searchableItems(forIdentifiers:)`, which returns the complete
`CSSearchableItem` on demand — letting the model manage responses over potentially
millions of results, and giving you a hook to attach **extra attributes that exist
only for the model to reason over** (metadata you wouldn't otherwise donate for
search):

```swift
class IndexDelegate: NSObject, CSSearchableIndexDelegate {
    func searchableItems(forIdentifiers identifiers: [String]) async -> [CSSearchableItem] {
        let entries = await mystore.fetchEntries(ids: identifiers)
        return entries.map { makeSearchableItem(from: $0) }
    }
}
```

## 5. Displaying results and partial replies (6:44)

Two surfaces, two UI styles:

- **Session `response`** — a concise description over the result set. Best for an
  **assistant-style** interface.
- **`tool.searchResults`** — the raw `CSSearchableItem`s, available directly on the
  tool. Best for a **list-style** display, especially with large result sets.

Replies arrive as an **async sequence of batched events** during the search. Because
the model may call the tool **more than once** before finalizing a response, watch the
`queryToken` to know when to start a new display section / refresh the UI:

```swift
for await reply in tool.searchResults {
    if reply.queryToken != currentToken {
        currentToken = reply.queryToken   // new query — new section
    }
    switch reply.content {
    case .items(let searchItems):
    }
}
```

## 6. Guidance profiles — scope the model's search powers (8:12)

`SpotlightSearchTool` exposes its *entire* capability set (semantic text search,
structured search over dates/persons/locations/…) to the model for guided generation.
That's a lot of guidance to feed a small model. A `GuidanceProfile` scopes it to only
what the app needs. The hiking app doesn't donate person relationships, so guidance on
authors/recipients can be dropped for limited-context models. You can also pin the
exact attribute list:

```swift
let profile = SpotlightSearchTool.GuidanceProfile(
    textMatch: true,
    dates: true,
    people: false,
    attributes: [.title, .altitude, .completionDate]
)

let tool = SpotlightSearchTool(
    configuration: .init(guide: .init(level: .dynamic(profile)))
)

// On-device models have smaller context — prefer focused guidance
let focusedTool = SpotlightSearchTool(
    configuration: .init(guide: .init(level: .focused(.items)))
)
```

Guide levels seen: **`.dynamic(profile)`** and **`.focused(.items)`**. On-device
models have a more restricted context, so focused guidance for simpler capabilities is
recommended.

## 7. Reference resolution with a ContactResolver (11:02)

When a prompt references a *person* ("who did I go hiking with?"), the model needs to
know who that person is. If the app already knows the user's identity, supply a
`ContactResolver` that returns matching contact info so the tool can disambiguate and
filter to the right results:

```swift
struct MyContactResolver: ContactResolver {
    func userIdentity() -> ResolvedContact {
        var contact = ResolvedContact(displayName: "Jane Doe")
        contact.emailAddresses = ["jane@example.com", "jdoe@work.com"]
        contact.names = ["Jane", "JD"]
        return contact
    }
}

tool.contactResolver = MyContactResolver()
```

## 8. Custom pipeline stages — search *plus* computation (11:24)

For complex requests the model may **forgo a simple query** in favor of a **pipeline
search**: queries to the index combined with computation over the result set, for
efficiency. Example: *"how many trails have I hiked this year, and for each month, how
many miles on average?"* The model can break this into a search for completed hikes →
a counting stage that builds a table by month → a stage that averages the counts.

Pipeline stages are **`@Generable`**, so the model generates them on demand based on
the prompt — and a stage may **return data back to the app** when it makes sense. Apps
register their own stages. The session's example computes a **happiness score** over
each hike's notes (e.g. via sentiment analysis or "rated 5 stars" logic), so the model
answers *"I remember being really happy on some hikes — which ones?"* from computed
top-scoring results rather than guessing:

```swift
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

let tool = SpotlightSearchTool(configuration: .init(
    customStages: [.happinessBoost(threshold: 0.5)]
))
```

Because a stage can emit a partial result, replies carry a **rich set of data types** —
each with an **LLM-generated `label`** describing the content, so the app has maximum
UI flexibility:

```swift
for await reply in tool.searchResults {
    let label = reply.label
    // reply data types:
    // .items / .scoredItems / .groupedItems / .count / .table / .statistic / .text
}
```

## 9. Evaluating response quality (12:47)

The Evaluations framework measures both **how well the model calls the tool** and **how
meaningful the response is**, and lets you iterate quickly on searchable content paired
with different guidance profiles. The session focuses on **result coverage**: given a
dataset indexed in Spotlight, how well does the model produce responses based on the
items you expect it to find?

Define a dataset adopting `ModelSampleProtocol` with the natural-language input, the
expected response, a trajectory expectation, and the set of expected item identifiers:

```swift
struct TrailRequest: ModelSampleProtocol {
    typealias ExpectedValue = String
    typealias Expectation   = TrajectoryExpectation
    var input:  ModelSampleInput
    var output: ModelSampleOutput<String, TrajectoryExpectation>
    var expectedIdentifiers: [String]
}
```

Seed samples (Codable / JSON) can be **expanded with the Sample Generation APIs** in a
command-line tool to get broad coverage of how people phrase questions. Define the
expected trajectory — here, a call to the tool to perform a query:

```swift
TrajectoryExpectation(
    unordered: [
        ToolExpectation("searchSpotlight", arguments: [.keyOnly(argumentName: "query")])
    ]
)
```

Then run it in a test target: load items + samples, donate items to Spotlight,
configure the tool, run the evaluation, and assert a metric:

```swift
@Test("Trail search evaluation meets quality thresholds")
func trailSearchEval() async throws {
    let items = try Self.loadItems()
    let samples = try Self.loadSamples()

    try await Self.indexDelegate.indexSearchableItems(items)
    let tool = Self.makeSearchTool()

    let evaluation = TrailSearchEvaluation(tool: tool, dataset: ArrayLoader(samples: samples))
    let result = try await evaluation.run()

    let coverageMean = result.aggregateValue(.mean(of: Metric("ResultCoverage")))
    #expect(coverageMean >= 0.5, "Result coverage should be at least 50% across queries")
}
```

(Background: sessions on sample data generation APIs and "creating robust evaluations
for an agentic app.")

## 10. Next steps (15:53)

Download the hiking trails sample, add your own custom functionality and an evaluation
suite, and lean into the deep-dive sessions.
