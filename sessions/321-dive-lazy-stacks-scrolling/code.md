# Code samples — Session 321

Extracted from the Code tab. Duplicates collapsed; timestamps map to the
transcript. Many snippets use `/* ... */` placeholders exactly as Apple showed
them — the focus is the one part being discussed.

## 1:37 — The basic Origami setup (ScrollView + LazyVStack + ForEach)

```swift
// Origami app

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

struct StepView: View { /* ... */ }
```

## 5:20 — Nesting a horizontal showcase (LazyHStack inside LazyVStack)

```swift
// Horizontally scrolling showcase

struct ContentView: View {
    var body: some View {
        ScrollView {
            LazyVStack {
                ForEach(steps) { step in
                    StepView(step: step)
                }
                Showcase()
            }
        }
    }
}

struct StepView: View { /* ... */ }

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

## 6:30 — Pinning a section header with `pinnedViews`

```swift
// Showcase section

struct ContentView: View {
    var body: some View {
        ScrollView {
            LazyVStack(pinnedViews: [.sectionHeaders]) {
                ForEach(steps) { step in
                    StepView(step: step)
                }
                Showcase()
            }
        }
    }
}

struct StepView: View { /* ... */ }

struct Showcase: View {
    var body: some View {
        Section {
            ForEach(photos) { photo in
                PhotoView(photo: photo)
            }
        } header: { /* ... */ }
    }
}
```

## 7:04 — Scroll transition pitfall (transform pushes views out of frame)

```swift
// Scroll effect — BROKEN: rotation/scale pushes views out of their original rect,
// so the lazy stack thinks they're off-screen and drops them too early.

struct ContentView: View { /* ... */ }

struct StepView: View { /* ... */ }

struct Showcase: View {
    var body: some View {
        Section {
            ForEach(photos) { photo in
                PhotoView(photo: photo)
                    .scrollTransition { effect, phase in
                        effect
                            .rotationEffect(.degrees(phase.value * 20))
                            .scaleEffect(1 + phase.value * 0.2)
                    }
            }
        } header: { /* ... */ }
    }
}
```

## 7:45 — Scroll transition that stays within the frame (works)

```swift
// Scroll effect — OK: scale only shrinks, never pushing views into the visible rect.

struct ContentView: View { /* ... */ }

struct StepView: View { /* ... */ }

struct Showcase: View {
    var body: some View {
        Section {
            ForEach(photos) { photo in
                PhotoView(photo: photo)
                    .scrollTransition { effect, phase in
                        effect
                            .scaleEffect(1 - abs(phase.value) * 0.1)
                    }
            }
        } header: { /* ... */ }
    }
}
```

## 8:21 — Absolute content offset (fragile because the offset is estimated)

```swift
// Absolute offset — fragile: the lazy stack's content offset is estimated,
// so the exact threshold where the button toggles can drift.

struct ContentView: View {
    @State var isScrollToShowcaseVisible = false

    var body: some View {
        ScrollView { /* ... */ }
            .overlay(alignment: .bottom) { /* ... */ }
            .onScrollGeometryChange(for: Bool.self) { geo in
                geo.contentOffset.y <= 100
            } action: { _, newValue in
                self.isScrollToShowcaseVisible = newValue
            }
    }
}
```

## 8:53 — Relative visibility instead (`onScrollTargetVisibilityChange`)

```swift
// Drive UI off which subviews are actually visible, not an absolute offset.

struct ContentView: View {
    @State var isScrollToShowcaseVisible = false

    var body: some View {
        ScrollView { /* ... */ }
            .overlay(alignment: .bottom) { /* ... */ }
            .onScrollTargetVisibilityChange(
                idType: Step.ID.self,
                threshold: 0.8
            ) { visibleIDs in
                isScrollToShowcaseVisible = shouldShowScrollButton(visibleIDs: visibleIDs)
            }
    }
}
```

## 10:09 — One view struct resolving to multiple subviews

```swift
// Multiple subviews — StepDiagram and StepInstructions sit at the top level
// of the body (no wrapping VStack), so the LazyVStack loads each separately.

