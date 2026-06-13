---
title: "SwiftUI Group Lab — Full Digest"
session: WWDC26 · 8006
url: https://developer.apple.com/videos/play/wwdc2026/8006/
duration: 01:00:49
sources: transcript.md (whisper.cpp ggml-large-v3, session SD video audio), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from the host's routing ("I'll start with you, Taylor") and context. whisper's auto-inserted speaker labels (e.g. "Sima Shah," "David East," "Matt Sullivan") are artifacts, not roster members.
---

# WWDC26 · 8006 — SwiftUI Group Lab

> A 60-minute live developer Q&A with a six-person UI Frameworks panel. Not a
> feature talk: it's the mechanics underneath SwiftUI's update model, told as
> answers to upvoted questions. The recurring lesson is "your view body is a
> *description*, re-evaluated cheaply; performance is about scoping what gets
> invalidated." Recurring refrains: **file feedback**, **bring code-specific
> questions to the forums**, **stay incremental and use interop**, and
> **trust-but-verify AI tooling**.

## TL;DR

- **There is no architecture SwiftUI expects you to adopt.** Not MVC, not MVVM.
  SwiftUI is architecture-agnostic; if your architecture integrates poorly, that's
  a feedback item. Keep views a projection of a robust, testable data model, and
  let features (not architecture) drive decisions. Persistence, sync, and whether
  you need a **CRDT** for simultaneous edits should shape the data model.
- **A separate `View` struct is not the same as a computed `@ViewBuilder` property.**
  A view has its own identity and tracks dependencies independently, so SwiftUI can
  scope invalidation to it. A computed property is "as if it was still in your view
  body": SwiftUI is just executing a function and can't intervene. Views are structs
  on the stack, so making *more* of them is cheap; there's no "too many views" rule.
- **`@ContentBuilder` is a type alias to `@ViewBuilder`.** It collapses the overloads
  the compiler must consider ("a cave became a hallway") and fixes the notorious
  "expression too complex to type-check" error. It **backports to the beginning of
  SwiftUI** and now works **outside views**, so you can build your own SwiftUI-like
  DSLs. The panel admitted they'd previously **capped their own API** (ForEach only
  worked in some types) because adding more overloads worsened type-checking.
- **Body re-evaluation is not re-rendering.** Updates are driven by **state change**,
  not by diffing a rendered tree ("we're not diffing the DOM of an HTML hierarchy").
  SwiftUI keeps an internal **graph of nodes (views) and edges (inputs)** and figures
  out what actually changed.
- **`if` inside a `ForEach` breaks the static count** SwiftUI needs for internal
  operations, forcing it to iterate the whole ForEach. Filter up front for a constant
  number of views, or wrap variable content in an HStack/ZStack. There's a **debug
  flag** for this.
- **`self._printChanges()` keeps its underscore on purpose**: it's a signal *not to
  ship it*. There's also `_logChanges` (via os_log). For real mystery re-render
  storms, reach for the **SwiftUI Instrument** (view tree + time-weighted flame graph)
  or throw a trace at an **agent** that "doesn't get bored" reading 10,000 lines.
- **Views are `@MainActor`, so `@State` is main-actor isolated.** A few SwiftUI
  closures are **`Sendable`** (run off-main as an optimization, e.g. animations at
  frame rate) and need an explicit capture-list copy of state. **Approachable
  Concurrency + default main-actor isolation** means new apps no longer need to
  hand-annotate observable classes with `@MainActor`.
- **iOS 27 adds a SwiftUI navigation-transition API** (crossfade and other custom
  transitions). Back-deploy via UIKit interop.

## Panel

Host **Kurt** (Worldwide Developer Relations) with engineers and leaders from the
**UI Frameworks team**: **Aditya** ("Adi," 10+ years of UIKit, the
UIKit-to-SwiftUI perspective), **Jason** (views, builders, button styles, opaque
types), **Taylor** (layout, navigation, lazy layouts, compositing/drawing groups),
**David** (the update graph internals, ForEach, Canvas), and **Seema** (the panel's
concurrency expert). A behind-the-scenes team triaged inbound questions.

## Developer Q&A

The host read upvoted questions and routed each to a named panelist, then opened it
to the table. Askers are named only when the host stated a name or username.

### Architecture & data model

