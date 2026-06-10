# Session 275 — Code-along: Add persistence with SwiftData

- **URL:** https://developer.apple.com/videos/play/wwdc2026/275/
- **Duration:** 22m
- **Speakers:** Matthew Turk (SwiftData team engineer)

## Description

A hands-on code-along that takes an existing SwiftUI sample app — **Wishlist**, a
list-based travel-planning app — from in-memory `DataSource` state to a persistent
SwiftData layer that works across Apple's platforms. The walkthrough covers three
adoption steps: identify relevant state, define schemas as `@Model` types, and define
model relationships. It then updates the view layer with `@Query` and targeted
`FetchDescriptor` predicates, handles runtime errors, and re-enables property-observer
behavior with the new `withContinuousObservation` API.

## Key topics

- Three-step SwiftData adoption: identify state → define schemas → define relationships
- Converting `@Observable` types to `@Model` (Activity, Trip); SwiftData auto-generates Observable conformance
- Model requirements: stored properties must be `var` (mutable) and `Codable`
- Refactoring a closed `enum Goal` (18 cases) into a `@Model class` hierarchy — `Goal` superclass with `TripGoal` / `ActivityGoal` subclasses via SwiftData model inheritance
- Storing progress as stored properties (`completedCount`, `isComplete`) instead of computed/separate state
- `@Relationship(deleteRule: .cascade, inverse:)` for the Trip → [Activity] to-many relationship
- Image handling: inline `thumbnailData: Data?` vs. a separate `TripImage` model with persistent external file reference (replacing a fragile `photoURL` file path)
- Deleting now-redundant helpers (`DataSource`, `TripEditModel`) — "hundreds of lines removed"
- `.modelContainer` scene modifier on the WindowGroup
- View layer: `@Query` macro, dynamically-constructed queries in initializers, `FetchDescriptor` with `fetchLimit`, predicate filtering for memory/IO balance
- Autosave enabled by default
- Surfacing runtime errors with state variables + `.alert(error:)` view modifier
- Re-enabling `dateEdited` side effects via `withContinuousObservation(options: .didSet)` (Observation framework, "2027 releases")

## Related sessions to fetch (referenced in this talk)

- [ ] SwiftData: Dive into inheritance and schema migration (WWDC 2025)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Wishlist sample app overview; three steps for adopting SwiftData (identify state, define schemas, define relationships).
- **1:05 Identify relevant state** — trip collections, goal statuses, and the `DataSource` become SwiftData models connected through a `ModelContext`. Demonstrates that in-memory state is lost on relaunch.
- **3:17 Define your schemas** — convert Activity, Trip, Goal into `@Model` types; handle `didSet` property observers under the `@Model` macro; refactor the `Goal` enum into a class hierarchy with `TripGoal` / `ActivityGoal` subclasses; inline thumbnail data.
- **9:41 Define model relationships** — to-many Trip↔Activity relationship via `@Relationship`; remove redundant `DataSource` and `TripEditModel`; attach the `.modelContainer` scene modifier.
- **13:33 Update the view layer** — replace environment `DataSource` properties with `@Query` and targeted `FetchDescriptor` predicates per subview; autosave; surfacing runtime errors with view modifiers; re-enable `dateEdited` observers via `withContinuousObservation`.
- **21:47 Next steps** — takeaways: design a schema that fits your data model; balance memory vs. disk with targeted queries; plan for interoperability and extensibility.

## Code

See `code.md` — 10 snippets extracted from the Code tab.
