---
title: "Code-along: Add persistence with SwiftData — Full Digest"
session: WWDC26 · 275
url: https://developer.apple.com/videos/play/wwdc2026/275/
duration: 22m
speakers: Matthew Turk
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 275 — Code-along: Add persistence with SwiftData

## TL;DR

A guided refactor that converts the **Wishlist** sample app — a SwiftUI travel-planner
whose state lives in an in-memory `DataSource` and evaporates on relaunch — into a
SwiftData-backed app. The arc is a clean three-step adoption story:

1. **Identify relevant state** — what in-memory data (trip collections, goal statuses,
   the `DataSource`) deserves to be persistent and routed through a `ModelContext`.
2. **Define your schemas** — turn `@Observable` types into `@Model` types, satisfy the
   `var` + `Codable` requirements, refactor a closed `enum Goal` into a `@Model` class
   hierarchy using **model inheritance**, and rethink image storage.
3. **Define model relationships** — declare the Trip → [Activity] to-many relationship
   with `@Relationship`, delete the now-redundant `DataSource` / `TripEditModel`
   ("hundreds of lines removed"), and attach `.modelContainer`.

Then the **view layer** is rewired: each subview swaps its environment `DataSource` for
a targeted `@Query` / `FetchDescriptor`, autosave handles writes, runtime errors are
surfaced with `.alert(error:)`, and a property-observer side effect is re-implemented
with the new **`withContinuousObservation`** API. The recurring teaching theme is
**memory vs. disk**: fetch only what a view needs (the "librarian" analogy) rather than
loading everything into RAM.

---

## 1. The starting point and the problem (0:00–3:16)

Wishlist organizes trips into seasonal collections, tracks per-trip activities, and
awards goal badges; a third tab searches trips and activities. Today every view reads a
`DataSource` from the SwiftUI environment, and `DataSource` filters/sorts all trips,
goals, and search results **in memory, on demand**. It works for a small demo, but:

- It leans on RAM both to process *and* to store data — not a stable home for
  user-created content.
- Add a "Northern Lights" trip, kill the app, relaunch → it's gone, reset to
  preinstalled content.

The fix is SwiftData: connect that identified state (trip collections, goal statuses,
search results) to a persistent store through a **model context**.

## 2. Define your schemas (3:17–9:40)

### Activity: @Observable → @Model

The mechanical step: `import SwiftData` and replace `@Observable` with `@Model`.
SwiftData **auto-generates Observable conformance**, so views keep updating.

```swift
import Foundation
import SwiftData

// SwiftData automatically generates Observable conformance
@Model
class Activity {
    var name: String
    var isComplete: Bool = false
    var dateCreated = Date.now
    var dateEdited = Date.now
}
```

A subtlety: the original `Activity` used `didSet` observers on `name` and `isComplete`
to bump `dateEdited` (a nice key for sort/filter). Property observers and computed
properties "are not always compatible" under `@Model`, so the observers are **removed
for now** and re-added later via `withContinuousObservation` (§7).

### Trip: the var + Codable requirements

Same macro swap, but the build surfaces SwiftData's model rules:

- **`creationDate` must be mutable** — change `let` to `var` so SwiftData can populate
  it from the database at load time.
- **Every model property must be `Codable`** so it can be serialized into a database
  column. `TripCollection` gets explicit `Codable` conformance.

```swift
enum TripCollection: String, CaseIterable, RawRepresentable, Codable {
    case springEscapes
    case summerVibes
    case fallGetaways
    case winterRetreats
}
```

### Goal: from a closed enum to a @Model class hierarchy

`Goal` is the non-trivial conversion. It was an **`enum` with exactly 18 cases** — a
closed set defined ahead of time. A persistent model needs a **class** (storable
properties, instantiable any number of times). The refactor:

- Make `Goal` a class with `name`, `kind`, `targetCount`.
- Add **stored** progress properties `completedCount` and `isComplete` (true when
  `completedCount >= target`). Storing `isComplete` rather than computing it makes it
  usable in query predicates that separate achieved vs. upcoming goals.
- Replace the `kind` distinction (trip-completion vs. activity-completion) with
  **SwiftData model inheritance**: drop `kind`, introduce `TripGoal` and `ActivityGoal`
  subclasses of `Goal`. (Analogy used: spiral vs. lenticular galaxies sharing a galaxy
  superclass.)
