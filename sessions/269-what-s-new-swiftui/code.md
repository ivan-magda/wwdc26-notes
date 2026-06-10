# Code samples — Session 269

Extracted from the Code tab. Ordered to follow the talk's chapters.

## Inactive-window dimming with `appearsActive`

```swift
struct SidebarFooterView: View {
    @Environment(\.appearsActive) private var appearsActive

    var body: some View {
        MyAccountView()
            .opacity(appearsActive ? 1 : 0.5)
    }
}
```

## Forcing a menu-bar icon to show with `labelStyle(.titleAndIcon)`

```swift
CommandMenu("Stickers") {
    Button { openStore() } label: {
        Label("Store", systemImage: "bag.fill")
            .labelStyle(.titleAndIcon)
        }
    }
    // Other menu items
}
```

## Prominent tab role

```swift
TabView {
    Tab { EventsTab() }
    Tab { HolidaysTab() }
    Tab { FunTab() }

    Tab(role: .prominent) {
        CartTab()
    }
}
```

## Toolbar item visibility priority, overflow menu, and pinned placement

```swift
StickerPageView()
    .toolbar {
        ToolbarItemGroup {
            UndoButton()
            RedoButton()
        }
        .visibilityPriority(.high)
        ToolbarOverflowMenu {
            ChoosePhotoButton()
            ExportAsImageButton()
            ClearAllStickersButton()
        }
        ToolbarItem(placement: .topBarPinnedTrailing) {
            ShareButton()
        }
    }
```

## Minimize the toolbar when scrolling

```swift
ScrollView {
    StickerListView()
}
.toolbarMinimizeBehavior(.onScrollDown, for: .navigationBar)
```

## DocumentCreationSource — custom new-document flows

```swift
// Use the context to create a document

@main
struct Stickers: App {
    var body: some Scene {
        DocumentGroupLaunchScene("Create a Sticker Page") {
            NewDocumentButton("New Sticker Page", source: .blank)
            NewDocumentButton("Sticker Page from Photo…", source: .photo)
        }

        DocumentGroup { /* ... */ }
    }
}

extension DocumentCreationSource {
    static let blank = Self(id: "blank")
    static let photo = Self(id: "photo")
}
```

## DocumentGroup wiring the document view and creation closure

```swift
@main
struct Stickers: App {
    var body: some Scene {
        DocumentGroupLaunchScene("Create a Sticker Page") {
            NewDocumentButton("New Sticker Page", source: .blank)
            NewDocumentButton("Sticker Page from Photo…", source: .photo)
        }

        DocumentGroup { document in
            StickerPageDocumentView(document)
        } { configuration, context in
            StickerPageDocument(configuration: configuration, context: context)
        }
    }
}
```

## DocumentGroup alongside a WindowGroup

```swift
@main
struct Stickers: App {
    var body: some Scene {
        DocumentGroup { /* ... */ }
        WindowGroup { /* ... */ }
    }
}
```

## Observable document class

```swift
@Observable
final class StickerDocument {
    // ...
}
```

## Declaring writable document types (custom UTType)

```swift
@Observable
final class StickerDocument {

    static let writableDocumentTypes: [UTType] = [.stickerDocument]

    // ...
}

import UniformTypeIdentifiers

extension UTType {
    static let stickerDocument = UTType(exportedAs: "stickerdocument")
}
```

## Snapshot method (returns content for writing)

```swift
@Observable
final class StickerDocument {

    static let writableDocumentTypes: [UTType] = [.stickerDocument]

    @MainActor
    func snapshot(contentType: UTType) async throws -> sending PageSnapshot { /* ... */ }

    // ...
}
```

## PageSnapshot value type

```swift
struct PageSnapshot {
    var background: Image
    var metadata: StickerPlacements
    var stickers: [Image]
}

struct StickerPlacements { /* ... */ }
```

## Providing the Writer from the document

```swift
@Observable
final class StickerDocument {

    static let writableDocumentTypes: [UTType] = [.stickerDocument]

    @MainActor
    func snapshot(contentType: UTType) async throws -> sending PageSnapshot {
        makeSnapshot()
    }

    func writer(configuration: sending WriteConfiguration) -> sending Writer {
        Writer(contentType: configuration.contentType)
    }
}
```

## DocumentWriter — Snapshot type alias

```swift
struct Writer<Snapshot>: DocumentWriter {
    typealias Snapshot = PageSnapshot

    // ...
}
```

## DocumentWriter — storing the content type

```swift
struct Writer<Snapshot>: DocumentWriter {
    typealias Snapshot = PageSnapshot

    let contentType: UTType

    // ...
}
```

## DocumentWriter — nonisolated async write method

```swift
struct Writer<Snapshot>: DocumentWriter {
    typealias Snapshot = PageSnapshot

    let contentType: UTType

    nonisolated func write(
        snapshot: sending PageSnapshot, to destination: URL,
        previous: sending PageSnapshot?, progress: consuming Subprogress
    ) async throws {
        // write .stickerDocument
    }
}
```

## DocumentWriter — reporting progress

```swift
struct Writer<Snapshot>: DocumentWriter {
    typealias Snapshot = PageSnapshot

    let contentType: UTType

    nonisolated func write(
        snapshot: sending PageSnapshot, to destination: URL,
        previous: sending PageSnapshot?, progress: consuming Subprogress
    ) async throws {
        // report progress…
        // write .stickerDocument
    }
}
```

## ReadableDocument conformance

```swift
extension StickerDocument: ReadableDocument {

}
```

## WritableDocument with multiple content types

