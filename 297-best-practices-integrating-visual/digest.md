---
title: "Best practices for integrating visual intelligence in your app — Full Digest"
session: WWDC26 · 297
url: https://developer.apple.com/videos/play/wwdc2026/297/
duration: 18m
speakers: David (ML engineer, System Experience)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 297 — Best practices for integrating visual intelligence in your app

## TL;DR

A practical, build-it-live walkthrough of plugging an app into Visual Intelligence,
framed by a sample music-discovery app. Two integration surfaces, four headline points:

1. **Image Search (you → Visual Intelligence).** Model content as an `AppEntity`,
   answer the system's `IntentValueQuery` (which hands you a captured image's pixel
   buffer), and provide an `OpenIntent` to land users on the right screen. That is the
   whole core loop, and the same code runs on iOS, iPadOS, and macOS.
2. **On-device similarity with Vision.** The recommended matching technique is feature
   prints — `GenerateImageFeaturePrintRequest` → `FeaturePrintObservation`, compared by
   distance. Pre-compute the catalog, threshold, sort by similarity, limit results.
3. **Cross-platform for free.** Entities, query, and OpenIntent carry over unchanged.
   Watch two platform differences: camera (iOS) vs screenshot (Mac/iPad) input, and
   much larger Mac pixel buffers that may need resizing.
4. **System stores (Visual Intelligence → you).** New actions write captured data into
   EventKit, Contacts, and HealthKit. If your app already reads those stores, Visual
   Intelligence becomes a free new input source — just observe change notifications.

New in iOS 26 / this cycle: Visual Intelligence on **iPad and Mac**, plus capturing
**contacts**, **multiple calendar events**, and **medical-device readings**.

---

## 1. Defining your content — App entities

Image Search builds on two frameworks: **App Intents** and **Visual Intelligence**.
The first step is defining what your app returns, modeled as an `AppEntity` (the "nouns"
of your app). For the music app, that is an `AlbumEntity`.

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
```

The `DisplayRepresentation` is the first thing people see in the results sheet, and
space is tight — **about three lines of text** (title + subtitle) plus a thumbnail.
Best practices David calls out:

- Put the **most important identifying info** in title/subtitle (album name + artist).
- If you initialize with an **image URL**, serve a **thumbnail-sized** image, not your
  full-resolution asset — smaller images load faster and still look good in the
  **two-column** layout used for multiple results.
- If you return **only one result**, that image takes the **full width** of the sheet,
  so size accordingly.

## 2. Implementing a query — IntentValueQuery

`IntentValueQuery` is a lightweight protocol that hands entity values to the system.
You may already have one from adopting App Intents for Siri. The Visual Intelligence
twist is the **input**: the system passes a `SemanticContentDescriptor` describing the
captured image, exposing its `pixelBuffer`.

```swift
// Adopt IntentValueQuery to return visual search results
import AppIntents
import VisualIntelligence

struct SearchHandler: IntentValueQuery {
    @Dependency var catalog: AlbumCatalog
    @Dependency var concertFinder: ConcertFinder

    func values(for input: SemanticContentDescriptor) async throws -> [VisualSearchResult] {
        guard let pixelBuffer = input.pixelBuffer else { return [] }
        let albums = try await catalog.search(matching: pixelBuffer)
        return albums.map { VisualSearchResult.album($0) }
    }
}
```

### On-device image similarity with Vision

The matching is done locally against a saved catalog using the **Vision** framework's
pre-trained models. Each catalog entry stores a **feature print** — a compact numerical
representation of an image used for similarity comparison.

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

    private func generateFeaturePrint(for image: CGImage) async throws -> FeaturePrintObservation {
        let request = GenerateImageFeaturePrintRequest()
        return try await request.perform(on: image)
    }
}
```

At query time: convert the pixel buffer to a `CGImage` (VideoToolbox), generate the
query's feature print, compare against pre-computed prints with a **max-distance
threshold**, then **sort by similarity** and **limit** the results.

