# Code samples — Session 297

Extracted from the Code tab of the session page. Timestamps map to the transcript chapters.

## Define the album content as an App entity

```swift
// Define the content you want to return as an App Entity
import AppIntents

struct AlbumEntity: AppEntity {
    var id: String
    @Property var name: String
    @Property var artistName: String
    var coverArtData: Data

    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(
            title: "\(name)",
            subtitle: "\(artistName)",
            image: .init(data: coverArtData)
        )
    }

    static let defaultQuery = AlbumEntityQuery()

    static var typeDisplayRepresentation: TypeDisplayRepresentation { "Album" }
}

struct AlbumEntityQuery: EntityQuery {
    @Dependency var catalog: AlbumCatalog
    func entities(for identifiers: [String]) async throws -> [AlbumEntity] {
        catalog.albums(for: identifiers)
    }
}
```

## Adopt IntentValueQuery to return visual search results

```swift
// Adopt IntentValueQuery to return visual search results
import AppIntents
import VisualIntelligence

struct SearchHandler: IntentValueQuery {
    @Dependency var catalog: AlbumCatalog
    @Dependency var concertFinder: ConcertFinder

    func values(for input: SemanticContentDescriptor) async throws -> [VisualSearchResult] {
        guard let pixelBuffer = input.pixelBuffer else {
            return []
        }

        let albums = try await catalog.search(matching: pixelBuffer)

        return albums.map { VisualSearchResult.album($0) }
    }
}
```

## Build a catalog with precomputed feature prints (Vision)

```swift
// Build a catalog of albums with precomputed feature prints
import Vision

@Observable
class AlbumCatalog {
    static let shared = AlbumCatalog()

    struct CatalogEntry: Sendable {
        let album: AlbumEntity
        let featurePrint: FeaturePrintObservation
    }

    private(set) var entries: [CatalogEntry] = []

    private func generateFeaturePrint(
        for image: CGImage
    ) async throws -> FeaturePrintObservation {
        let request = GenerateImageFeaturePrintRequest()
        let result = try await request.perform(on: image)
        return result
    }
}
```

## Search the catalog for matching albums (feature-print distance)

```swift
// Search the catalog for albums matching the captured image
func search(matching pixelBuffer: CVReadOnlyPixelBuffer, limit: Int = 10, maxDistance: Double = 1.0) async throws ->
[AlbumEntity] {
    var cgImage: CGImage?
    _ = pixelBuffer.withUnsafeBuffer { VTCreateCGImageFromCVPixelBuffer($0, options: nil, imageOut: &cgImage) }
    guard let cgImage else { return [] }

    let queryPrint = try await generateFeaturePrint(for: cgImage)

    return try entries.compactMap { entry -> (album: AlbumEntity, distance: Double)? in
        let distance = try queryPrint.distance(to: entry.featurePrint)
        guard distance <= maxDistance else { return nil }
        return (entry.album, distance)
    }
    .sorted { $0.distance < $1.distance }
    .prefix(limit)
    .map { $0.album }
}
```

## OpenIntent to land users on the right screen

```swift
// Create an open intent to land users on the right screen
import AppIntents

struct OpenAlbumIntent: OpenIntent {
    static let title: LocalizedStringResource = "Open Album"

    @Parameter(title: "Album")
    var target: AlbumEntity

    @Dependency var appState: AppState

    func perform() async throws -> some IntentResult {
        await appState.openAlbum(id: target.id)
        return .result()
    }
}
```

## UnionValue for multiple result types + concert OpenIntent

```swift
// Use UnionValue to return multiple visual search result types
@UnionValue
enum VisualSearchResult {
    case album(AlbumEntity)
    case concert(ConcertEntity)
}

struct OpenConcertIntent: OpenIntent {
    static let title: LocalizedStringResource = "Open Concert"

    @Parameter(title: "Concert")
    var target: ConcertEntity

    @Dependency var appState: AppState

    func perform() async throws -> some IntentResult {
        await appState.openConcert(id: target.id)
        return .result()
    }
}
```

## Expand the query to return the UnionValue (albums + nearby concerts)

