---
title: "Dive into lazy stacks and scrolling with SwiftUI — Full Digest"
session: WWDC26 · 321
url: https://developer.apple.com/videos/play/wwdc2026/321/
duration: 21m
speakers: Rens Breur
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 321 — Dive into lazy stacks and scrolling with SwiftUI

## TL;DR

A mechanics-first tour of `LazyVStack`/`LazyHStack`. The throughline: a lazy
stack only evaluates the views it can see, so **everything off-screen is an
estimate** — sizes, the content offset, even subview counts. Most of the
"gotchas" in the talk are the same mistake wearing different clothes: feeding
the lazy stack information it can only compute by loading the very views it's
trying to avoid loading. Four headline takeaways:

1. **Sizes and offsets are estimated.** Don't build UI on absolute content
   offset or absolute content size; they drift as the stack learns more.
2. **A view struct ≠ one subview.** A body can resolve to multiple subviews or
   to a *dynamic* number. Dynamic counts in a `ForEach` leaf force the stack to
   keep views (and their state) alive. Filter at the **data** level instead.
3. **Prefetching does work ahead of time.** Set views up in `init`, not
   `onAppear`, so that pre-work isn't thrown away.
4. **Off-screen views get torn down.** View `@State` is not durable across
   scrolling — lift important state into models or bindings.

Best-practice summary from the close: avoid absolute content size/offset; don't
filter data with conditional content in leaf views; set up in `init` not
`onAppear`; don't change subview layout after appearance.

---

## 1. Layout — estimated sizes and a coordinated offset

The setup: a `ScrollView` wrapping a `LazyVStack` of `StepView`s.

```swift
struct ContentView: View {
    var body: some View {
        ScrollView {
            LazyVStack {
                ForEach(steps) { step in
                    StepView(step: step)
                }
            }
        }
    }
}
```

Unlike a `VStack`, a `LazyVStack` doesn't evaluate or render views outside the
visible rect. It lays out top-to-bottom and stops once the visible rect is
filled; scrolling adds and removes views to keep that rect filled.

The cost is correctness:

- **Off-screen heights are estimated** from the average size of already-placed
  views times the estimated remaining count. The stack is unaware of changes in
  off-screen views.
- **Ideal width = the first subview's width.** The stack can't find the max
  width across all views because it hasn't loaded them. (Origami's first view is
  flexible, so the stack fills the screen width.) The same rule gives a
  `LazyHStack` its height in a vertical scroll — the **first** subview's height.
- **Total height drifts.** Scroll to the bottom and if the last views are
  shorter, the stack corrects its original estimate.
- **The space above the visible rect is estimated too**, so the **content
  offset is an estimate**. After an iPhone orientation change, `StepView`s are
  shorter in landscape; the stack keeps the topmost visible view anchored, and
  only when you scroll back to the top does it reconcile the estimated space
  above — updating the `ScrollView`'s content offset by the same amount so the
  top reads zero.

The lazy stack and the embedding scroll view **coordinate position and offset**
so that when estimates update, the visible subviews don't visibly jump.

### Composing stacks

A `LazyHStack` can nest inside a `LazyVStack` (the Origami "Showcase" of user
photos), which is also a performance win since not everyone scrolls the inner
view.

```swift
struct Showcase: View {
    var body: some View {
        ScrollView(.horizontal) {
            LazyHStack {
                ForEach(photos) { photo in
                    PhotoView(photo: photo)
                }
            }
        }
    }
}
```

Because the `LazyHStack`'s height comes from its first subview, variable-length
caption labels would get clipped — the stack can't know the tallest one ahead of
time. **Fix the heights** (e.g. a line limit plus reserved space).

Switching the showcase to a vertical `Section` lets you pin its header:

```swift
LazyVStack(pinnedViews: [.sectionHeaders]) {
    ForEach(steps) { step in StepView(step: step) }
    Showcase()   // contains a Section { ... } header: { ... }
}
```

### Two scroll-effect / offset pitfalls

**`.scrollTransition` must not push views into the visible rect.** The stack
decides visibility from a view's *original* frame. A rotation + grow transform
moves a view into view that the stack thinks is off-screen, so it drops it (the
"pink swan disappears too soon"). A shrink-only effect stays inside the frame
and works:

```swift
// Works — never grows past the original frame
.scrollTransition { effect, phase in
    effect.scaleEffect(1 - abs(phase.value) * 0.1)
}
```

**Don't gate UI on absolute content offset.** Toggling a "scroll to showcase"
button off `onScrollGeometryChange` content offset is fragile because that
offset is estimated. Use relative subview visibility instead:

```swift
.onScrollTargetVisibilityChange(idType: Step.ID.self, threshold: 0.8) { visibleIDs in
    isScrollToShowcaseVisible = shouldShowScrollButton(visibleIDs: visibleIDs)
}
```

## 2. Subview loading — one struct isn't always one subview

