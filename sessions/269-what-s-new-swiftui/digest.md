---
title: "What's new in SwiftUI — Full Digest"
session: WWDC26 · 269
url: https://developer.apple.com/videos/play/wwdc2026/269/
duration: 28m
speakers: Steven, Julia
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 269 — What's new in SwiftUI

## TL;DR

The 2027-releases SwiftUI roundup, demoed end-to-end with a "Stickers" document app
(photos + draggable pet stickers). Five headline themes:

1. **Refreshed Liquid Glass for free** — apps adopt the updated look on 2027 OS releases without changing a line of code: new tint slider, `interactive` custom elements on macOS, inactive-window dimming via `appearsActive`.
2. **Toolbars built for resizable apps** — iPhone apps become resizable on iOS 27, so toolbars need to degrade gracefully. New `visibilityPriority`, `ToolbarOverflowMenu`, `topBarPinnedTrailing`, `toolbarMinimizeBehavior`, and the `prominent` tab role give you control over what survives a squeeze.
3. **A real Document API** — `DocumentCreationSource` custom launch flows, `@Observable` document classes, and the `WritableDocument` / `ReadableDocument` protocol pair (with `DocumentWriter` / `DocumentReader` friends) for `nonisolated async`, incremental, progress-reporting disk I/O and easy multi-format export.
4. **Container-agnostic interactions** — `reorderable()` / `reorderContainer(for:)` works on List, `LazyVGrid`, and watchOS for the first time; swipe actions escape `List` via `swipeActionsContainer()`; `confirmationDialog` and `alert` gain the item-binding pattern sheets already had.
5. **Quiet performance wins** — `AsyncImage` HTTP caching by default, `@State` becomes a macro so stored classes initialize lazily (back-ported to iOS 17 / macOS 14), and `ContentBuilder` collapses the type-checker's combinatorial blowup. Plus new Xcode 27 agent skills for adopting all of it.

Throughout, "2027 releases" is Apple's phrase for iOS/iPadOS/macOS/watchOS 27 (see Open Questions).

---

## 1. Refreshed look and feel

Build and run on the 2027 releases and Liquid Glass takes on its updated appearance
automatically — "without having to change a single line of code." It responds to the
new Liquid Glass tint slider on its own.

Fine-tuning hooks:

- **Interactive custom elements on macOS** — mark custom Liquid Glass elements as `interactive` (as on iOS) so they respond fluidly to clicks; optimized for the mouse pointer.
- **Inactive-window appearance** — iPad apps (like Mac) dim icons and text when the window is inactive, reinforcing which window is active. Tap to switch between apps and the inactive one dims.
- **`appearsActive` environment value** — fine-tune custom views (a sidebar account button here) to follow the active/inactive state:

```swift
struct SidebarFooterView: View {
    @Environment(\.appearsActive) private var appearsActive

    var body: some View {
        MyAccountView()
            .opacity(appearsActive ? 1 : 0.5)
    }
}
```

- **Minimal menu-bar icons** — iPad and Mac menu bars now show a minimal set of icons by default, reserving them for key actions. Force one to show with `labelStyle(.titleAndIcon)`:

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

### Resizability

- On **iOS 27, iPhone apps become resizable too** (Mac/iPad already were).
- **Xcode 27 Live Previews gain resize handles** so you can interactively test resize behavior — useful for iPhone Mirroring and running iPhone apps on iPad.
- SwiftUI apps get most of this automatically. **Mixed UIKit + SwiftUI apps** have more to consider: screen geometry, **using size classes instead of idiom** for sizing, and interface-orientation changes. Deep-dive: **"Modernize your UIKit app."**

## 2. Toolbars and tabs for limited space

The `prominent` tab role makes a tab stand out — here the shopping-cart tab is pushed
to the bottom trailing edge, distinct from content tabs:

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

When a window shrinks (especially on iPhone), the system hides toolbar items that
don't fit into an overflow menu. The new toolbar APIs let you control *which* items
survive:

- **`visibilityPriority(.high)`** on a `ToolbarItemGroup` keeps important items (Undo/Redo) visible.
- **`ToolbarOverflowMenu`** always parks less-used actions (swap photo, export, clear) in the overflow menu.
- **`topBarPinnedTrailing`** placement pins an item (Share) so it's never hidden.

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

- **`toolbarMinimizeBehavior(.onScrollDown, for: .navigationBar)`** moves the navigation bar out of the way while scrolling, reclaiming space.

```swift
ScrollView {
    StickerListView()
}
.toolbarMinimizeBehavior(.onScrollDown, for: .navigationBar)
```

## 3. The new Document API

SwiftUI has long had `FileDocument` and `ReferenceFileDocument`. The 2027 releases
expand on that foundation. Document-based apps (Pixelmator Pro, Pages, Xcode) get a lot
out of the box: ⌘N / ⌘O shortcuts, the edited indicator, smart autosaving. The session
covers three improvements: the **document creation context**, **disk read/write
performance**, and **direct document URL access**.

### Custom new-document flows with `DocumentCreationSource`

Declare named creation sources and add a `NewDocumentButton` per source to the launch
scene. The chosen source flows into the creation closure via the `context` parameter —
here, choosing "photo" opens the document with the photo picker already presented.

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