```swift
// Expand the IntentValueQuery to return the UnionValue
struct SearchHandler: IntentValueQuery {
    @Dependency var catalog: AlbumCatalog
    @Dependency var concertFinder: ConcertFinder

    func values(for input: SemanticContentDescriptor) async throws -> [VisualSearchResult] {
        guard let pixelBuffer = input.pixelBuffer else {
            return []
        }

        let albums = try await catalog.search(matching: pixelBuffer)

        let artists = albums.map { $0.artistName }

        let concerts = await concertFinder.findNearby(byArtists: artists)

        return albums.map { VisualSearchResult.album($0) }
            + concerts.map { VisualSearchResult.concert($0) }
    }
}
```

## Continue into in-app search (semanticContentSearch schema)

```swift
// Provide a link to in-app search
@AppIntent(schema: .visualIntelligence.semanticContentSearch)
struct SemanticContentSearchIntent: AppIntent {
    static let title: LocalizedStringResource = "Search in app"
    static let openAppWhenRun: Bool = true

    var semanticContent: SemanticContentDescriptor
    @Dependency var catalog: AlbumCatalog
    @Dependency var concertFinder: ConcertFinder
    @Dependency var appState: AppState

    func perform() async throws -> some IntentResult {
        guard let pixelBuffer = semanticContent.pixelBuffer else { return .result() }
        let albums = try await catalog.search(matching: pixelBuffer)
        let artists = albums.map { $0.artistName }
        let concerts = await concertFinder.findNearby(byArtists: artists)
        await appState.openSearch(albums: albums, concerts: concerts)
        return .result()
    }
}
```

## Request calendar access and fetch upcoming concerts (EventKit)

```swift
// Request calendar access and fetch upcoming concerts
import EventKit

@Observable
class UpcomingConcertManager {
    private let eventStore = EKEventStore()
    var upcomingConcerts: [EKEvent] = []
    var authorizationStatus: EKAuthorizationStatus = .notDetermined

    func requestAccessAndFetch() async throws {
        let granted = try await eventStore.requestFullAccessToEvents()
        guard granted else {
            authorizationStatus = .denied
            return
        }
        authorizationStatus = .fullAccess
        await fetchUpcomingConcerts()

        // ...
    }
}
```

## Filter upcoming events against known catalog artists

```swift
// Filter for upcoming events that match known artists in our catalog
class UpcomingConcertManager {
    func fetchUpcomingConcerts() async {
        let predicate = eventStore.predicateForEvents(
            withStart: .now,
            end: .now.addingTimeInterval(90 * 24 * 60 * 60),
            calendars: nil
        )

        let events = eventStore.events(matching: predicate)

        upcomingConcerts = events.filter { event in
            AlbumCatalog.shared.entries.contains { entry in
                event.title?.localizedCaseInsensitiveContains(entry.album.artistName) == true
            }
        }
    }
}
```

## Observe newly created events (store-change notifications)

```swift
// Observe newly created events
@Observable
class UpcomingConcertManager {
    // ...

    func requestAccessAndFetch() async throws {
        // ...

        for await _ in NotificationCenter.default
            .notifications(
                named: .EKEventStoreChanged
            ) {
            await fetchUpcomingConcerts()
        }
    }
}
```

---

## Useful API facts surfaced by the code

- Image Search integration pulls in two frameworks: `AppIntents` + `VisualIntelligence`.
- `IntentValueQuery.values(for:)` receives a `SemanticContentDescriptor`; the captured image is on its `pixelBuffer` (a `CVReadOnlyPixelBuffer`).
- Vision: `GenerateImageFeaturePrintRequest` → `FeaturePrintObservation`; `.distance(to:)` measures similarity (lower = closer). Pre-compute catalog prints; threshold + sort + limit at query time.
- `pixelBuffer` → `CGImage` via VideoToolbox `VTCreateCGImageFromCVPixelBuffer` (inside `withUnsafeBuffer`).
- `@UnionValue` enum lets one `IntentValueQuery` return multiple entity types; each type needs its own `OpenIntent`.
- In-app continuation uses `@AppIntent(schema: .visualIntelligence.semanticContentSearch)` with `openAppWhenRun = true` and a system-provided `semanticContent` property.
- System stores read by the app: `EKEventStore` (EventKit), `CNContactStore` (Contacts), `HKHealthStore` (HealthKit). Observe `.EKEventStoreChanged` (and equivalents) so Visual-Intelligence-written data appears automatically.