- **Is there an architecture (MVC, MVVM, other) the SwiftUI team expects? (UJ).**
  No. SwiftUI is **architecture-agnostic**; whatever works for your app should work.
  If it integrates poorly or has ergonomic friction passing data around, file
  feedback. (Seema) A small app shouldn't reach for an elaborate architecture that
  forces a big refactor; a large app with a team committing daily may want something
  more standardized across the codebase. The underrated thing is staying **nimble**:
  don't build your app *around* an architecture, ship features to users. Structuring
  your data model only around the UI framework is "maybe not the leading way":
  account for **persistence, syncing, whether you need a CRDT** for simultaneous
  edits. Keep views "just a projection of your data model to pixels" and make the
  data robust and testable.
- **Does adopting Observable with AppKit/UIKit have architecture implications? (host
  to David).** You can use Observable to store your model and ease a later transition
  to SwiftUI, but it's "just Swift code": integrated with UI frameworks, not dependent
  on them. So you can write tests for it, use it in other components, and listen to
  changes yourself. Tied to a David WWDC26 talk on using Observable with AppKit/UIKit.

### Views, builders & anti-patterns

- **@ViewBuilder closure / computed property vs a separate view struct, performance
  pros and cons?** ("apologies if this is basic" — "no apologies needed.") A large
  body should be broken up; people often make a computed `@ViewBuilder` (now
  `@ContentBuilder`) property. But a **view has its own identity and independently
  tracks dependencies**: you pass in the data specific to it, and SwiftUI (which calls
  its `body`) scopes dependency tracking and invalidation to that view. A computed
  property "really is, in effect, just as if that was still in your view body" — just
  moved to a separate method; SwiftUI is "just executing a function" and can't
  intervene if that function calls other functions. (Jason, David, Seema) **Trade-off
  / how small is too small?** No recommendation on minimum view size. There's a
  **preview benefit** (finer-grained previews) on top of the performance benefit.
  Each view is a struct **allocated on the stack**, "very inexpensive to allocate,
  render, and discard"; the fewer pieces of state a view depends on, the less likely
  it is to redraw. Think of a body as "a description of your views… a data model for
  your views," short-lived and lightweight. The same extraction concept applies to
  **modifiers** (extract repeated modifiers into a custom modifier). Custom views also
  let you **read the environment** — example: the **`backgroundProminence`**
  environment value, which increases when a list/table row is selected, so you can
  adapt a color to stand out against the darker selected background. (Aditya)

- **Common anti-patterns? (host to Taylor, then everyone).**
  - **GeometryReader** where a **`Layout`** (introduced several years ago) would do,
    so you don't invalidate the whole hierarchy on layout change. (Taylor)
  - **`onChange`** used to "trampoline data back and forth." Not always wrong, but
    analyze whether there's a better, more declarative substitute. People reach for
    `onChange` because it's "comfortable imperative territory" when moving from an
    imperative framework. (Taylor)
  - **Frequently-changing environment values.** Every environment change re-evaluates
    every view that reads that environment — *and* SwiftUI must invalidate readers
    even if the body doesn't actually read the value, just because the property was
    declared. Reduce frequency or remove unneeded readers. Passing an **`@Observable`
    object** down through the environment fixes this: the pointer is stable, so the
    environment is stable, and views read individual properties. (Seema, plus the
    table)
  - **Wrapper views around buttons.** People make "my custom button view" = a button
    plus modifiers. Prefer a **custom `ButtonStyle`**: pass `configuration` through to
    another button, or apply modifiers to `configuration.label`. You keep all the
    button initializers (localization, system images) for free, and modifiers applied
    to a button differ from modifiers applied to its label (e.g. **padding belongs on
    the label or in the style**, not on the button). (Jason)
  - **Rewriting stock components from scratch** (easier than ever with AI tooling)
    when a `List` or standard component gives you a lot out of the box — including
    **automatically evolving with this year's new design** with no extra work. Old
    forgotten wrappers (written "five or six years ago") cause bugs where "neither us
    from Apple nor the developer… can seem to figure out why something isn't changing"
    — half an hour of debugging to find a wrapper everyone forgot.
  - **Rewriting in SwiftUI instead of using interop.** Pulling in a UIKit/AppKit view
    "is not an anti-pattern. That is what we would recommend doing."
  - **Conditional modifiers.** An `if` that applies a modifier in one branch and not
    the other is acceptable only if the condition never changes after init; if it
    changes on screen it **recreates the view** — breaking animations, re-initializing
    state, and a performance concern. (David) Pointer to **Renz's lazy-stacks talk**
    for things that accidentally slow a lazy stack. ("Thanks for that question, Ozzy.")

