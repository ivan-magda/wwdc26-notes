# Session 274 — What's new in SwiftData

- **URL:** https://developer.apple.com/videos/play/wwdc2026/274/
- **Duration:** 13m
- **Speakers:** Thomas (Engineer, SwiftData team)

## Description

What's new in SwiftData for Apple's 2027 releases. The session covers three
additions: fetching data in sections with `@Query`'s new `sectionBy:` parameter,
storing custom non-modelable types via `@Attribute(.codable)`, and observing the
store from non-SwiftUI code with the new `ResultsObserver` and `HistoryObserver`.
All demos build on the long-running "SampleTrips" sample app.

## Key topics

- **Sectioned fetches** — `@Query(sort:, sectionBy:)` groups results by a key path; access the underlying query via the underscore name (`_trips`) to iterate `.sections`, each with an `id` and a collection of models
- **Custom types via `@Attribute(.codable)`** — escape hatch for types SwiftData can't model natively (e.g. `MKMapItem.Identifier`); serialized blob is opaque (no predicates/sort/migration)
- **`ResultsObserver`** — Query-style fetching + observation for non-SwiftUI code (state objects, SceneKit games); supports filter/sort/sectioning; pair with `withContinuousObservation(options: [.didSet])`
- **`HistoryObserver`** — single observable `eventCounter` that ticks on new persistent-history transactions; filter by model type and transaction author; pair with `ModelContext.fetchHistory()` for server sync

## Related sessions to fetch (referenced in this talk)

- [ ] Add persistence with SwiftData (code-along, beginner onboarding)
- [ ] Track Model Changes with SwiftData History (WWDC 2024)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Thomas; agenda: sectioning fetches, using custom types, observing data stores. Targets Apple's 2027 releases.
- **0:53 Sectioning your fetches** — `@Query`'s new `sectionBy:` parameter groups results by a key path; iterate `_trips.sections` (each with `id` + a model collection); demoed by grouping trips by destination in SampleTrips.
- **2:56 Using custom types** — store types SwiftData can't model (e.g. `MKMapItem.Identifier`) by marking properties `@Attribute(.codable)`. Codable is an escape hatch for external types, not types you own; modeling your own types unlocks filtering, sorting, migration.
- **6:26 Observing data stores with ResultsObserver** — brings Query-style fetching to non-SwiftUI code; combine with `withContinuousObservation(didSet:)` to react to model changes anywhere; shown updating map camera bounds as trips change.
- **9:41 Observing history with HistoryObserver** — exposes a single observable `eventCounter` that ticks on new persistent-history transactions; pair with `ModelContext.fetchHistory()` filtered by model type / author — ideal for syncing to an external server.
- **12:20 Next steps** — recap: section fetches, adopt codable types, react with ResultsObserver, observe history with HistoryObserver.

## Code

See `code.md` — 4 snippets extracted from the Code tab.