```swift
// Search the catalog for albums matching the captured image
func search(matching pixelBuffer: CVReadOnlyPixelBuffer, limit: Int = 10, maxDistance: Double = 1.0) async throws -> [AlbumEntity] {
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

Principles that hold whether you search on device or hit a server:

- **Pre-compute** what you can (feature prints) so the query stays fast.
- Return results **fast and ranked** (best match first).
- **Limit** the number of results to keep them relevant.
- **No matches?** Return an empty array — the system renders an empty state for you.

Feature prints are just the surface: Vision can also extract text, scan barcodes,
detect faces, and classify images, all useful for extending visual search.

## 3. Opening results — OpenIntent

When someone taps a result, the system calls your `OpenIntent` with the selected entity.

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

Guidance:

- Take people **straight to the selected content**.
- **Reuse** an existing `OpenIntent` you already have from other App Intents features —
  you do not need a Visual-Intelligence-specific one.
- **Keep it lightweight.** `perform()` runs as the app comes to the foreground, so do
  navigation here and defer heavy loading until after the view appears.

That trio — entity, query, OpenIntent — is a complete basic Image Search integration.
Note that your app appears alongside other adopting providers; the **system decides
ordering** based on which providers are available on the device.

## 4. Mac and iPad adoption

Visual Intelligence is on **iPadOS and macOS** this year, and the same APIs apply with
minimal changes — your `IntentValueQuery`, entities, and `OpenIntent` are literally the
same code. Two differences to design for:

- **Input type.** iOS leans on the **camera** (physical objects: vinyl records, concert
  posters); Mac and iPad lean on **screenshots** (digital media). Make sure your search
  handles both well.
- **Buffer size.** On Mac the input **pixel buffer can be much larger** than on iPhone —
  consider whether **resizing** is needed for your use case.

## 5. Returning multiple result types — @UnionValue

An app can have only **one** `IntentValueQuery` that accepts a `SemanticContentDescriptor`.
To return more than one entity type, use a `@UnionValue` enum.

```swift
// Use UnionValue to return multiple visual search result types
@UnionValue
enum VisualSearchResult {
    case album(AlbumEntity)
    case concert(ConcertEntity)
}
```

Each type needs its own `OpenIntent` (album + concert). The query then matches albums by
image similarity and **derives** nearby concerts from the matched artists — combining
two different kinds of results.

```swift
func values(for input: SemanticContentDescriptor) async throws -> [VisualSearchResult] {
    guard let pixelBuffer = input.pixelBuffer else { return [] }
    let albums = try await catalog.search(matching: pixelBuffer)
    let artists = albums.map { $0.artistName }
    let concerts = await concertFinder.findNearby(byArtists: artists)
    return albums.map { VisualSearchResult.album($0) }
        + concerts.map { VisualSearchResult.concert($0) }
}
```

The lesson: think beyond **matching pixels** — surface related content the captured
context implies (here, image similarity → artist names → nearby concerts).

## 6. Continuing search in your app — semanticContentSearch

When the inline results are not enough, give people a way into your **full** search
experience using the `semanticContentSearch` schema. The system provides the
`semanticContent` property (the same descriptor with the pixel buffer) automatically.

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

Tapping **More results** lands users in the app's full search. **Pre-populate** that
view from the captured context rather than starting from scratch — your app can show far
more than the inline sheet: filters, categories, the full depth of your content.

## 7. System store integrations (receiving data)

The other side of the story: Visual Intelligence **writes** data into shared system
stores that your app reads back. If you already read these stores, Visual Intelligence
becomes a new input source **automatically**:

- **Events** → EventKit (`EKEventStore`)
- **Contacts** → Contacts (`CNContactStore`), e.g. from a business card
- **Medical-device readings** → HealthKit (`HKHealthStore`), e.g. blood-pressure
  monitors, glucose meters, weight scales

For the music app, an `UpcomingConcertManager` requests calendar access, fetches upcoming
events, and filters them against catalog artists:

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
        guard granted else { authorizationStatus = .denied; return }
        authorizationStatus = .fullAccess
        await fetchUpcomingConcerts()
        // ...
    }
}
```

```swift
// Filter for upcoming events that match known artists in our catalog
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
```

The key best practice: **observe store-change notifications** so events created by
Visual Intelligence appear without manual refresh.

```swift
// Observe newly created events
for await _ in NotificationCenter.default.notifications(named: .EKEventStoreChanged) {
    await fetchUpcomingConcerts()
}
```

Demo payoff: capture a social post about a concert → Visual Intelligence detects the
event → add it to the calendar → it shows up in the app's Upcoming Concerts with a
suggestion to start listening. The same pattern applies to Contacts and HealthKit.

## Recap

Two integration points: **provide** results through **Image Search** (entity + query +
OpenIntent, optionally `@UnionValue` and `semanticContentSearch`), and **receive** data
through **system store integrations** (EventKit / Contacts / HealthKit). With Visual
Intelligence now on iOS, iPadOS, and macOS, one integration reaches people across devices.