```swift
@Observable
final class StickerDocument: WritableDocument {

    static let writableContentTypes: [UTType] = [.stickerDocument, .png]
}
```

## Branching the writer per content type

```swift
struct Writer<Snapshot>: DocumentWriter {
    typealias Snapshot = PageSnapshot

    let contentType: UTType

    nonisolated func write(
        snapshot: sending PageSnapshot, to destination: URL,
        previous: sending PageSnapshot?, progress: consuming Subprogress
    ) async throws {
        if contentType.conforms(to: .stickerDocument) {
            // write .stickerDocument
        } else if contentType.conforms(to: .png)

    }
}
```

## PNG export via Core Graphics

```swift
struct Writer<Snapshot>: DocumentWriter {
    typealias Snapshot = PageSnapshot

    let contentType: UTType

    nonisolated func write(
        snapshot: sending PageSnapshot, to destination: URL,
        previous: sending PageSnapshot?, progress: consuming Subprogress
    ) async throws {
        if contentType.conforms(to: .stickerDocument) {
            // write .stickerDocument
        } else if contentType.conforms(to: .png) {
            let context = CGContext(/* ... */)
            context.draw(/* ... */)
        }
    }
}
```

## Reorderable List

```swift
List {
    ForEach(stickers) { sticker in
        StickerListItemView(sticker: sticker)
    }
    .reorderable()
}
.reorderContainer(for: Sticker.self) { difference in
    difference.apply(to: &stickers)
}
```

## `apply` helper using swift-collections OrderedDictionary

```swift
import OrderedCollections // from https://github.com/apple/swift-collections

extension ReorderDifference where CollectionID == ReorderableSingleCollectionIdentifier {
    func apply(to values: inout [some Identifiable<ItemID>]) {
        var dictionary = OrderedDictionary(uniqueKeys: values.map { $0.id }, values: values)
        let destinationOffset: Int? = switch destination.position {
        case .before(let destination):
            dictionary.keys.firstIndex(of: destination)
        case .end:
            nil
        }
        dictionary.move(keys: sources, to: destinationOffset ?? values.endIndex)
        values = dictionary.values.elements
    }
}
```

## Same reorder code on a LazyVGrid

```swift
LazyVGrid {
    ForEach(stickers) { sticker in
        StickerListItemView(sticker: sticker)
    }
    .reorderable()
}
.reorderContainer(for: Sticker.self) { difference in
    difference.apply(to: &stickers)
}
```

## Swipe actions inside a List

```swift
List {
    ForEach(stickers) { sticker in
        StickerListItemView(sticker: sticker)
            .swipeActions {
                DeleteButton(sticker: sticker)
            }
    }
}
```

## Swipe actions on any view via `swipeActionsContainer()`

```swift
ScrollView {
    LazyVStack {
        ForEach(stickers) { sticker in
            StickerListItemView(sticker: sticker)
                .swipeActions {
                    DeleteButton(sticker: sticker)
                }
        }
    }
}
.swipeActionsContainer()
```

## Item-binding confirmation dialog

```swift
struct StickerCanvasView: View {
    var stickers: [Sticker]
    @State private var stickerToDelete: Sticker?

    var body: some View {
        ZStack {
            ForEach(stickers) { sticker in
                PlacedStickerView(sticker: sticker)
                    .contextMenu {
                        // ...
                    }
            }
        }
        .confirmationDialog(
            "Delete?", item: $stickerToDelete
        ) { sticker in
            DeleteStickerButton(sticker)
        }
    }
}
```

## Same item-binding pattern with `alert`

```swift
struct StickerCanvasView: View {
    var stickers: [Sticker]
    @State private var stickerToDelete: Sticker?

    var body: some View {
        ZStack {
            ForEach(stickers) { sticker in
                PlacedStickerView(sticker: sticker)
                    .contextMenu {
                        // ...
                    }
            }
        }
        .alert(
            "Delete?", item: $stickerToDelete
        ) { sticker in
            DeleteStickerButton(sticker)
        }
    }
}
```

## Custom AsyncImage URLRequest + URLSession cache

```swift
@Observable class StickerStore {
    static let imageSession: URLSession = {
        let config = URLSessionConfiguration.default
        config.urlCache = URLCache(
            memoryCapacity: 64 * 1024 * 1024,
            diskCapacity: 256 * 1024 * 1024)
        return URLSession(configuration: config)
    }()
}

ForEach(pets) { pet in
    AsyncImage(request: URLRequest(
        url: pet.imageURL,
        cachePolicy: .returnCacheDataElseLoad)
    )
}
.asyncImageURLSession(StickerStore.imageSession)
```

## Lazy @State class initialization (the macro upgrade)

```swift
@Observable class StickerStore { }

struct StickerStoreView: View {
    // store is now lazily initialized, only
    // created once for the lifetime of the view
    @State private var store = StickerStore()

    var body: some View {
        // ...
    }
}
```

## Source-breaking case: default value + init assignment (error)

```swift
struct StickerPageView: View {
    @State private var page = StickerPage()
    let title: String

    init(title: String) {
        self.page = StickerPage(title: title) // Variable 'self.title' used before being initialized
        self.title = title
    }

    var body: some View {
        // ...
    }
}
```

## Fix: drop the default value

```swift
struct StickerPageView: View {
    @State private var page: StickerPage // Removed default value to fix error
    let title: String

    init(title: String) {
        self.page = StickerPage(title: title)
        self.title = title
    }

    var body: some View {
        // ...
    }
}
```

## ContentBuilder attribute

```swift
@ContentBuilder
func stickerLibraryView() -> some View {
  // ...
}
```