- Pointer for the inheritance details: **"SwiftData: Dive into inheritance and schema
  migration" (WWDC 2025).**

## 3. Define model relationships (9:41–13:32)

Each Trip has many Activities — a **to-many relationship**. The old code faked this with
dictionaries and array-looping functions (e.g. a Trip→Activity map keyed by activity
ID). The idiomatic SwiftData replacement is an array property plus `@Relationship`:

```swift
import Foundation
import SwiftData

@Model
class Trip {
    var name: String
    var collection: TripCollection
  
    var photo: TripImage
    var thumbnailData: Data?
  
    @Relationship(deleteRule: .cascade, inverse: \Activity.trip)
    var activities: [Activity] = []
  
    private(set) var creationDate = Date.now
    var subtitle: String?
    var isComplete: Bool = false
}
```

- `deleteRule: .cascade` means deleting a Trip also deletes its Activities; `inverse:`
  wires the back-reference on `Activity.trip`.

### Image storage: thumbnail inline, full-res as an external reference

The old `photoURL` was a raw file path — fragile (breaks if the file moves/renames) and
it eagerly implies loading full-resolution images. The new design splits storage:

- **`thumbnailData: Data?`** — a low-res cache whose raw bytes are **inlined** in the
  database, cheap to read when scrolling a carousel.
- **Full-resolution image** lives in a separate **`TripImage` `@Model`** stored as a
  **persistent external file reference** (loaded only when a detail view needs it). The
  `photoURL` property is renamed to `photo` (multi-cursor / refactor-rename across
  files).

### Deleting redundant infrastructure + the container

With models, queries, and relationships in place, `TripEditModel` (views now bind
directly to models) and `DataSource` (its filtering/sorting/search/relationship-
traversal is now automatic) are deleted — "hundreds of lines of code" gone. Finally the
model layer is finished by attaching the container to the scene:

```swift
import SwiftUI
import SwiftData

@main
struct WishlistApp: App {
    let container: ModelContainer = {
        do {
            let modelContainer = try ModelContainer(for: Trip.self, Activity.self, TripImage.self, Goal.self, TripGoal.self, ActivityGoal.self)
            try SampleData.seedIfNeeded(in: modelContainer.mainContext)
            return modelContainer
        } catch {
            fatalError("Could not create model container: \(error)")
        }
    }()

    var body: some Scene {
        WindowGroup {
            ContentView()
                .preferredColorScheme(.dark)
        }
        .modelContainer(container)
    }
}
```

## 4. Memory vs. disk: the core mental model (13:33–16:11)

With the schema and `.modelContainer` in place, **autosave is on by default**. Before
touching views, the talk lays out two principles:

- A **`FetchDescriptor`** is how you *plan* which models to load.
- The store lives **outside your app's address space** (local DB file or remote server)
  and can be **orders of magnitude slower** than memory — so decide which data needs to
  be where and when.

Old global arrays like `allGoals` were compiled into the binary: fast, but they inflate
memory for the app's whole lifetime and (being in-binary) can't capture user edits.
With SwiftData you insert/update/save through the context, then fetch back. Two fetch
styles, via the **librarian analogy**:

