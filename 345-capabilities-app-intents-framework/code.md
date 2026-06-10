# Code samples — Session 345

Extracted from the Code tab of the session page. Labels derived from snippet content;
timecodes map to the transcript chapters.

## ValueRepresentation — export an entity as a structured type (closure + key-path)

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

// If the entity already has a PlaceDescriptor property, use a key-path — much less code:
struct LandmarkEntity: AppEntity, Transferable {
    var id: Int
    @Property var placeDescriptor: PlaceDescriptor

    static var transferRepresentation: some TransferRepresentation {
        ValueRepresentation(exporting: \.placeDescriptor)
    }
}
```

## RelevantEntities — register and remove suggestions with a context

```swift
// Suggest playlists for the workout session
let playlistEntities = [dailyRun, runningMix]
let workoutContext = AppEntityContext.audio(.workout(activityType: .running))

try await RelevantEntities.shared.updateEntities(
    playlistEntities, for: workoutContext
)

// Clear all entities for a context
try await RelevantEntities.shared.removeAllEntities(for: workoutContext)

// Remove specific entities from a context
try await RelevantEntities.shared.removeEntities(playlistEntities, from: workoutContext)

// Or remove all entities across all contexts
try await RelevantEntities.shared.removeAllEntities()
```

## EntityCollection — pass identifiers to perform() without full resolution

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

## SyncableEntity — stable IDs across devices

```swift
// If your ID is already stable across devices (server UUID, CloudKit record ID):
struct PhotoEntity: AppEntity, SyncableEntity {
    var id: Int  // Already stable across devices — that's it
}

// If you use local IDs, pair a local and a stable ID:
struct PhotoEntity: AppEntity, SyncableEntity {
    var id: SyncableEntityIdentifier<String, String>

    init(localID: String, stableID: String) {
        self.id = SyncableEntityIdentifier(local: localID, stable: stableID)
    }
}
```

## @UnionValue — one parameter accepting multiple entity types

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

## LongRunningIntent + CancellableIntent — extended execution with progress and cleanup

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

## ExecutionTargets — pin an intent to a specific process

```swift
// Write operation — needs the main app
struct UpdateFavoriteIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { .main }
}

// Standalone download — runs in the extension
struct DownloadPhotoIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { .appIntentsExtension }
}

// Display-only — runs in the widget extension
struct GetLandmarkStatusIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { .widgetKitExtension }
}

// Works in either — lets the system choose
struct TagPhotosIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { [.main, .appIntentsExtension] }
}
```

---

## Useful API facts surfaced by the code

- `ValueRepresentation` is a `TransferRepresentation` case alongside `FileRepresentation`
  / `DataRepresentation`; supports an `exporting:` closure or a key-path.
- `RelevantEntities.shared` exposes `updateEntities(_:for:)`,
  `removeAllEntities(for:)`, `removeEntities(_:from:)`, and `removeAllEntities()`.
- Contexts come from `AppEntityContext` (e.g. `.audio(.workout(activityType:))`).
- `EntityCollection<E>` exposes `.identifiers` instead of resolved entities.
- `SyncableEntityIdentifier<Local, Stable>` pairs a local and stable ID
  (`init(local:stable:)`).
- `@UnionValue` enum cases carry `typeDisplayRepresentation` and a
  `caseDisplayRepresentations` map keyed by the macro-generated `Cases`.
- `LongRunningIntent` builds on `ProgressReportingIntent` (free `progress` object) and
  wraps work in `performBackgroundTask { } onCancel: { reason in }`.
- `CancellableIntent` adds the `onCancel` reason handler.
- `ExecutionTargets` values: `.main`, `.appIntentsExtension`, `.widgetKitExtension`, or
  an array combination; set via `static var allowedExecutionTargets`.
