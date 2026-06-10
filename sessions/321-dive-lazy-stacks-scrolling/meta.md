# Session 321 — Dive into lazy stacks and scrolling with SwiftUI

- **URL:** https://developer.apple.com/videos/play/wwdc2026/321/
- **Duration:** 21m
- **Speakers:** Rens Breur (UI Frameworks Engineer)

## Description

A refresher and deep dive into how `LazyVStack` and `LazyHStack` actually work
inside a `ScrollView`. Rens Breur walks through layout (estimated sizes and
content offsets), how view structs resolve into the subviews a lazy stack
sees, the prefetching mechanism that keeps scrolling hitch-free, and
programmatic scrolling — pausing on each topic to call out the patterns that
quietly break performance and correctness. Built around an Origami instructions
app.

## Key topics

- Lazy stack layout: only visible views are evaluated/rendered; off-screen sizes and the content offset are **estimated** and can shift mid-scroll
- Ideal width of a `LazyVStack` (and height of a `LazyHStack` in a vertical scroll) = the size of its **first** subview; fix sizes to avoid clipping
- Coordination between the lazy stack and the embedding `ScrollView` so the visible region stays put when estimates update (e.g. after orientation change)
- Composing stacks: nesting a `LazyHStack` in a `LazyVStack`; pinning section headers with the `pinnedViews` parameter
- `.scrollTransition` gotcha: transforms must not push off-screen views into the visible rect (the lazy stack thinks they're gone)
- Prefer relative subview visibility (`.onScrollTargetVisibilityChange`) over absolute `.onScrollGeometryChange` content offset
- Subview resolution: one view struct can resolve to **multiple** or a **dynamic** number of subviews; dynamic counts in a `ForEach` leaf keep views alive longer (index addressing)
- Filter at the **data** level (SwiftData `#Predicate` on `@Query`), not with conditionals in leaf view bodies
- Prefetching: lazy stacks do partial render work across frames before a view appears; set views up in `init`, not `onAppear`
- View state is discarded once scrolled off-screen — move durable state into model objects or bindings
- Programmatic scrolling via `ScrollPosition` works for off-screen targets; dynamic subview counts and post-appearance layout changes (`onGeometryChange`) hurt smoothness — reach for a custom `Layout`

## Related sessions to fetch (referenced in this talk)

- [ ] Code-along: Build powerful drag and drop in SwiftUI
- [ ] Stacks, Grids, and Outlines in SwiftUI
- [ ] Compose custom layouts with SwiftUI

## Chapter summary (Summary tab)

- **0:00 Introduction** — Rens Breur introduces lazy stacks, an essential SwiftUI component for long and custom scrolling content.
- **1:24 Layout** — how `LazyVStack`/`LazyHStack` lay out subviews: only visible views are added, full size is estimated; how estimations change and coordinate the content offset with the embedding `ScrollView`; lazy stacks can be composed for more complex layouts.
- **9:13 Subview loading** — how view structs resolve into individual subviews; the 1-to-1 mapping isn't always what happens. A body can resolve to multiple or a dynamic number of subviews, with consequences for what the lazy stack keeps alive.
- **13:15 Prefetching** — lazy stacks prefetch subviews before they scroll on screen, doing partial render work to avoid hitches; don't delay setup to `onAppear`. Subviews are kept a little longer after scrolling off but removed eventually; move durable state into models/bindings.
- **17:40 Programmatic scrolling** — a `ScrollPosition` binding scrolls to a target even off-screen (the lazy stack estimates its position). Same pitfalls: dynamic subview counts and `onAppear`/`onGeometryChange`-driven layout passes hurt smoothness. Sometimes a custom `Layout` is the better solution.
- **19:55 Next steps** — avoid absolute content size/offset; don't filter data with conditional content in leaf views; set views up in `init` not `onAppear`; keep important state outside view structs that may scroll off-screen.

## Code

See `code.md` — snippets extracted from the Code tab.