### Mental models people trip over

- **One concept that takes longest to learn? (down the line, starting with Seema).**
  The **hidden update graph**: an internal representation with edges (inputs) and
  nodes (views) that figures out which parts of the hierarchy changed from a state
  change. Worth being aware of when you move into performance tooling and Instruments
  to reason about "why is my view updating more than I'd expect." Updates are about
  **state change, not re-rendering and comparing views** — "we're not diffing the DOM
  of an HTML hierarchy." (Seema)
- **(David)** With **`ForEach`**, people write an `if` in the body to filter. It looks
  right, but SwiftUI often needs the **total element count** for internal operations,
  and an `if` (or `AnyView`) forces it to iterate the whole ForEach. Fix: filter up
  front for a constant number of views, or wrap variable content in an HStack/ZStack
  so the count is constant. The docs have examples and **a flag you can pass to debug
  this**.
- **(Jason, riffing on conditional modifiers)** Your body is a **description of the
  view in all the states it can be in**. Most modifiers have an **"inert" identity
  value** (opacity 1 = fully opaque). Instead of adding/removing an opacity modifier,
  pass a value via a **ternary** (1 or 0). This generalizes to almost any modifier and
  **helps animation** between states. If something lacks an inert version, file
  feedback.
- **Body re-evaluation ≠ re-render/redraw.** Re-evaluating a body doesn't necessarily
  mean anything is redrawn; SwiftUI handles that. Easy to misjudge if you come from
  another framework where body = a big task.
- **(Aditya, the UIKit "impedance")** UIKit lays out and renders **top-down** (window
  down to the smallest child). SwiftUI tends to go **the other way**: start at the
  innermost node and build outward. This matters in a **"sandwich" or "cake"** — Adi's
  term for interop layers alternating between frameworks — so consider how data flows
  and where you invalidate. The layout system asks subviews "how big do you want to
  be" down to the leaf nodes, then flows back up. Pointer to **Paul's custom-layout
  video from WWDC23**, and a note that Aditya did "What's… doing SwiftUI" that year.

### Cell reuse & prefetching

- **In UIKit, cell reuse was fundamental for scrolling. Are lazy stacks/grids enough
  for an infinitely scrolling grid, or drop down to a hosted UICollectionView?
  (Tyler, to Adi).** UIKit reused cells because views are **heap-allocated** — a fixed
  up-front cost per cell, plus add/remove overhead. SwiftUI doesn't have that issue.
  (Aditya) SwiftUI also has **implicit prefetching**: while you scroll one direction
  it looks at which cells come next and starts evaluating their bodies. Because the
  graph is individual nodes, it can do **partial evaluation** — render the current
  frame, then use leftover time before the next frame to keep evaluating the next
  cell's graph, stopping right before the next frame begins. (Seema, with the table
  "nerding out" that this is "one of the coolest things about this graph model.")
  Pointer to **Renz's talk** for an animation of this. **Perf gotcha (Jason, learned
  working with Renz):** an `onAppear` that reconfigures a cell's state and changes its
  size forces re-layout and **throws away the prefetch work** from prior frames. Do
  that work in **`init`, not `body` or `onAppear`** — both `onChange` and `onAppear`
  have to go into the view body, forcing a recompute to run their closures.

### @ContentBuilder & type-checking

- **The change from @ViewBuilder and other builders to @ContentBuilder — how does it
  work and why did it improve type-checking? (to Seema).** "What's New in SwiftUI" has
  a visualization. The compiler now considers **fewer overloads** when type-checking
  a body, fixing the **"expression is too complex to type-check"** error. The work
  let them **collapse the overloads**: Group, Section, ForEach used to each carry
  their own ViewBuilder overloads; now there's **one**. (Seema) **Candid admission:**
  they'd previously **limited their own API** because of this — "we had a ceiling…
  we can't introduce more because it's gonna worsen [it]," and **ForEach only worked
  in some types** because they couldn't add it anywhere else. Analogy (Stephen, via
  the host): a view full of groups/sections/ForEach was **a cave the type-checker had
  to explore every path of**; now it's a **hallway** — "content builder all the levels
  down until you know that it's a view." It's effectively a **type alias to
  ViewBuilder** (which "got a lot smarter"), so it **backports to the beginning of
  SwiftUI**.
