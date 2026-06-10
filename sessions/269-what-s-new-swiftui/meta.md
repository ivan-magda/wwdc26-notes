# Session 269 — What's new in SwiftUI

- **URL:** https://developer.apple.com/videos/play/wwdc2026/269/
- **Duration:** 28m
- **Speakers:** Steven (UI Frameworks), Julia (UI Frameworks)

## Description

The annual SwiftUI roundup for the 2027 releases. Covers the refreshed Liquid Glass
look and feel (gained with no code changes), toolbar customization for resizable apps,
an expanded document-based app API (`DocumentCreationSource`, `WritableDocument` /
`ReadableDocument`, `DocumentWriter` / `DocumentReader`), presentation and interaction
improvements (reorderable containers, swipe actions on any view, item-binding
confirmation dialogs), and data-flow / performance enhancements (`AsyncImage` HTTP
caching, `@State` as a macro with lazy class init, `ContentBuilder` for faster type
checking). Demoed with a "Stickers" document app.

## Key topics

- **Refreshed Liquid Glass** on 2027 releases — automatic, no code changes; new tint slider; `interactive` custom elements on macOS; inactive-window dimming on iPad/Mac via `appearsActive`.
- **Resizability** — iPhone apps become resizable on iOS 27; Xcode 27 Live Preview resize handles; use size classes (not idiom) for mixed UIKit/SwiftUI apps.
- **Toolbar APIs** — `prominent` tab role; `visibilityPriority`; `ToolbarOverflowMenu`; `topBarPinnedTrailing` placement; `toolbarMinimizeBehavior(.onScrollDown, for: .navigationBar)`.
- **Document API** — `DocumentCreationSource` + `NewDocumentButton` in `DocumentGroupLaunchScene`; `@Observable` document classes; `WritableDocument` / `ReadableDocument` protocols with `DocumentWriter` / `DocumentReader` friends; `nonisolated async` writes, incremental writing via previous-snapshot diff, `Subprogress` reporting; multi-format export (e.g. PNG via Core Graphics).
- **Presentation & interaction** — `reorderable()` + `reorderContainer(for:)` working on List, `LazyVGrid`, and watchOS for the first time; `swipeActions` on any view + `swipeActionsContainer()`; `confirmationDialog`/`alert` item-binding pattern.
- **Data flow & performance** — `AsyncImage` standard HTTP caching by default + `AsyncImage(request:)` / `asyncImageURLSession(_:)`; `@State` converted to a macro → lazy class init (back-ported to iOS 17 / macOS 14); `ContentBuilder` unifies builders for faster type checking.
- **Agent skills** — SwiftUI Specialist Skill + What's New In SwiftUI Skill in Xcode 27 Coding Assistant; exportable via `xcrun agent skills export`.

## Related sessions to fetch (referenced in this talk)

- [ ] Modernize your UIKit app
- [ ] Build powerful drag and drop in SwiftUI (code-along)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Steven & Julia; overview of refreshed Liquid Glass, new document APIs, presentation/interaction, and data-flow/performance. Stickers demo app introduced.
- **2:12 Refreshed look and feel** — automatic Liquid Glass on 2027 OS releases; interactive elements; inactive-window appearance on iPadOS; toolbar customization (overflow menus, pinned placements); minimize-on-scroll; building resizable apps with size classes.
- **8:06 Document-based apps** — expanded document APIs; `DocumentCreationSource` for custom new-document flows; read/write performance; first-class direct document URL access via `FileDocument` / `ReferenceFileDocument`.
- **15:18 Presentation and interaction** — reorderable container APIs (List, `LazyVGrid`, watchOS first time); swipe actions on arbitrary views; item-binding confirmation dialogs.
- **19:58 Data flow and performance** — `AsyncImage` HTTP caching by default + custom `URLRequest`/`URLSession` APIs; `@State` becomes a macro → lazy class init, back-ported to iOS 17 / macOS 14.
- **27:25 Next steps** — build in Xcode 27 to see updated Liquid Glass; adopt new Document APIs; explore the SwiftUI agent skills.

## Code

See `code.md` — 33 snippets extracted from the Code tab.