The 1-to-1 mental model (`ForEach` → one `StepView` → one subview) is only the
simple case. Two ways it breaks:

**Multiple subviews.** If `StepView`'s body has two views at the top level with
no wrapping layout, the `LazyVStack` loads each separately:

```swift
struct StepView: View {
    let step: Step
    var body: some View {
        StepDiagram(/* ... */)
        StepInstructions(/* ... */)
    }
}
```

**Dynamic subview count — watch out.** A conditional makes each `StepView`
resolve to one *or zero* subviews:

```swift
struct StepView: View {
    let step: Step
    @Environment(\.detailLevel) var detailLevel
    var body: some View {
        if step.isVisible(in: detailLevel) {
            VStack { /* ... */ }
        }
    }
}
```

The stack addresses subviews **by index**, so a varying count forces it to keep
earlier `StepView`s alive in case `detailLevel` changes the indices. Worse, an
unrelated env value (`writingStyle`) used in the body now triggers body
evaluations and held-onto state for off-screen views.

The fix is to make the count knowable without constructing views — **filter at
the data level** with a SwiftData predicate:

```swift
init(detailLevel: DetailLevel) {
    _steps = Query(filter: #Predicate<Step> { step in
        step.detailLevel >= detailLevel
    })
}
```

Unwrapping an optional in a body (`if let token`) is the same dynamic-count
trap; push that decision higher up (a `NetworkClient` model + a
`ContentUnavailableView` when unauthenticated) rather than into the leaf.

Because lazy stacks hold only a small window of data, they skip a full diff and
do a **minimal change check on visible views** — another reason keeping the
subview structure stable matters.

## 3. Prefetching — partial work, ahead of the deadline

While scrolling, a `ScrollView` must hit a per-frame deadline. Placing a new
view (body eval + layout + render) can be expensive enough to blow that
deadline, dropping a frame — a visible hitch. **Prefetching** lets the lazy
stack do part of that work *before* a view is visible, spread across multiple
frames, so by the time the view appears most of the work is done. Nested
`LazyHStack` setup can be broken up this way too.

Consequences for ordering:

- Body is generally called first; `onAppear` only later, when placed. If the
  scroll direction reverses, **body may run during prefetch and `onAppear` never
  fires.**
- `onAppear` is still right for some things — **infinite scroll** fetches the
  next page from a trailing `ProgressView`'s `onAppear`.
- But **don't set up a whole view in `onAppear`.** If size/contents change after
  placement, prefetched work is wasted and re-done, and the stack may load more
  views than needed. **Set up in `init`** so the view is reasonable before it
  appears.

```swift
struct StepView: View {
    @State var viewModel: StepViewModel
    init(id: Step.ID) {
        _viewModel = State(initialValue: StepViewModel(id: id))
    }
    var body: some View { /* ... */ }
}
```

Same idea for async loads: a `.task` fires on appear, but a cache-backed loader
that **starts fetching in its initializer** gets a prefetch head start:

```swift
init(step: Step) {
    self.step = step
    _diagramLoader = State(initialValue: DiagramLoader(id: step.id))
}
```

### Off-screen teardown — state isn't durable

Scrolled-off views aren't removed immediately (kept for a number of updates in
case you scroll back), but eventually they're deleted **along with their
`@State`**. So `@State var isHighlighted` is lost on scroll-off. Move durable
state up:

```swift
struct ContentView: View {
    @State var highlighted: Set<Step.ID> = []
}
struct StepView: View {
    @Binding var highlighted: Set<Step.ID>
}
```

## 4. Programmatic scrolling

A `ScrollPosition` binding scrolls to a target by id, **even off-screen** — the
stack estimates the target's position and, in an animated scroll, updates that
estimate every frame.

```swift
struct ContentView: View {
    @State var scrollPosition = ScrollPosition()
    var body: some View {
        ScrollView { /* ... */ }
            .scrollPosition($scrollPosition)
    }
    func scrollToShowcase() {
        withAnimation { scrollPosition.scrollTo(id: "showcase-header") }
    }
}
```

The same pitfalls resurface, now as smoothness problems:

- **Dynamic subview counts** hurt. Scrolling to an id is most performant when
  each `ForEach` element resolves to exactly one subview, so the stack can find
  the id by **querying the `ForEach` without constructing views**. Counting to a
  near-the-end target is also faster when the count is cheap — again, **filter on
  the data level**.
- **Layout that changes after appearance** hurts. The `onGeometryChange` →
  state → second-layout-pass pattern means the stack measures one height, then
  the view grows after appearing and pushes content down. If you can't express
  it with SwiftUI's layout primitives, reach for a **custom `Layout`**
  (`StepLayout`) — single pass, no state round-trip.

## Bonus — what's new around lazy stacks (2027 releases)

Briefly mentioned, not the focus: `reorderable` (drag-to-reorder views) and
swipe actions on views **outside** `List` — both work with lazy stacks. See the
drag-and-drop code-along.
