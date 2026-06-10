---
title: "What's new in SwiftData — Full Digest"
session: WWDC26 · 274
url: https://developer.apple.com/videos/play/wwdc2026/274/
duration: 13m
speakers: Thomas (SwiftData team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 274 — What's new in SwiftData

## TL;DR

A short, focused update to SwiftData for Apple's 2027 releases, structured around three additions — all demoed in the long-running "SampleTrips" app:

1. **Sectioned fetches** — `@Query` gains a `sectionBy:` key-path parameter. The wrapped value stays `[Trip]`; you reach grouped `.sections` through the underscore-prefixed query (`_trips`), each section carrying an `id` (the key-path value) and its own model collection.
2. **Custom types via `@Attribute(.codable)`** — a new attribute option that delegates serialization to any `Codable` type SwiftData can't model natively (the example is MapKit's `MKMapItem.Identifier`). It's explicitly framed as an *escape hatch* for types you don't own, with real trade-offs.
3. **Observation outside SwiftUI** — two new observers, `ResultsObserver` (Query-style fetch + observe, anywhere) and `HistoryObserver` (a single `eventCounter` that ticks on new persistent-history transactions), both driven by Swift Observation's `withContinuousObservation`.

The framing throughout: `@Query` remains the right tool *inside* SwiftUI views; the new observers bring the same reactivity to everything that isn't a view.

---

## 1. Sectioning your fetches

The starting point is the familiar pattern: `@Query(sort: \Trip.startDate)` fetches all trips into a SwiftUI view as a plain `[Trip]`. The goal is grouping trips by destination so same-place trips sit together.

In the 2027 releases, `@Query` takes a new **`sectionBy:`** parameter — a key path from the model root to the section key (here a `String` destination):

```swift
struct TripListView: View {
    @Query(sort: \Trip.startDate,
           sectionBy: \.destination)
    var trips: [Trip]

    var body: some View {
        List(selection: $selection) {
            ForEach(_trips.sections) { section in
                Section(section.id) {
                    ForEach(trips) { trip in
                        TripListItem(trip: trip)
                    }
                }
            }
        }
    }
}
```

Key mechanics from the talk:

- The **wrapped value is unchanged** — still `[Trip]`. Adding `sectionBy:` alone doesn't change the rendered list.
- To get sections you go through the **property wrapper itself** via the underscore-prefixed name: `_trips.sections` returns the list of sections.
- Each **section has an `id`** equal to the value at the `sectionBy:` key path (the destination), used here as the `Section` header label.
- Each **section is a collection** of the section's models, so the inner `ForEach` iterates the section's trips.

## 2. Using custom types

The SampleTrips `Trip` model stores `name`, `destination`, `startDate`, `endDate`. The author wants to enrich destinations with MapKit data — coordinates plus a `MKMapItem.Identifier`, which uniquely identifies a location and can reopen it in Maps.

Adding `MKMapItem.Identifier` directly crashes at launch:

> "Class property within Persisted Struct/Enum is not supported" — pointing at `MKMapItem.Identifier`.

Why: when SwiftData loads it auto-generates a **schema** mapping model classes to persistable entities/properties. It can't inspect a class that isn't `@Model`, and `MKMapItem.Identifier` is a MapKit class the developer can't modify or annotate. It does, however, conform to `Codable`.

The new option: mark the attribute **`@Attribute(.codable)`** to tell SwiftData to delegate serialization to the type and persist the encoded representation instead of inferring a schema:

```swift
import SwiftData

@Model class Trip {
    struct Location: Codable {
        var latitude: Double
        var longitude: Double
    }

    var name: String
    var destination: String
    var startDate: Date
    var endDate: Date

    var location: Location?
    @Attribute(.codable) var mapItemIdentifier: MKMapItem.Identifier?
}
```

**Trade-offs (like transformable attributes):**

