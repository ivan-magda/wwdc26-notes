# Session 246 — LLM search using Core Spotlight

- **URL:** https://developer.apple.com/videos/play/wwdc2026/246/
- **Duration:** 16m
- **Speakers:** Jennifer (Spotlight engineering team)

## Description

Build conversational search by making your app's content available to a language
model. The session grounds a `LanguageModelSession` in an app's Core Spotlight
index via the Foundation Models Tool protocol, using the new `SpotlightSearchTool`.
The running example is a hiking trails app that browses state parks and trails and
stores personal notes on completed hikes. Topics: configuring and adding the tool,
displaying batched results, recovering full searchable items through an index
delegate, scoping guidance with guidance profiles, resolving people with a contact
resolver, building custom pipeline stages, and measuring quality with the
Evaluations framework.

## Key topics

- **`SpotlightSearchTool`** — adopts the Foundation Models `Tool` protocol to let a model search an app's Core Spotlight index for contextual response generation; iOS, iPadOS, macOS, visionOS
- Prerequisite: the app must already **donate searchable content** to Core Spotlight (or index entities for Apple Intelligence)
- One-line setup; optional `configuration` with sources like `.files` (`FileSource`) to search the app sandbox
- Model choice via `SystemLanguageModel` or the new **Model Provider APIs**
- Tool-call trajectory: model decides → generates query → Spotlight executes → model reasons over result-set description → final response
- **Index delegate** — new `searchableItems(forIdentifiers:)` on `CSSearchableIndexDelegate` recovers the full `CSSearchableItem` on demand (some donated metadata, e.g. text/HTML, is stored compactly and is searchable but not LLM-readable); also a hook to attach extra model-only attributes
- **Two display surfaces** — the session `response` (concise summary, assistant-style UI) vs. the tool's batched `searchResults` async sequence (list UI); use `queryToken` to know when to refresh, since the model may call the tool multiple times per response
- **Guidance profiles** — `SpotlightSearchTool.GuidanceProfile` scopes guided-generation capabilities (text match, dates, people, specific attributes); `guide` levels `.dynamic(profile)` and `.focused(.items)`; focused guidance matters for smaller on-device context windows
- **Reference resolution** — `ContactResolver` returns the user's identity contact info so the tool can disambiguate "who did I go hiking with?"
- **Custom pipeline stages** — `@Generable` types conforming to `CustomStage` (input/output `SearchPipelineDataType`); model generates stages on demand for complex search + compute requests; reply data types include items, scoredItems, groupedItems, count, table, statistic, text — each with an LLM-generated label
- **Evaluations framework** — `ModelSampleProtocol` dataset with expected identifiers + `TrajectoryExpectation`; Sample Generation APIs expand seed samples; assert metrics like result coverage in a `@Test`

## Related sessions to fetch

- [ ] Deep dive into the Foundation Models framework
- [ ] Supporting semantic search with Core Spotlight (past session)
- [ ] Bring an LLM provider to the Foundation Models framework (Model Provider APIs)
- [ ] Sample data generation APIs (Evaluations)
- [ ] Creating robust evaluations for an agentic app

## Chapter summary (Summary tab)

- **0:00 Introduction** — Make app content available to a language model for conversational search. Running example: a hiking trails app browsing state parks/trails and storing personal hike notes.
- **1:41 Grounding answers with Spotlight tool-calling** — A `LanguageModelSession` answers broad questions from world knowledge; to answer only about the app's hikes, ground it in the Core Spotlight index via the `Tool` protocol. Introduces `SpotlightSearchTool` (iOS/iPadOS/macOS/visionOS) and the prerequisite of donating searchable content.
- **4:00 Configure and add SpotlightSearchTool** — Import `CoreSpotlight` and `FoundationModels`, create the tool (optionally with a custom configuration like a `FileSource`), choose a model (`SystemLanguageModel` or a Model Provider), add the tool to a session. Walks the query→grounded-response trajectory.
- **6:44 Displaying results and partial replies** — The session response suits an assistant-style UI; the tool's searchable items suit a list UI. Replies arrive as an async sequence of batched results; use the query token to know when to refresh, since the model may call the tool multiple times per response.
- **6:46 Provide full items with an index delegate** — Some donated metadata is stored compactly and isn't LLM-readable. Implement `searchableItems(forIdentifiers:)` on `CSSearchableIndexDelegate` to recover the full `CSSearchableItem` on demand and attach extra model-only attributes.
- **8:12 Customizing with guidance profiles** — `SpotlightSearchTool` exposes its full search capabilities for guided generation; a `GuidanceProfile` scopes that guidance (specific attributes, a dynamic guide level), which matters for the smaller context of on-device models.
- **11:02 Reference resolution with a contact resolver** — When a query references a person ("who did I go hiking with?"), supply a `ContactResolver` returning contact info matching the user's identity so the tool can disambiguate.
- **11:24 Custom pipeline stages** — For complex requests the model can run a pipeline of search + computation stages. Register your own `@Generable` stages (e.g. a happiness-score stage over notes); the model generates stages on demand and may return computed data to the app for display.
- **12:47 Evaluating response quality** — Use the Evaluations framework to measure tool-calling and response quality. Define a dataset via `ModelSampleProtocol` with expected item identifiers and trajectory, expand seed samples with the Sample Generation APIs, and assert metrics like result coverage in a test.
- **15:53 Next steps** — Download the hiking trails sample, add custom functionality and an evaluation suite, lean into the deep-dive sessions. Takeaway: stop writing search queries — provide the content and let intelligence do the rest.

## Code

See `code.md` — 13 snippets from the Code tab plus the spoken/Summary-tab APIs.
