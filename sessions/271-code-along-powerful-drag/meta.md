# Session 271 — Code-along: Build powerful drag and drop in SwiftUI

- **URL:** https://developer.apple.com/videos/play/wwdc2026/271/
- **Duration:** 15m
- **Speakers:** Jack (UI Frameworks engineer)
- **Format:** Code-along (downloadable Solitaire sample project)

## Description

SwiftUI's drag and drop, shipped since iOS 16 via `draggable` and `dropDestination`,
gains three new capabilities in the 2027 releases: a reordering API for rearranging
content with drag and drop, the Drag Container APIs for dragging multiple items at once,
and drag/drop configuration APIs that express how data is transferred (move vs. copy).
The whole session builds these interactions into a game of Solitaire.

## Key topics

- `reorderable` + `reorderContainer` modifiers — lift a view, leave a placeholder, drop into a new position; one container can span multiple collections (the seven Solitaire piles) keyed by a `Card.Group` type
- Scoping reordering: multiple `reorderable` views in one container each need a unique `collectionID`; exclude items (face-down cards) by splitting them into a separate `ForEach` without the modifier
- `reorderContainer` closure receives a `difference` (a `ReorderDifference`) you apply to your model
- Drag Container API — `dragContainer(for:)` lifts multiple items per drag; `reorderContainer` implicitly provides one, but you can add your own to customize (e.g. pull the stacked cards above the dragged one)
- Preview formations — `dragPreviewsFormation` (pile / list / stack) for the lifted items; `dropPreviewsFormation` for how they look over a drop destination; set the same value on both for a consistent look
- Drag Configuration API — `dragConfiguration(DragConfiguration(allowMove:))` on the source expresses intent to move (vs. the default copy); the destination has the final say
- `dropConfiguration` closure returns a `DropConfiguration(operation:destination:)` — picks the target collection from `session.location`, restricts to `.move`, and validates against game rules; return `.forbidden` to reject the drop
- `session.reorderDestination(for:in:)` exposes where reordered/inserted items will land inside a custom `dropDestination`

## Availability

- `reorderable`, `reorderContainer`, and the reordering APIs: all Apple platforms that support drag and drop
- `dragContainer`: new on iOS, iPadOS, visionOS 27
- All three formation/container modifiers: macOS 26 and newer
- (Speaker frames everything as "the 2027 releases" — see Open Questions in digest re: 2027 vs. 27 numbering)

## Related sessions to fetch (referenced in this talk)

- [ ] Meet Transferable (WWDC 2022) — how to make content `Transferable`

## Chapter summary (Summary tab)

- **0:00 Introduction** — recap of `draggable` / `dropDestination` / `Transferable` since iOS 16; the three new capabilities (reordering, multi-item drags, drag configuration); Solitaire as the running example.
- **1:42 Reordering** — adopt `reorderable` + `reorderContainer` to rearrange content; enable reordering across all Solitaire piles in one container; exclude face-down cards by splitting the `ForEach`.
- **6:50 Drag multiple items** — use `dragContainer` to lift several items at once based on a selection; customize previews at the source with `dragPreviewsFormation` and at the destination with `dropPreviewsFormation`; pick up and stack multiple cards.
- **9:59 Drag configuration** — express intent for how data transfers between source and destination; `dragConfiguration` for move (vs. copy) on the source, `dropConfiguration` on the destination for the final say; move a card from the deck into a pile without duplication.
- **14:29 Next steps** — recap: make content reorderable, allow multi-item drags, express intent with drag/drop configurations.

## Code

See `code.md` — 8 snippets extracted from the Code tab.
