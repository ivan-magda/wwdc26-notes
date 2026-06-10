---
title: "Discover new capabilities in the App Intents framework — Full Digest"
session: WWDC26 · 345
url: https://developer.apple.com/videos/play/wwdc2026/345/
duration: 18m
speakers: Moe (App Intents team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 345 — Discover new capabilities in the App Intents framework

## TL;DR

The 2027 App Intents release is a "smooth out the rough edges" update, driven by
developer feature requests, organized into three buckets. Every feature is demonstrated
on the **Landmarks Travel Tracking** sample app from WWDC25's "Get to know App Intents".

1. **Entities get more reach and scale.**
   - `ValueRepresentation` lets an entity export a **structured system type** (like a
     `PlaceDescriptor`) so it flows to Maps for directions, not just to Mail as a file.
   - `RelevantEntities` proactively **suggests content the system has never seen** (a new
     running playlist) with a situational context.
   - `EntityCollection` **skips full entity resolution** by passing only identifiers to
     `perform()` — a one-line change that turns tagging 1000 photos from slow to instant.
   - `SyncableEntity` gives entities a **stable cross-device ID** so Siri can continue a
     conversation on another device.
2. **Parameters get richer.** More native types (`Duration`, `PersonNameComponents`,
   each with a free picker), plus `@UnionValue` so one parameter can accept **multiple
   types**.
3. **Execution gets controllable.** `LongRunningIntent` breaks the **30-second limit**
   with a Live Activity progress UI; `CancellableIntent` adds graceful cleanup; and
   `ExecutionTargets` lets you **pin an intent to a specific process** (main app vs.
   extension).

---

## 1. Share entities across apps — `ValueRepresentation`

Entities (a landmark, a playlist) live inside your app, but people move between apps. An
entity conforming to `Transferable` (CoreTransferable) can already be shared in a known
file/data format — so the travel tracker's "share a trip idea" shortcut hands a landmark
to **Mail** just fine.

But "get directions to that landmark" fails: **Maps needs structured data** (a
coordinate, an address) that has no file/data format. The existing `FileRepresentation`
and `DataRepresentation` only cover known formats like PDFs or images.

`ValueRepresentation` is the new representation type for **structured types the system
already understands**. Export a landmark's coordinate + name as a `PlaceDescriptor` (from
the **GeoToolbox** framework), which carries exactly the metadata Maps needs to navigate.

```swift
struct LandmarkEntity: AppEntity, Transferable {
    var id: Int
    var landmark: Landmark  // contains CLLocationCoordinate2D

    static var transferRepresentation: some TransferRepresentation {
        ValueRepresentation(
            exporting: { entity in
                PlaceDescriptor(
                    representations: [.coordinate(entity.landmark.locationCoordinate)],
                    commonName: entity.landmark.name
                )
            }
        )
    }
}
```

If the entity already exposes a `PlaceDescriptor` `@Property`, skip the closure and use a
**key-path** — same result, much less code:

```swift
static var transferRepresentation: some TransferRepresentation {
    ValueRepresentation(exporting: \.placeDescriptor)
}
```

Now running the shortcut flows the landmark to Maps as a `PlaceDescriptor` and Maps opens
with directions.

## 2. Register relevant entities — `RelevantEntities`

The problem: a music app (CosmoTunes, from "Explore advanced App Intents features for
Siri and Apple Intelligence") ships a **brand-new high-tempo playlist** perfect for
running. How does it land in the Fitness app's suggested playlists when someone starts a
workout?

The two existing surfacing mechanisms both fall short here:

- **Spotlight indexing** — makes content searchable/retrievable (the primary path for
  Siri retrieval, including semantic search). But nobody searches for a playlist they
  don't know exists.
- **Interaction donation** (`IntentDonationManager`) — the system learns from actions
  people take and suggests repeats. But nobody has played the playlist yet, so there's
  nothing to donate.

`RelevantEntities` fills the gap: **proactively hint** which content is relevant, with a
context describing *when and why*.

```swift
let playlistEntities = [dailyRun, runningMix]
let workoutContext = AppEntityContext.audio(.workout(activityType: .running))

try await RelevantEntities.shared.updateEntities(
    playlistEntities, for: workoutContext
)
```

Entities stay registered until removed. You can clear by context, by entity, or
everything:

```swift
try await RelevantEntities.shared.removeAllEntities(for: workoutContext)
try await RelevantEntities.shared.removeEntities(playlistEntities, from: workoutContext)
try await RelevantEntities.shared.removeAllEntities()
```

**How to choose:** Spotlight for searchable/Siri-retrievable content; interaction
donation to teach the system usage patterns to repeat; `RelevantEntities` to hint
situational relevance for the right moment.

## 3. Handle entities efficiently — `EntityCollection`

The travel tracker added a `PhotoEntity` (with the photos app schema) and a tagging
intent. Tagging many photos at once got slow.

The reason is **parameter resolution**: before an intent runs, the system resolves every
entity — calling the entity query to populate all its properties. Great when you need the
full entity, but tagging only needs the **ID** to update the data model, so resolving
thousands of photos is wasted work.

`EntityCollection` stores an array of **identifiers** rather than resolved entities. As a
parameter type, the system passes just the IDs to `perform()` — no resolution.

```swift
struct TagPhotosIntent: AppIntent {
    static let title: LocalizedStringResource = "Tag Travel Photos"

    @Parameter var photos: EntityCollection<PhotoEntity>   // was: [PhotoEntity]
    @Parameter var tag: String

    func perform() async throws -> some IntentResult {
        modelData.tagPhotos(ids: photos.identifiers, tag: tag)   // was: tagPhotos(photos, tag: tag)
        return .result()
    }
}
```

A one-line parameter-type change. The demo: tagging 1000 photos went from noticeably slow
(array of `PhotoEntity`) to **almost instant** (`EntityCollection`).

## 4. Use entities across devices — `SyncableEntity`

In the 2027 releases **Siri can continue conversations across devices**, and entities can
be part of that. The catch: entity IDs are often generated **locally per device**, so the
same logical entity gets a different ID on each device. Ask Siri on iPhone to add a photo
to an album, then ask another device to tag "that photo" — Siri may not find it.

Siri needs a **stable ID** that's identical everywhere (a server UUID or a CloudKit record
ID). `SyncableEntity` declares that an entity's ID is stable and cross-device safe.