extension DocumentCreationSource {
    static let blank = Self(id: "blank")
    static let photo = Self(id: "photo")
}
```

A `DocumentGroup` can coexist with a `WindowGroup` in the same app's body.

### `@Observable` documents → free incremental view updates

Declare a `DocumentGroup` as the first scene and back it with an `@Observable` document
class. Observation alone is a win: views update only when a property they depend on
changes.

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

### Writing: `WritableDocument` + `DocumentWriter`

`WritableDocument` has **three requirements**: a list of writable formats, a `snapshot`
method that returns the current content, and a `writer`. The snapshot is a plain value
type capturing the document at a point in time:

```swift
struct PageSnapshot {
    var background: Image
    var metadata: StickerPlacements
    var stickers: [Image]
}
```

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

The `DocumentWriter` is where the performance story lives. Its one requirement, `write`,
is **`nonisolated` and `async`** so expensive disk work runs in the background. It
receives the **previous snapshot**, so you can diff and write only what changed, and a
**`Subprogress`** (Foundation) to report progress:

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

### Reading: `ReadableDocument` + `DocumentReader`

`ReadableDocument` is the twin of `WritableDocument`. Each requires a content-type list;
`WritableDocument` provides a snapshot while `ReadableDocument` knows how to apply one.
Their "friend" protocols mirror too: `DocumentWriter` ↔ `DocumentReader`, the latter
doing the disk heavy-lifting on read.

```swift
extension StickerDocument: ReadableDocument {
}
```

### Multi-format export

Add another content type and branch in `write`. Here PNG export flattens stickers +
background with Core Graphics — "I could write the document in any format, using any
framework, just by adding another content type."

```swift
@Observable
final class StickerDocument: WritableDocument {
    static let writableContentTypes: [UTType] = [.stickerDocument, .png]
}
```

```swift
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
```

## 4. Presentation and interaction

### Reorderable containers — and they're container-agnostic

Add `reorderable()` to the `ForEach` and `reorderContainer(for:)` to the container.
SwiftUI handles the drag interaction and animation; your closure commits the new order.

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

The `apply` helper uses the open-source **swift-collections** `OrderedDictionary` to
commit ordering changes:

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

The headline: **the same reorder code works on any container.** Swap the `List` for a
`LazyVGrid` and the reordering code stays identical — and it brings reordering to
**watchOS for the first time**.

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

Deep-dive code-along: **"Build powerful drag and drop in SwiftUI."**

### Swipe actions on any view

`swipeActions` used to be List-only. Now you can move a `ForEach` into a `LazyVStack`
inside a `ScrollView` and coordinate swipes with `swipeActionsContainer()`:

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

### Item-binding confirmation dialogs and alerts

`confirmationDialog` (and `alert`) now support the **item-binding pattern** sheets
already use: set an optional `@State` item and the dialog presents with that value.

```swift
struct StickerCanvasView: View {
    var stickers: [Sticker]
    @State private var stickerToDelete: Sticker?

    var body: some View {
        ZStack {
            ForEach(stickers) { sticker in
                PlacedStickerView(sticker: sticker)
                    .contextMenu { /* ... */ }
            }
        }
        .confirmationDialog("Delete?", item: $stickerToDelete) { sticker in
            DeleteStickerButton(sticker)
        }
    }
}
```

## 5. Data flow and performance

### AsyncImage HTTP caching

Until now `AsyncImage` didn't keep images in memory — scroll back up and they reloaded.
On the 2027 releases it supports **standard HTTP caching by default**, respecting the
server's cache headers, with no code changes, for every app. Apps built with Xcode 27
can customize downloads: pass your own `URLRequest` (e.g. a cache policy) and supply a
longer-lived `URLSession`/`URLCache` via `asyncImageURLSession(_:)`.

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

### `@State` is now a macro → lazy class init

Previously, a class assigned to a `@State` property was constructed on **every** view
init, then immediately discarded because the stored instance was already stable —
wasted allocations. On the 2027 releases, classes initialized and stored via `@State`
are **lazy**: created only once, for the view's lifetime. This is thanks to converting
`@State` from a `DynamicProperty` **to a macro**, and the behavior is **back-ported to
iOS 17 / macOS 14 and aligned releases** (where `@Observable` first shipped).

```swift
@Observable class StickerStore { }

struct StickerStoreView: View {
    // store is now lazily initialized, only
    // created once for the lifetime of the view
    @State private var store = StickerStore()

    var body: some View { /* ... */ }
}
```

**Source-breaking caveat:** if you give a `@State` property a default value *and* assign
it again in `init`, Xcode flags "used before being initialized." Fix by dropping the
default value:

```swift
struct StickerPageView: View {
    @State private var page: StickerPage // Removed default value to fix error
    let title: String

    init(title: String) {
        self.page = StickerPage(title: title)
        self.title = title
    }
    var body: some View { /* ... */ }
}
```

### ContentBuilder — killing "unable to type-check in reasonable time"

The classic deeply-nested-view compiler error comes from result-builder overloads.
`Section` can build a `View` *or* `TableRowContent`; nested `Group` and `ForEach` each
add more options, so the compiler explores a combinatorial decision tree even though
only one path (everything builds views) is valid. On the 2027 releases the most common
builders **share a single initializer** because they're unified under one builder,
**`ContentBuilder`** — leaving one straightforward path.

```swift
@ContentBuilder
func stickerLibraryView() -> some View {
  // ...
}
```

`ContentBuilder` is an evolution of `ViewBuilder`, works with **any minimum deployment
target**, and improves type-checking when building with Xcode 27 — whether you target
2027 releases or earlier. It's framed as a step toward unified builders across all of
SwiftUI.

### Xcode 27 agent skills

Two new skills ship with Xcode 27's Coding Assistant: the **SwiftUI Specialist Skill**
(best practices) and the **What's New In SwiftUI Skill** (adopting 2027-release APIs).
Export them for other tools via `xcrun agent skills export`, which writes importable
Markdown files.