- **Any reason to keep using @ViewBuilder? Reason to choose? (to Seema).**
  `@ContentBuilder` and `@ViewBuilder` are **the same** — just a type alias. What
  ContentBuilder adds: you can use it **outside your views** to build **custom
  SwiftUI-like DSLs** whose building blocks aren't necessarily SwiftUI views. ("I
  learned something, thanks for that question.")

### Debugging re-renders

- **What's the real workflow for a mystery re-render storm — instruments,
  `_printChanges`, or some secret technique? (Ozzy/throwback question, to Jason).**
  "All of the above." A handy trick: a **random color background** so you can see
  when a body re-evaluates (try it on resize). There's also **`_logChanges`** (uses
  **os_log**) alongside `_printChanges`. `self changed` in the output just means the
  *value of that view* changed; crawl up to the parent to see why. (Jason) The
  **SwiftUI Instrument** is great for this, not just perf: capture a trace and "you
  get this giant graph of exactly what caused your body to re-evaluate," often simpler
  than print-changes. You can also **throw a trace or crash report at an agent** —
  "the problem is one line in 10,000… we get bored reading that, the agent doesn't,"
  and it sometimes catches the thing you overlooked. The Instrument can show the **view
  hierarchy as a tree** and a **flame graph weighted by time spent in each view**.
  Real example: profiling the **TV app**, the panelist saw **sliders updating when
  there were no sliders on screen** — a strong signal something's worth investigating
  ("either a bug in the app or in your understanding of it").
- **Follow-up: why the underscore still on `_printChanges`?** It's a **signal not to
  ship it** — don't submit to the App Store with it; you don't want users' consoles
  full of these. There's also **performance overhead**: it's not an optimized path,
  the string has to be generated and written out.

### Concurrency & state

- **Property wrappers feel like opaque magic; if I don't grasp how @State manages
  lifecycle, how do I safely adopt Swift concurrency? (MVC-background asker, to
  Seema).** SwiftUI tries to make concurrency simple: **views are `@MainActor`**, so
  everything declared in them — all your state property wrappers — is **main-actor
  isolated**. The one snag: some SwiftUI closures are **`Sendable`** (as an
  optimization, executed off the main thread), and are annotated as such. Using
  `@State` values inside them can surface concurrency errors because the state value
  is **conditionally Sendable** (only when its value is Sendable). The fix: an
  **explicit capture list** copying the values into the closure, telling the compiler
  to use the copy. But these closures are **rare** — most SwiftUI closures are
  main-actor, so you normally won't hit this. (Seema) Pointer to **Daniel's and
  Seema's concurrency talks last year**; Daniel's covered sendable closures. The one
  place they're used is **animations**, which run at frame rate, taking them out of
  the main rendering loop.
- **Do @Observable properties (held in @State) get allocated on the right thread
  automatically, or should I mark types `@MainActor`? Where should state live? (to
  Seema).** Since views are `@MainActor`, bodies run on the main thread at runtime, so
  an **`@Observable` class in `@State` is automatically allocated on the main thread**.
  With **Approachable Concurrency** and **default isolation set to main actor** (now
  the setting for new apps), main-actor is **inferred** for everything including your
  custom observable classes — so you no longer need to **manually annotate them with
  `@MainActor`** (which adopting Swift 6 used to require). (Seema) **Addition (David):**
  because state is accessed on main, you want **atomic updates** — don't update part
  of an observable from a background thread mid-render. Factor the **background
  computation into a separate function**, `await` the result, then jump back to main
  and update. State updates in your view should be **mostly synchronous** so SwiftUI
  produces better animations and you avoid hitches. **Test on-device with a release
  build** at least sometimes: a lot of perceived quality is "feel-based," and crisp
  updates matter.

### Navigation