```swift
// Already stable across devices — nothing else to do:
struct PhotoEntity: AppEntity, SyncableEntity {
    var id: Int
}

// Local IDs (e.g. CoreData row IDs)? Pair a local + a stable ID:
struct PhotoEntity: AppEntity, SyncableEntity {
    var id: SyncableEntityIdentifier<String, String>

    init(localID: String, stableID: String) {
        self.id = SyncableEntityIdentifier(local: localID, stable: stableID)
    }
}
```

`SyncableEntityIdentifier` pairs the two: **on-device your code uses the local ID; across
devices the system uses the stable one.**

## 5. Richer parameter types

Declaring a `@Parameter` already gives you a native picker, Siri understanding, and
localization for free. The 2027 release extends that to **more native types**:

- `Duration` — no more building custom time pickers.
- `PersonNameComponents` — structured name input instead of a plain `String`.
- "And more."

Each gets a native picker and works everywhere the intent does — Siri, Shortcuts, and
Widgets.

### Union value parameters — `@UnionValue`

When a single parameter needs to accept **more than one type**, a union value is a Swift
enum where each case wraps a different type. The travel tracker wants **one widget** that
shows photos from either a photo album or a landmark collection.

```swift
@UnionValue
enum TravelGalleryContent {
    case landmarkCollection(LandmarkCollectionEntity)
    case photoAlbum(PhotoAlbumEntity)

    static let typeDisplayRepresentation: TypeDisplayRepresentation = "Travel Gallery"
    static let caseDisplayRepresentations: [Cases: DisplayRepresentation] = [
        .landmarkCollection: "Landmark Collection",
        .photoAlbum: "Photo Album"
    ]
}
```

The `@UnionValue` macro generates type information, case metadata, and picker support.
`typeDisplayRepresentation` labels the overall type; `caseDisplayRepresentations` maps
each case to its picker name. Works everywhere the intent does, including Shortcuts.

## 6. Extend execution — `LongRunningIntent` + `CancellableIntent`

Intents run from Siri/Shortcuts/any system surface get **30 seconds** to finish. Fine for
everyday actions, but the travel tracker's "upload large photos to a shared album"
(triggered by a widget button) kept failing the limit.

`LongRunningIntent` runs **beyond 30 seconds**, manages the app's background task
lifecycle, and surfaces progress automatically as a **Live Activity** (with a stop
button). It builds on `ProgressReportingIntent`, so you get a `progress` object for free;
wrap the work in `performBackgroundTask`.

`CancellableIntent` adds an `onCancel` handler so you can clean up gracefully — whether
the user tapped stop, the system timed out, or it needed to reclaim resources. The handler
receives the **reason**.

```swift
struct UploadPhotoIntent: LongRunningIntent, CancellableIntent {
    static let title: LocalizedStringResource = "Upload Photo"

    @Parameter var photo: IntentFile

    func perform() async throws -> some IntentResult & ProvidesDialog {
        let result = try await performBackgroundTask {
            let chunks = calculateChunks(for: photo)
            progress.totalUnitCount = Int64(chunks)

            for chunk in 1...chunks {
                try Task.checkCancellation()
                try await uploadChunk(chunk)
                progress.completedUnitCount = Int64(chunk)
            }
            return "Upload complete!"
        } onCancel: { reason in
            cleanup(for: reason)
        }
        return .result(dialog: "\(result)")
    }
}
```

`LongRunningIntent` also supports **background GPU access** on supported devices (photo
processing, on-device inference) — add the GPU access entitlement. (Background-task
mechanics: see a WWDC25 background tasks video.)

## 7. Target the right process — `ExecutionTargets`

As an app grows, intents may move into a **Widget extension** or an **App Intents
extension** (lightweight processes that handle requests without launching the app). It's
common to put intents/entities/queries in a **shared Swift package** imported by both the
app and its extensions — exactly what the travel tracker does.

When intents live in a shared package, the system decides which process runs each one via
heuristics (prefer the app if it's already running; otherwise launch the extension).
Sometimes that's wrong: a widget "favorite" button needs the **main app** to write,
because the widget only has read-only access to the shared data store and two writers
would conflict.

`ExecutionTargets` overrides the heuristics:

```swift
struct UpdateFavoriteIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { .main }
}

struct DownloadPhotoIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { .appIntentsExtension }
}

struct GetLandmarkStatusIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { .widgetKitExtension }
}

struct TagPhotosIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { [.main, .appIntentsExtension] }
}
```

Target the main app, an App Intents extension, a WidgetKit extension, or any combination.
