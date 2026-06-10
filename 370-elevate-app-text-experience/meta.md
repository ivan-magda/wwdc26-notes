# Session 370 — Elevate your app's text experience with TextKit

- **URL:** https://developer.apple.com/videos/play/wwdc2026/370/
- **Duration:** 24m
- **Speakers:** Tarun Uday (engineer, TextKit team)

## Description

TextKit is Apple's next-generation text engine, powering text layout and rendering
across SwiftUI, UIKit, and AppKit. This session tackles the long-standing tension
between convenience (framework text views like `UITextView`, `NSTextView`,
`TextEditor`) and control (building custom text views directly on TextKit). New 2027
APIs — the rendering-surface protocols plus public delegate conformance on the
framework text views — let you subclass `UITextView`/`NSTextView` and hook into the
viewport layout process to get the best of both worlds.

## Key topics

- TextKit's four-layer architecture: text storage, layout, viewport, view
- `NSTextContentStorage` → `NSTextParagraph` elements; `NSTextLayoutManager` →
  immutable `NSTextLayoutFragment`s; `NSTextViewportLayoutController` coordinates
  viewport rendering
- New `NSTextViewportRenderingSurface` protocol — a common abstraction for any
  view/layer (UIView, NSView, CALayer) that draws layout fragments
- New `NSTextViewportRenderingSurfaceKey` protocol — uniquely identifies surfaces
  across viewport layout cycles; `NSTextLayoutFragment` can serve as a key for caching
- `UITextView` / `NSTextView` now publicly conform to
  `NSTextViewportLayoutControllerDelegate` — subclass and override `willLayout`,
  `configureRenderingSurface`, `didLayout`
- Bringing framework text views into SwiftUI via `UIViewRepresentable` /
  `NSViewRepresentable`
- Example 1: code editor with line numbers (enumerate paragraphs, capture fragment
  bounds, draw numbers in a container view)
- Example 2: collapsible recipe sections via `NSTextContentStorageDelegate`'s
  `textContentManager(_:shouldEnumerate:)` to skip layout for collapsed paragraphs
- Text attachments (`NSTextAttachment` + `NSTextAttachmentViewProvider`) and the new
  `register(_:forTextAttachmentViewProviderType:)` reuse policies
  (`onEditingInlineParagraphs`, `onScrollingOutOfViewport`)

## Related sessions to fetch

- [ ] Meet TextKit 2 (WWDC21)
- [ ] What's new in TextKit and text views (WWDC22)

## Chapter summary (Summary tab)

- **0:00 Introduction** — TextKit and the tension between using framework text views
  versus building custom ones. TextKit is Apple's text engine powering text controls
  in SwiftUI, UIKit, and AppKit.
- **3:09 TextKit architecture** — the four-layer architecture: text storage, layout,
  viewport, and view. `NSTextContentStorage` breaks an attributed string into
  `NSTextParagraph` elements; `NSTextLayoutManager` produces immutable
  `NSTextLayoutFragment`s; `NSTextViewportLayoutController` coordinates with the text
  view to render only the paragraphs visible in the viewport.
- **9:17 What's new in TextKit** — the new `NSTextViewportRenderingSurface` protocol
  (a common abstraction for views/layers that draw layout fragments) and
  `NSTextViewportRenderingSurfaceKey` (uniquely identifies surfaces across viewport
  layout cycles). New delegate methods assign and query rendering surfaces during the
  viewport layout process.
- **11:27 Extending framework text views** — `UITextView`/`NSTextView` now publicly
  conform to `NSTextViewportLayoutControllerDelegate`, so you can subclass and override
  `willLayout`, `configureRenderingSurface`, and `didLayout`. A SwiftUI
  ViewRepresentable brings these text views into a SwiftUI app.
- **12:58 Example: Code editor with line numbers** — subclass `UITextView`, override
  the viewport controller delegate methods, compute the starting line number with
  `enumerateTextElements`, capture each fragment's bounds in
  `configureRenderingSurface`, and pass results to a container view that draws line
  numbers.
- **17:52 Example: Collapsible recipe sections** — conform to
  `NSTextContentStorageDelegate`; use `textContentManager(_:shouldEnumerate:)` to skip
  layout for collapsed paragraphs, track collapsed offsets in state, and toggle them on
  tap — collapsing each multi-paragraph recipe to its heading.
- **19:56 Text attachments and view provider reuse** — attachments use the same TextKit
  architecture; `NSTextAttachmentViewProvider` supplies the view. New: register a reuse
  policy via `register(_:forTextAttachmentViewProviderType:)`; `onEditingInlineParagraphs`
  preserves view providers across edits and `onScrollingOutOfViewport` caches surfaces
  that scroll off screen.
- **23:00 Next steps** — kickstart with `UITextView`/`NSTextView`/`TextEditor`, extend
  them via the viewport controller delegate hooks, or use TextKit directly for fully
  custom rendering. Download the sample app.

## Code

See `code.md` — 11 snippets from the Code tab.