- Contents are **opaque** to SwiftData — can't be used in `Predicate` filters or `SortDescriptor` sorting.
- Shape changes (adding/removing properties) **don't trigger a migration**; the type's `Codable` implementation must encode/decode in a forward- and backward-compatible way.
- It's an **escape hatch** — recommended for types you *don't own*. For your own types, prefer modeling them as `@Model` or supported value types to keep sorting, filtering, indexing, and migration.

## 3. Observing data stores with `ResultsObserver`

`@Query` already does more than fetch: when a view appears it fetches, then **continuously monitors** the store and re-renders on relevant changes (e.g. a deleted trip). It's the right first choice *inside* a SwiftUI view.

But non-view code — a state object deriving values from the store, or a non-SwiftUI app such as a SceneKit game — needs the same reactivity. Enter **`ResultsObserver`**: same fetch-then-observe model as `@Query`, working **anywhere** via Swift Observation, with the same primitives (filtering, sorting, the new sectioning).

The demo adds a `MapCameraController` that fits the map camera to all planned trips:

```swift
@Observable @MainActor final class MapCameraController {
    private let resultsObserver: ResultsObserver<Trip, Never>
    var bounds: MapCameraBounds?
    private var token: ObservationTracking.Token?

    init(modelContext: ModelContext) throws {
        resultsObserver = try ResultsObserver<Trip, Never>(modelContext: modelContext)

        token = withContinuousObservation(options: [.didSet]) { [weak self] event in
            self?.bounds = self?.calculateBounds(trips: resultsObserver.results)
        }
    }

    private func calculateBounds(trips: [Trip]) -> MapCameraBounds? { /* ... */ }
}
```

- No predicate or sectioning key path is passed → observes **all** trips.
- `withContinuousObservation(options: [.didSet])` fires a callback on every change; the closure recalculates the map bounds.
- It returns an **`ObservationTracking.Token`** whose lifetime defines the observation window — stored on the class so updates continue for the controller's lifetime.
- Demo: launch fits all trips; deleting the Toronto trip triggers a recompute and the camera re-fits.

## 4. Observing history with `HistoryObserver`

Quick history primer: when the store changes, SwiftData records everything. On every save it writes a **history transaction** describing what changed, where it came from, and a **token** uniquely identifying it. That token feeds `ModelContext.fetchHistory` to pull newer transactions. (Background: "Track Model Changes with SwiftData History," WWDC 2024.)

New **`HistoryObserver`** makes reacting to history easy — the history analogue of `ResultsObserver`. It exposes a **single observable property, `eventCounter`**, that increments when new persistent-history transactions arrive. You can also **filter by model type and transaction author**. Typical use: keeping part of the store in sync with an external server.

```swift
@SyncActor final class ServerSync {
    private let observer: HistoryObserver
    private var token: ObservationTracking.Token?

    func start() throws {
        self.observer = try HistoryObserver(authors: ["App"], modelContainer: modelContainer)
        token = withContinuousObservation(options: .didSet) { [weak self] _ in
            _ = self?.observer.eventCounter
            self?.processChanges()
        }
    }

    private func processChanges() {
        // Use ModelContext.fetchHistory(...) to fetch the latest changes
        // and upload them to the server.
    }
}
```

- Passing `authors: ["App"]` scopes observation to app-originated changes, so server-originated changes aren't replayed back to the server.
- The closure must **touch `eventCounter`** so Swift Observation knows what to track, then calls `processChanges()`, which uses `ModelContext.fetchHistory` to fetch and upload.
- As with the camera controller, the `ObservationTracking.Token` is stored to keep tracking alive for the object's lifetime.

## Next steps (recap)

- Tailor fetches with **sections** (`@Query(sectionBy:)`).
- Use **`@Attribute(.codable)`** for types from other frameworks.
- Use **`ResultsObserver`** to react to query-result changes outside SwiftUI.
- Use **`HistoryObserver`** to react to persistent-history changes.