- **Fetch-all-then-filter** — less standing memory but more I/O (asking the librarian to
  bring *every* book so you can pick your author's).
- **Fetch-with-predicate** — ask the librarian up front for exactly what you want. This
  is what the `@Query` macro with a predicate does, and the view auto-updates when the
  result changes (equivalent to `context.fetch`).

## 5. Rewiring the views with @Query (16:12–19:38)

Each subview drops its environment `DataSource` and gains a targeted query.

**GoalsView** — two predicate queries split achieved vs. upcoming goals:

```swift
@Query(filter: #Predicate<Goal> { $0.isAchieved }, sort: \Goal.dateAchieved, order: .reverse)
private var achievedGoals: [Goal]

@Query(filter: #Predicate<Goal> { !$0.isAchieved }, sort: \Goal.sortOrder)
private var upcomingGoals: [Goal]
```

**RecentTripsPageView** — a `FetchDescriptor` with `fetchLimit: 5`, reverse-chronological,
feeding a `ForEach`:

```swift
@Query(FetchDescriptor<Trip>(sortBy: [SortDescriptor(\Trip.creationDate, order: .reverse)], fetchLimit: 5))
private var trips: [Trip]
```

**TripCollectionView** — one instance per season, so the query is declared as a property
and **constructed in the initializer**, capturing the `tripCollection` parameter inside
the predicate before it goes to the database:

```swift
init(tripCollection: TripCollection, cardSize: TripCard.Size, namespace: Namespace.ID) {
    _trips = Query(filter: #Predicate<Trip> { $0.collection == tripCollection }, sort: \Trip.name)
    self.tripCollection = tripCollection
    self.cardSize = cardSize
    self.namespace = namespace
}
```

**SearchResultsListView** — the parent owns the search text and passes it in; the
initializer builds predicates from it. Empty text → three most recent trips and no
activities; non-empty → `localizedStandardContains` predicates over trips and over
activities that belong to a trip. The empty-state `ContentUnavailableView` overlay now
checks `trips.isEmpty && activities.isEmpty` directly instead of a `DataSource` flag.

```swift
init(searchText: String, namespace: Namespace.ID) {
    self.searchText = searchText
    self.namespace = namespace

    if searchText.isEmpty {
        _trips = Query(FetchDescriptor(sortBy: [SortDescriptor(\Trip.creationDate, order: .reverse)], fetchLimit: 3))
        _activities = Query(filter: #Predicate<Activity> { _ in false })
    } else {
        let tripSearchPredicate = #Predicate<Trip> { $0.name.localizedStandardContains(searchText) }
        _trips = Query(filter: tripSearchPredicate, sort: \Trip.name)
        let activitySearchPredicate = #Predicate<Activity> { $0.trip != nil && $0.name.localizedStandardContains(searchText) }
        _activities = Query(filter: activitySearchPredicate, sort: \Activity.name)
    }
}
```

The payoff: add "Northern Lights," relaunch, and the trip is still there.

## 6. Surfacing runtime errors (19:39–20:11)

`updateGoalAchievements()` (in `ActivityItemView`) mutates progress as activities are
completed and can throw — e.g. low disk capacity or an unsupported predicate. The
pattern: catch into a `@State` error, forward it to telemetry (`reportError`), and
present an `.alert(error:)` so users can recover.

```swift
.onDisappear {
    do {
        try updateGoalAchievements()
    } catch {
        updateError = error
        reportError(error)
    }
}
.alert(error: $updateError) {
    // Customize the presentation of the error
}
```

## 7. Re-enabling dateEdited with withContinuousObservation (20:12–21:46)

The bug: with the `didSet` observers gone, checking off an activity no longer bumps
`dateEdited`, so a "Date Edited" sort doesn't reorder the row — no error, just stale UI.
The fix uses the new **`withContinuousObservation`** function (added to the Observation
framework in the "2027 releases" — see Open Questions), set up in `ActivityItemView`'s
initializer, the place where activities are edited:

```swift
init(activity: Activity, isLast: Bool, isEditing: Bool) {
    activity.token = withContinuousObservation(options: .didSet) { event in
        _ = activity.name
        _ = activity.isComplete

        if event.matches(\Activity.name) {
            activity.dateEdited = .now
        }

        if event.matches(\Activity.isComplete) {
            activity.dateEdited = .now
            activity.trip?.isComplete = activity.trip?.activities.isEmpty == false
            && activity.trip?.activities.allSatisfy { $0.isComplete } == true
        }
    }
    self.activity = activity
    self.isLast = isLast
    self.isEditing = isEditing
}
```

Two side effects in one observer: bump `dateEdited` on `name`/`isComplete` changes
(triggering the query to re-sort), and roll the activity's completion up to the parent
Trip's `isComplete` (true only when the trip has activities and *all* are complete). The
returned token is retained on the activity to keep the observation alive.

## 8. Takeaways (21:47–end)

- Start from the **appropriate representation of your app's state** and declare the
  `@Model` types of your schema.
- Write **targeted queries with predicates** to balance memory vs. on-disk storage.
- Keep adjusting your SwiftUI views for **interoperability and extensibility** with
  SwiftData as the app grows.