struct ContentView: View { /* ... */ }

struct StepView: View {
    let step: Step

    var body: some View {
        StepDiagram(/* ... */)
        StepInstructions(/* ... */)
    }
}
```

## 10:52 — Dynamic number of subviews (anti-pattern in a ForEach leaf)

```swift
// Dynamic number of views — each StepView resolves to one OR zero subviews
// depending on detailLevel. The LazyVStack addresses subviews by index, so it
// has to keep earlier StepViews alive in case the count changes.

struct ContentView: View { /* ... */ }

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

## 11:55 — Why dynamic counts cost more (unrelated env value triggers updates)

```swift
// A change in writingStyle now re-evaluates bodies even for off-screen views,
// and the lazy stack won't release their state.

struct ContentView: View { /* ... */ }

struct StepView: View {
    let step: Step

    @Environment(\.detailLevel) var detailLevel
    @Environment(\.writingStyle) var writingStyle

    var body: some View {
        if step.isVisible(in: detailLevel) { /* ... */ }
    }
}
```

## 12:18 — Filter at the data level with a SwiftData `#Predicate`

```swift
// Filter at the data level — the LazyVStack knows the subview count up front
// and never has to construct views just to compute indices.

struct ContentView: View {
    @Query var steps: [Step]

    init(detailLevel: DetailLevel) {
        _steps = Query(filter: #Predicate<Step> { step in
            step.detailLevel >= detailLevel
        })
    }

    var body: some View { /* ... */ }
}

struct StepView: View { /* ... */ }
```

## 12:39 — Optional unwrapping has the same dynamic-count effect

```swift
// Optional unwrapping in a body is also a dynamic (0 or 1) subview count.

struct ContentView: View { /* ... */ }

struct StepView: View {
    let step: Step

    @Environment(\.apiToken) var token

    var body: some View {
        if let token { /* ... */ }
    }
}
```

## 12:49 — Push the auth decision higher up instead

```swift
// Let a model object hold the token and gate the whole lazy stack higher up
// (e.g. show a ContentUnavailableView when not authenticated).

struct ContentView: View { /* ... */ }

struct StepView: View {
    let step: Step

    @Environment(NetworkClient.self) var networkClient

    var body: some View { /* ... */ }
}
```

## 15:31 — Infinite scrolling: a legitimate use of `onAppear`

```swift
// Loading more content — onAppear on a trailing ProgressView fetches the next page.

struct Showcase: View {
    @State var pager = ShowcasePager()

    var body: some View {
        ForEach(pager.pages) { page in
            PageView(page: page)
        }
        if !pager.atEnd {
            ProgressView()
                .progressViewStyle(.circular)
                .onAppear {
                    pager.fetchPage()
                }
        }
    }
}
```

## 15:52 — Don't set up the whole view in `onAppear` (throws away prefetch)

```swift
// onAppear — BAD: setup happens after placement, so prefetched work is wasted
// and the view's size/contents change once it appears.

struct StepView: View {
    let id: Step.ID
    @State var viewModel = StepViewModel()

    var body: some View {
        VStack {
            if let content = viewModel.content { /* ... */ }
        }
        .onAppear {
            viewModel.configure(with: id)
        }
    }
}
```

## 16:15 — Set up in the initializer instead

```swift
// onAppear — GOOD: configure in init so the view is reasonable before it appears.

struct StepView: View {
    @State var viewModel: StepViewModel

    init(id: Step.ID) {
        _viewModel = State(initialValue: StepViewModel(id: id))
    }

    var body: some View { /* ... */ }
}
```

## 16:28 — `task` loads on appear...

```swift
// Diagram loading via .task — fires when the view appears.

struct StepView: View {
    let step: Step
    @State var diagramLoader = DiagramLoader()

    @State var diagram: Diagram?

    var body: some View {
        VStack { /* ... */ }
            .task {
                diagram = await diagramLoader.loadDiagram(id: step.id)
            }
    }
}
```