- **Recommended way to do navigation in iOS 27 if custom transitions are required —
  SwiftUI, UIKit + SwiftUI? (to Taylor).** Depends on your app. **iOS 27 adds a new
  SwiftUI API for customizing navigation transitions**, with built-in **crossfades**
  and other custom ones — if that fits, "iOS 27 has your back." If you need to
  **back-deploy** before this API, that's where **UIKit interop** ("layer cake")
  helps. (Taylor) Navigation recommendations are hard in the abstract: bring a
  specific question to the **forums** (there's a SwiftUI forums Q&A tomorrow). Also
  (from the State of the Union on Monday): **resizability** matters — iPhone apps
  resize on iPad and in iPhone mirroring, so prefer system navigation components,
  which handle resizing/adapting for you.

### Scroll tracking

- **Recommended way to track a scroll view's offset, to show elements based on scroll
  distance? (to David).** Different APIs for different needs: **`onGeometryChange`**,
  **scroll effects** (which also offload work and can be more efficient), and
  **`scrollPosition`** to see which item is on screen. There's also an API (name not
  recalled, covered in **Renz's talk**) to track **what percent of a view is visible**
  — great for **analytics/impressions**: apply it to the first/last view and trigger
  when it crosses, say, 80% visible, instead of tracking content offset. **Content
  offset is estimated** (based on estimated heights of off-screen views) and, with
  lazy stacks, should be **treated as an implementation detail with no semantic
  meaning** — focus on something relative to the views on screen. `scrollPosition`
  takes an **ID, not an offset**, keeping you relative to your data model. (David)
  Related: for **infinite scrolling / paging**, add a view at the bottom of the list
  with an **`onAppear`** that tells your networking layer to fetch more and updates
  the data model — page by the last view becoming visible.

### Custom lazy layouts

- **Can you build custom layouts that are lazy, like LazyVStack? (to Taylor).**
  **Today, no** — there's no protocol for a custom *lazy* layout. **File feedback**
  with your use case. For now: **interop a UICollectionView** if you need it, use the
  suite of built-in lazy grids/stacks, or use the existing **non-lazy `Layout`
  protocol** (much better than arranging things with GeometryReader and offsets). You
  can also **compose**: a built-in lazy container with a **custom `Layout`** inside for
  a sub-piece, getting laziness from one and custom layout from the other (e.g. "pretty
  cool mosaic layouts" that look more complex than a stack or grid). (Taylor)

### Large-app performance

- **Most common mistake that hurts performance in large SwiftUI apps? (to David).**
  Usually **too many invalidations cascading from the top down**. Examples: something
  high in the **environment** read by many views that **updates too often** — a "very
  bad" hypothetical is putting **time in milliseconds** there; a real one they've seen
  is putting **scroll position in the environment** (updates every frame while
  scrolling). Another: a **complex value type** with "high arity" passed down through
  many views — each level needs an **equality comparison**, even views that just
  forward it. Using **`@Observable`** (a reference type) fixes this: the **pointer is
  unchanged**, so only views reading specific properties update. (David) "Maybe
  surprising to folks deep into Swift value types: there are cases where a **reference
  type is the right choice**, and that's why Observable is a reference type." Observable
  in the environment lets you update something frequently while the environment itself
  stays static. **Resizability** (Mac, iPad, now iPhone) is another smooth-experience
  expectation: **avoid GeometryReader**, which updates the view's body on **every frame**
  during resize. Same disco-dance-party random-color trick to spot invalidations during
  resize. And **avoid large view bodies** — the scope of invalidation is the whole body
  plus everything it calls.

### Graphics: timeline, shaders, Canvas

- **(David, volunteered)** **`TimelineView`** is a great, optimized API when you need
  something that **updates every frame**. Pointer to **Hao Qian's (Haojian) advanced
  graphics & animation talk** — "the best explanation of shaders I've personally ever
  seen," and of alignment guides — which puts a **shader inside a TimelineView** for a
  flowing background on a sample podcast app.
- **(panel, on shaders)** A favorite SwiftUI feature: **integrating your own custom
  shaders**, rare in other frameworks. Shaders can be used as **foreground or
  background styles**, so you can render the **glyphs of text** with a shader and
  **animate a gradient flowing through the text**. Combine with a **text renderer** to
  move and resize the glyphs. ("Now you get a sense of what we all do in our spare
  time.")

### some View vs any View

- **In a multi-module app we return `any View` from protocols to hide concrete types
  and avoid cross-module dependencies. Is there a more idiomatic way without `any
  View` — @ViewBuilder closures, `any View` existentials? (to Jason).** Prefer **`some
  View`** if you can. If not, **`any View` is fine as long as the underlying type
  isn't changing** — avoid dynamic type changes (in line with the conditional-modifier
  advice). (Jason) **Why it matters (David):** with `any View` there's **no static type
  information**, so **`ForEach` can't get its static count**; wrap an `any View` in an
  **HStack/ZStack** to tell ForEach it only ever returns a single view, for better
  performance. SwiftUI's own APIs almost always return **`some View`** — an **opaque
  type** the type-checker knows the concrete type of without exposing it. With `some
  View`, **the compiler catches you if you try to change the type**.

### Compositing group, drawing group, Canvas

- **Performance trade-offs (speed, memory) of a compositing group vs a drawing group?
  (to Taylor).** **CompositingGroup is not about performance** — it's about **how
  visual effects apply.** Apply a shadow to a ZStack and SwiftUI applies it to **each
  element**; a compositing group makes it apply to the **overall result**. (Taylor)
  **DrawingGroup** (from SwiftUI's first year — the "pie-chart-esque" custom-graphics
  demo) **flattens many rendered layers into a single drawing layer**, so the same
  number of SwiftUI views render far faster; **gestures and the usual APIs still work.**
  **Canvas** is similar and like UIKit/AppKit's **`drawRect`**: direct control over each
  drawing invocation. Trade-off: Canvas drawings are **not SwiftUI views**, so you
  **can't attach sub-gestures** to individual pieces. Pointer to **David's DubDub talk**
  building a **Canvas-based color picker** — the drawing code is in the talk's **code
  snippets, not the slides**. There, one overall gesture covers three sub-sliders (so
  you do math to find where a drag starts), and **`accessibilityRepresentation`** (a
  favorite API) exposes the canvas as **three real sliders** so you don't hand-build a
  fake accessibility hierarchy.

## Unconventional facts & takeaways (the live-Q&A gold)

- **"No architecture expected" stated flatly.** SwiftUI is architecture-agnostic; poor
  integration with your architecture is a feedback item, not a you-problem.
- **`@ContentBuilder` is literally a type alias to `@ViewBuilder`** and **backports to
  the start of SwiftUI**. ViewBuilder "got a lot smarter"; the win is fewer overloads,
  not a new type.
- **Apple capped its own SwiftUI API to protect type-checking.** "We had a ceiling…
  we can't introduce more because it's gonna worsen it," and **ForEach only worked in
  some types** until this year. A candid internal-constraint admission.
- **`if` inside `ForEach` (and `any View`) silently breaks SwiftUI's static count**,
  forcing full iteration; wrap in HStack/ZStack or filter up front. There's a debug flag.
- **The underscore on `_printChanges` is intentional**: a ship-blocker signal, plus
  real (unoptimized) string-generation overhead.
- **Concrete debugging anecdote:** profiling the **TV app** revealed **sliders updating
  with no sliders on screen** — the panel's example of "trust your intuition about what
  shouldn't be updating."
- **Cell-reuse is largely obsolete in SwiftUI** because views are stack-allocated
  structs, plus **implicit prefetching** and **partial graph evaluation** that uses
  leftover time between frames.
- **Perf gotcha learned on-air:** an `onAppear` that changes a cell's size **throws away
  prefetch work**; push setup into **`init`**, not `body`/`onAppear`.
- **`@Observable` is a reference type on purpose** — the stable pointer avoids
  per-level equality comparisons of large value types and avoids invalidating
  forwarding views. "A case where a reference type is the right choice."
- **Sendable SwiftUI closures run off-main** (e.g. animations at frame rate) and need a
  capture-list copy of `@State`; otherwise everything is main-actor.
- **Approachable Concurrency + default main-actor isolation** removes the manual
  `@MainActor` annotation that Swift 6 adoption used to require on observable classes.
- **Content offset is *estimated*** (from estimated heights of off-screen views) and is
  an **implementation detail** with lazy stacks — use `scrollPosition` (by ID) or a
  percent-visible API instead.
- **AI / agents are a first-class debugging tool here**: throw a trace or crash report
  at an agent that "doesn't get bored" reading 10,000 lines. But also a *cause* of
  anti-patterns: AI makes it "easier than ever" to rewrite a stock component you should
  have reused.
- **Interop is endorsed, not a fallback.** Pulling in a UIKit/AppKit view "is not an
  anti-pattern. That is what we would recommend doing." Adi's **"sandwich/cake"** =
  alternating framework layers.
- **Cultural color:** the table "nerding out" over partial graph evaluation; "now you
  get a sense of what we all do in our spare time" (shaders flowing through text); the
  "cave became a hallway" type-checking analogy; old forgotten wrapper views taking the
  team **half an hour to debug** in workshops.