## 16:41 — ...but kicking the load off in `init` exploits prefetching

```swift
// Diagram loading — start the fetch in init (via a cache-backed loader) so
// prefetching gives it a head start before the view is on screen.

struct StepView: View {
    let step: Step
    @State var diagramLoader: DiagramLoader

    init(step: Step) {
        self.step = step
        _diagramLoader = State(initialValue: DiagramLoader(id: step.id))
    }

    var body: some View { /* ... */ }
}

@Observable
class DiagramLoader { /* ... */ }
```

## 17:25 — Don't keep durable state in view `@State` (lost on scroll-off)

```swift
// Highlighting — BAD: isHighlighted is lost when StepView scrolls off-screen.

struct ContentView: View { /* ... */ }

struct StepView: View {
    let step: Step
    @State var isHighlighted = false

    var body: some View { /* ... */ }
}
```

## 17:33 — Lift durable state to an outer view via a binding

```swift
// Highlighting — GOOD: the set of highlighted IDs lives in ContentView and is
// passed down with a binding, so it survives scrolling.

struct ContentView: View {
    @State var highlighted: Set<Step.ID> = []

    var body: some View { /* ... */ }
}

struct StepView: View {
    let step: Step
    @Binding var highlighted: Set<Step.ID>

    var body: some View { /* ... */ }
}
```

## 17:54 — Programmatic scroll with a `ScrollPosition` binding

```swift
// Programmatically scroll to showcase — works even when the target is off-screen.

struct ContentView: View {
    @State var scrollPosition = ScrollPosition()

    var body: some View {
        ScrollView { /* ... */ }
            .scrollPosition($scrollPosition)
            .overlay(alignment: .bottom) {
                Button {
                    scrollToShowcase()
                } label: { /* ... */ }
            }
    }

    func scrollToShowcase() {
        withAnimation {
            scrollPosition.scrollTo(id: "showcase-header")
        }
    }
}
```

## 19:08 — Layout that changes after appearance hurts scrolling

```swift
// Don't change layout after views appear — BAD: onGeometryChange feeds a state
// value into a second layout pass, so the view's height shifts post-appearance.

struct ContentView: View { /* ... */ }

struct StepView: View {
    let step: Step
    @State var subtitleHeight: CGFloat?

    var body: some View {
        VStack {
            StepDiagram(diagram: step.diagram)
                .frame(height: diagramHeight(subtitleHeight: subtitleHeight))
            Title(step.title)
            Subtitle(step.subtitle)
                .onGeometryChange(for: CGFloat.self, of: \.size.height) { _, value in
                    subtitleHeight = value
                }
        }
    }
}
```

## 19:44 — Use a custom `Layout` instead

```swift
// Don't change layout after views appear — GOOD: a single-pass custom Layout
// computes the arrangement without a state round-trip.

struct ContentView: View { /* ... */ }

struct StepView: View {
    let step: Step

    var body: some View {
        StepLayout {
            StepDiagram(diagram: step.diagram)
            Title(step.title)
            Subtitle(step.subtitle)
        }
    }
}

struct StepLayout: Layout { /* ... */ }
```

---

## API surface surfaced by the code

- `LazyVStack(pinnedViews: [.sectionHeaders])` pins `Section` headers.
- `.scrollTransition { effect, phase in ... }` — `phase.value` drives the effect; keep transforms inside the original frame.
- `.onScrollGeometryChange(for:_:action:)` exposes absolute `geo.contentOffset` (estimated — avoid).
- `.onScrollTargetVisibilityChange(idType:threshold:_:)` reports visible IDs relative to the visible region (preferred).
- `ScrollPosition()` + `.scrollPosition($binding)` + `scrollPosition.scrollTo(id:)` for programmatic scrolling, even to off-screen targets.
- `.onGeometryChange(for:of:_:)` — fine in general, but a layout-driving state round-trip in a lazy stack subview hurts scrolling.
- `Layout` protocol (custom `StepLayout`) is the escape hatch for single-pass measurement.
