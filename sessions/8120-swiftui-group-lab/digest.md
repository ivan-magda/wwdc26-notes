---
title: "SwiftUI Group Lab (8120) — Full Digest"
session: WWDC26 · 8120
url: https://developer.apple.com/videos/play/wwdc2026/8120/
duration: 01:02:35
sources: transcript.md (whisper.cpp ggml-large-v3, session SD video audio), meta.md
compiled: 2026-06-13
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and host routing. Whisper renders some names inconsistently ("Stephen"/"Steven") and may invent spoken first names; only intro-round names are treated as the roster. Garbled terms flagged inline.
---

# WWDC26 · 8120 — SwiftUI Group Lab

> A ~62-minute live developer Q&A the hosts nickname "SwiftUI After Dark." A
> six-person UI Frameworks panel works through about a dozen upvoted developer-forum
> questions. There is no warm-up round and no slides: it's the unscripted layer under
> the WWDC26 SwiftUI sessions, full of "why we named it that," performance mental
> models, candid limitations, and a steady ask to **file feedback with the actual use
> case**, because the team already knows the bugs and wants the *why*.

## TL;DR

- **The 1M-item List slowdown is a known bug, not user error.** A List over a million
  reorderable items still traverses the whole collection on first display and on every
  count/order change. The panel: "it definitely shouldn't be working in that way,"
  it's being looked into, file feedback with your data shape and reason for the size.
- **You can't write a custom navigation transition yet.** `NavigationTransition` is a
  **publicly empty protocol** in iOS 27 beta 1; SwiftUI vends concrete ones (crossfade,
  zoom, automatic) but no hook to build your own. The sleeper win this year is a
  composable **"any navigation transition"** that lets you switch between transitions,
  which you couldn't do before. Crossfade and zoom now apply to **sheets** too.
- **Liquid Glass belongs on the control layer, not in content.** Toolbar / nav-bar
  buttons take glass automatically; glass in the content area has nothing scrolling
  under it to refract, so it's just static — often the flat, simpler design is better.
  For a glass button use **`glassButtonStyle` / glass-prominent**, not a raw
  `glassEffect`, then a **button border shape**. `sharedBackgroundHidden(true)` drops
  the glass behind things like a profile photo.
- **Prefer `onGeometryChange` over `GeometryReader`, and `DynamicProperty` over piled-up
  `onChange`.** `GeometryReader` invalidates its subviews every frame; `onGeometryChange`
  has a second closure that collapses per-frame geometry into breakpoints so the action
  fires only on threshold crossings. A `DynamicProperty`'s `update()` runs **right before
  `body`**, saving a render cycle (and enabling cache-hit image loads on first body).
- **`if` and `AnyView` are fine — until a lazy container has to count views.** In List /
  LazyStack a top-level `if` (or `AnyView`, group, custom layout) can resolve to a
  *variable number* of subviews, so SwiftUI can't position them and falls off the fast
  path. Wrap in a unary container (a VStack) to guarantee exactly one view.
- **Split views to isolate dependencies, not just for tidiness.** `@ViewBuilder` computed
  properties give **no** isolation benefit; only a real child-view boundary lets SwiftUI
  short-circuit work when its inputs are unchanged. Views are cheap value-type structs.

## Panel

Host **Kurt** (Worldwide Developer Relations; previously worked on presentations and
navigation) with the UI Frameworks team: **Sam** (programming languages × SwiftUI;
this year's type-checking performance work), **Russell** (long-time UIKit; animations and
the UIKit↔SwiftUI interop/bridging layer), **Nick** (ex-AppKit/UIKit, now a SwiftUI
manager; presentations and containers — Sheet, Inspector, Navigation, Controls — and
data flow this year), **Stephen** (ex-Apple Music app engineer; now SwiftUI performance
and the agentic coding skills), and **Anna** (navigation, bars, tabs). A triage team
works behind the scenes on inbound questions. (No diarization; names are from the intro
round only. "Stephen" is also transcribed "Steven.")

## Developer Q&A

### Lists, collections & performance

- **1M reorderable items: List traverses the whole collection (forum question).** The
  asker noted a List over a million reorderable items renders only visible views but
  still traverses the entire collection on first display and on every count/order change,
  that UIKit's diffable data source has the same issue, and only a classic data source
  helps. Answer (Stephen): this is a **known issue being looked into; it definitely
  shouldn't work that way; file feedback** so it can be tracked. The follow-on point
  (host): feedback isn't to make them aware of the bug — they know — it's to learn **your
  use case**: why display that many items, what's the nature of the data driving that
  structure. "Bravo to this developer for downloading the betas and testing." They read
  incoming feedbacks daily ("we've already gotten a couple on `@State` macro as well"),
  and **right now is the best time to file feedback that influences the next release.**

### Navigation transitions

- **`NavigationTransition` protocol is publicly empty — what's it for? (forum question).**
  Answer (Anna): it's how SwiftUI exposes conforming types so they can be navigation
  transitions, similar to how some protocols/styles give you pieces to build your own
  (e.g. a button style) while others only vend concrete members. You can use a **zoom**
  navigation transition, but there's **no public way to create a custom one right now**.
  File feedback with what you're trying to build so they know which pieces you'd need.
- Today's set is **crossfade, zoom, and automatic** ("the trifecta"). The sleeper feature
  this year (Russell-style enthusiasm, unattributed): a composable **"any navigation
  transition"** lets you **switch between** transitions, which wasn't possible before.
  Crossfade applies to **sheets** too; zoom as well.
- On the name: it was deliberately called **navigation** transition after a lot of
  back-and-forth ("presentation transition, navigation transition, transition transition")
  because **navigation was the unifying concept — sheets are navigation.** Aside: there's
  no first-class SwiftUI API for custom transitions the way UIKit has one, **but** if your
  view isn't pushing onto a navigation stack it's easy and fun to build your own: drive
  the animation yourself, flex shapes with modifiers, use **shaders**, lean on
  **`Animatable` and keyframe-based animations**.

### Liquid Glass, toolbars & agentic skills

- **Using coding intelligence / agentic models to build Liquid Glass views (forum
  question).** Answer (Stephen): the **new Xcode skills** are a good way to get insight
  into new APIs and best practices (feedback on the skills welcome). Xcode's tooling
  renders **Liquid Glass previews** so you can iterate the design toward the look you
  want. Skills work with the **model of your choice** (results vary by model, so
  experiment). They're built into Xcode's agentic workflows but **exportable** to other
  agentic systems via a command "listed in What's New and in Docs." Skills called out:
  new-API insight, best practices, and **"modernize your UIKit app."** They're also "fun
  to read through" with "tidbits for humans," not just LLMs.
- **`glassEffect` on buttons (second half of the question).** Key guidance: buttons in
  **toolbars / the navigation bar take on the appropriate glass automatically**, so the
  `glassEffect` modifier is for things **not** in those locations. In general **don't put
  Liquid Glass in the content area** — think of the glass control layer as *hugging* the
  content layer, which stays flat. If nothing scrolls under the glass there's **nothing to
  refract**, so it's just static; the simpler design is usually better in content.
  - For glass on a button specifically, use the **glass button style** — `glassButtonStyle`
    or **glass-prominent** (cited example: FaceTime's green button). A common mistake:
    reaching for `glassEffect` on a button gives you a "button **on** glass" that doesn't
    look right; you want the button glass style, then a **button border shape** (capsule,
    circle, etc.).
  - In a toolbar item a button already has a glass background — don't add another; to tint
    it like a prominent glass button, just use a **tint**.
  - **`sharedBackgroundHidden(true)`** puts content in a toolbar **without** the glass
    background. Main case (example: the Books app profile photo): glass around/behind a
    photo of a person doesn't look great, so this makes it flat. New this year: API to
    **remove just the toolbar content margin** (the padding) while keeping the background,
    so the glass refraction sits right at the photo's edge and the avatar can fill the
    button — which also makes the **push/pop "mitosis"** look more natural since it's all
    glass. ("Mitosis" = the liquid blob-together/blob-apart effect; jokingly also called
    "schlerp.")

### Adaptive layout & window sizing

- **Managing screen sizes with resizable windows / split views / the resizable simulator
  (forum question).** Answers, mostly Russell + Anna + Stephen:
  - **Don't hard-code window sizes** ("a losing battle"). If you can lay out at any size,
    do that. **Lean into size classes** — the team is leaning into them more than before —
    as defining *experiences*: regular = full iPad/Mac-like multi-column; compact (vertical
    or horizontal) = constrained, show less (sometimes just fewer columns, sometimes a
    dramatically different app).
  - Size classes alone aren't sufficient: windows take many sizes **between** the class
    transitions, so handle **true continuous flexibility** too.
  - **System containers** let SwiftUI do the adaptation work for you. **Custom layouts**
    are powerful (referenced: the WWDC 2022 custom-layouts talk, "Paul's talk"). The
    combination of **custom layouts + custom containers** makes flexible reusable controls
    (referenced: "Demystify SwiftUI containers," WWDC 2024, "Matt's talk," karaoke-themed).
  - Gotcha: an **`if` off a size class tears down the view on the other branch**, losing
    state (or forcing you to sync state both ways). A **layout** repositions/resizes views
    flexibly **without** throwing away state. For switching whole layouts by size class,
    use **`AnyLayout`**: it wraps the candidate layouts, preserves each view's structural
    identity, can **animate between** them, and works with system layouts.
  - **`ViewThatFits`** is handy but don't use it across many views at once — it measures
    successive candidates to find the one that fits; keep it at a higher level.
  - **Adaptive `TabView`** (shipped two releases back, iOS 18-aligned; Anna built it with
    others): tabs that gain an optionally-collapsible **sidebar** at regular size classes
    and collapse to plain tabs when compact. "A great container that's less appreciated
    than it should be on larger devices."

### GeometryReader vs onGeometryChange

- **When to use `GeometryReader`, and the performance implications (asked of Stephen).**
  - Prefer **`onGeometryChange`** for most "react to size" cases (e.g. resizing grid
    items), because what people usually want is **breakpoints**. Its **first closure** gets
    real-time geometry every frame; whatever you **return** from it determines whether the
    **action** fires — so you map a size range to a value and only relayout when you cross a
    threshold. "Something running at frame rate suddenly just fires twice as you cross."
  - **`GeometryReader`** is expensive for the subviews inside its closure (they invalidate
    every frame). On older releases you'd only use it in a **background** view. You don't
    want views invalidating every frame.
  - For precise placement from bounds, reach for a **custom layout**. The combo
    **`onGeometryChange` + custom layout** "gets you so far." Referenced: this year's
    **advanced-graphics-in-SwiftUI talk ("Hao-Chien's talk")** on **alignment guides** —
    a one-pass, very efficient way to do things you'd think needed a custom layout (vs the
    minimum two passes the geometry/state approach takes).

### Data flow, @Observable & DynamicProperty

- **How to properly learn data management / passing data between views (asked of Sam).**
  - (Sam) At its core, data management is making **pieces of information** and keeping views
    **as lightweight as possible** mapping that info to view data. Build self-contained
    **state machines and logical components** to drive things; when view code gets messy
    bridging back and forth, **pare the model/logic layer down to only what it needs.**
    SwiftUI is **deliberately architecture-agnostic** — not because they won't choose, but
    because there **isn't one correct choice**; it depends on your data's shape. So **build
    lots of things modeling very different data** and you'll find each needs different
    patterns.
  - (host) Build sample apps in a **domain you care about** (the running gag: tracking
    watercolor paints, how often you feed your fish, terrazzo/floor-tile patterns) so you
    understand the data and spend brain cycles on the problem, not on someone else's
    tutorial subject.
  - (Stephen) Use **Instruments** for learning, not just perf debugging: the **SwiftUI
    instrument** shows a **timeline of what's updating**, so you can see which view bodies
    run as you change data and how data flows / how much work SwiftUI does as a result.
  - (Russell-style, on `DynamicProperty`) "Peddling `DynamicProperty` to all who will
    listen." Rule of thumb: **almost any `onChange` can be replaced by good use of
    `DynamicProperty`**, which also saves a render cycle because its **`update()` is called
    right before `body`** — folding what would be (onChange fires → update state →
    re-render) into one pass. Any `onChange`/action closure with extra state costs both
    memory and processing time.
  - Best `DynamicProperty` use case (e.g. loading an image): if it's already in cache you
    don't want to wait for `onAppear` (which guarantees a full re-render). Because update
    runs before body, you can check the cache and have the value **available on body's
    first run**, skipping a round trip. Referenced: this year's **Lazy Stacks talk
    ("Renz's talk")** uses exactly this — moving loading earlier so SwiftUI can instantiate,
    lay out, and render in separate frames and scroll without glitching.
  - On **`onChange` as anti-pattern** (a question from a previous SwiftUI lab): it's not
    always wrong (fine for broadcasting to an external system), but **using `onChange` to
    mutate view state is almost always wrong** — that's reaching back into imperative code.
    Make the work live in an **observable data model** that vends the new value as inputs
    change. The broader stance: don't say "X API is bad"; if Apple shipped it, it's useful.
    When you see `AnyView` ask "am I really erasing a type / using a heterogeneous
    collection, or just dodging generics?"; when you see `onChange` ask "is this genuinely a
    reactive→imperative breakout, or could environment / a `DynamicProperty` do it better?"
  - **`onAppear` is even more commonly misused than `onChange`.** Same cache trick applies.
  - **Timing detail:** new Swift concurrency features make **`task` and `onAppear` fire
    top-down** in declaration order. Previously `onAppear` fired first because `task`
    incurred an isolation hop; that hop is gone, so a stacked `task` + `onAppear` now
    executes in the order written.

### Conditionally hiding views, if-conditionals & AnyView

- **"I keep hearing I shouldn't use `if` to conditionally hide elements" (forum question,
  with the example: show "9:41" only at 9:41).** Answers:
  - To fade something in/out at a moment, use an **`opacity` modifier with a conditional
    value** (1 at the time, 0 otherwise) — or a transition. The view stays in the hierarchy.
  - You only need to actually `if`-remove it when you must **reclaim its layout space**.
    The reason to avoid removing-and-re-adding is it forces a **relayout and re-initializes
    state**; if the view isn't truly leaving (just visually changing), keep it and animate.
  - **Conditionals inside view-modifier bodies are dangerous.** Make modifiers take an
    **inert argument** (e.g. `opacity(1)` does nothing) and put the **conditional in the
    argument**, not an `if` around the modifier. An "`if`-modifier" that wraps a conditional
    branch can inject conditionals "all over the place" and reset state unexpectedly when a
    branch switches. (Some style modifiers intentionally have **no** inert form: they take a
    generic type bound statically so SwiftUI can render more efficiently; switching those on
    the fly is an explicit if/else decision, not an oversight.)
  - **The real "avoid `if`" rule is about lazy containers.** A top-level `if` in a view
    inside a **LazyStack / `ForEach` / List** is a problem because SwiftUI **identifies and
    positions views by count**; an off-screen view with a top-level conditional can't be
    counted, so SwiftUI must keep it around to know whether the conditional changed.
    (Referenced: Lazy Stacks talk again.) Fix: wrap the whole thing in a **unary container
    (VStack / a layout)** so it always resolves to exactly **one** view and you get the fast
    path. The same applies to **`AnyView`** (could be 1 or 10 views under the hood),
    **custom layouts**, and **`Group`** (which intentionally doesn't flatten — so a custom
    view can expand into multiple list rows; important, intentional functionality). Note:
    you **can** return multiple views from `body`, which is still "multiple views."
  - Bonus data-flow aside: beyond environment, there are **preferences** and **container
    values** (transcript first said "traits," corrected to **container values**) that flow
    **upward** to a surrounding container — but wrapping in a VStack stops an outer container
    from reading them, a thing to check when debugging. (Both the view-counting behavior and
    container-values API are in the WWDC 2024 demystify-containers talk.)

### Cost of AnyView type erasure

- **Concrete cost of `AnyView` in deeply nested / frequently updating views (asked of
  Sam).** Type erasure itself is fine and sometimes necessary (e.g. the type isn't known at
  compile time). The case to watch: when the **underlying type changes**, SwiftUI must
  **tear down and rebuild** that part of the hierarchy (same as if/else), which isn't
  efficient — so avoid changing the underlying type. The other watch-out is the lazy-
  container counting problem above; wrap in a stack to fix it.

### Adaptive layouts with resizable Xcode previews

- **Tips for adaptable layouts now that Xcode previews are resizable (asked of Nick).** The
  panel reframes the question as **backwards**: now that previews resize, **resize your app
  and find where you made assumptions** / hard-coded magic numbers, then fix those spots.
  If you write a **custom layout**, the **size is an input parameter** — set it up to do
  something reasonable at any size and you're already most of the way; then test it at all
  sizes. Also use the app in genuinely resizable contexts (iPad, **iPhone mirroring**) to
  surface breakage. Inspiration: study how other apps — especially **indie apps**, free to
  be creative without a product/design team — adapt to compact space.

### Full-screen overlays above sheets

- **Presenting a full-screen SwiftUI overlay on top of everything, including sheets,
  without interfering with modal presentation (forum question, "a hard one").** Honest
  answer (Anna): **not really expressible in pure SwiftUI today.** Common real case: a
  **login UI** that must appear over the app after a network-triggered logout. A
  full-screen cover works if you present it from a base level, but **another modal already
  presented makes it tricky.** Today you'd **track presentations** yourself and coordinate
  navigation + data model — and **file feedback** with the use case.
  - The framework-interop route (Russell): drop to **UIKit**, make a **`UIWindow`** via the
    scene lifecycle, position it on top, set its **root view controller to a
    `UIHostingController`** and put SwiftUI inside. "Plenty of people do it; it should work."
  - The deeper point: **"last `UIWindow` wins."** If two teammates each make a window, the
    last one wins — this distributed "I'm always on top" ownership is **exactly the bug
    SwiftUI exists to solve** (a *reductio ad absurdum*: there must be a single source of
    truth for layering). So reconsider the **design brief**: a full-screen cover feels
    disruptive; maybe rewind the navigation stack to a base view and restore it on login.
    Dropping to UIKit for these corners is **fine and intended** (frameworks are built for
    real interop) — but **file feedback** so SwiftUI can grow APIs for it.

### Building a pure-SwiftUI custom control

- **Best way to build a pure-SwiftUI custom control — e.g. a dropdown that expands over
  other elements without affecting their layout (forum question).** Answers:
  - First ask **does it need to be custom?** If the control exists in UIKit/AppKit, prefer
    **framework interop** and use that control — you inherit design changes (like this
    year's new-design tweaks) **for free** without updating your app.
  - SwiftUI is **compositional**; compose building blocks. The layout principle for
    "overlay without affecting layout": SwiftUI layout is a **proposal** — the parent
    proposes a size, the child can draw bigger/smaller and **reports its own size**. So a
    child can **draw outside its reported bounds** (an overlay) while reporting a **stable
    size**, so siblings don't move. Framed not as "lying" but as the **intent** of the
    layout system. Higher-level convenience: SwiftUI **overlays**.
  - **The scroll-view gotcha (learned this year):** if you draw outside your reported/layout
    bounds inside a **scroll view**, the system may think your layout bounds are off-screen
    and **remove your view** — so the "protuberance" sticking into the screen vanishes. Same
    gotcha applies to `overlay` (a large overlay on a small host scrolling off). This makes
    custom controls hard — they have real corner cases (should a dropdown collapse when its
    host scrolls, signaling intent to dismiss, or stay so you can see more?).
  - **`swipeActions` is the favorite new API this year** (referenced: this year's "What's
    New in SwiftUI," Stephen and Julia): previously the swipe-action modifier worked only on
    **list rows**; now **any container** can support swipe actions via a **swipe-action
    container** modifier, and the small swipe buttons can be **arbitrary views** ("you can
    put a navigation split view in there — I wouldn't recommend it").
  - Don't over-rotate on "use system controls": custom controls can be the **touches of
    magic** that take an app from good to great — just think critically about whether it adds
    real value vs the **familiarity + accessibility** you get from standard UI. And before
    wrapping a control, **reach for a custom *style*** (point credited to "Jason" from the
    last panel): you keep **accessibility** and **all the standard initializers** (Button has
    *many*) while only changing the look.

### How small should views be

- **Breaking large views into smaller ones helps performance — but how small, and new
  struct views vs `@ViewBuilder` computed properties? (asked of Stephen, the closer-ish
  question).** Answer:
  - Decomposition isn't about smaller pieces; it's about **isolating the data responsible
    for each piece updating.** Example: header / content / footer that each update
    independently — you don't want the whole body re-running when only the footer's data
    changes, so split into three views. If dependencies are **shared across everything**,
    splitting gives **no benefit** (same work).
  - The mechanism: at a child view's **initialization point**, SwiftUI gets a **boundary**
    to compare inputs and say "nothing new here, skip the work." **`@ViewBuilder` computed
    properties give you no such boundary** — it's the same one giant body with all the same
    dependencies, just moved out of line.
  - Reframed: a view re-runs whenever **any** of its inputs change. If one input changes
    constantly and eight rarely, **move the frequently-changing bit into its own small
    view** so the big body doesn't re-run on every tick. **`@Observable` helps "jump over"**:
    the **pointer to the observable doesn't change** (so it doesn't invalidate the super
    view), but the specific property a tiny subview reads does cause **just that subview** to
    redraw — skip the big stuff above, update only the leaf.
  - **Don't fear lots of views.** Views are **structs / value types — they're cheap.**

## Unconventional facts & takeaways (the live-Q&A gold)

- **A real bug, on record:** the **1M-item List traversal** slowdown "definitely shouldn't
  be working that way" and is being looked into — not a misuse, despite matching UIKit's
  diffable-data-source behavior.
- **`NavigationTransition` is a publicly empty protocol in iOS 27 beta 1** — there is **no
  supported way to write a custom navigation transition today**; the official move is file
  feedback. Custom transitions *are* easy off the navigation stack (shaders, `Animatable`,
  keyframes).
- **Naming archaeology:** "navigation transition" beat "presentation transition" /
  "transition transition" because **sheets are navigation**; `sharedBackgroundHidden`
  likewise had "a lot of debate." The team says outright "we think a lot about names."
- **Liquid Glass mental model in one line:** the **glass control layer hugs a flat content
  layer**; glass with nothing scrolling under it has **nothing to refract** and is just
  static. `glassEffect` on a button gives a "button **on** glass," which is *not* what you
  want — use the glass **button style**.
- **`DynamicProperty.update()` runs right before `body`** — a free render-cycle saving that
  turns "most `onChange`" and cache-hit `onAppear` loads into a single pass.
- **Lazy containers identify views by count**, so a top-level `if`/`AnyView`/group/custom-
  layout that resolves to a variable number of subviews breaks the fast path; **a unary
  VStack wrapper restores it.** `@ViewBuilder` computed properties provide **zero** update
  isolation.
- **`task` and `onAppear` now fire top-down**, thanks to new Swift concurrency removing
  `task`'s isolation hop — a subtle ordering correctness win.
- **Apple ships SwiftUI inside UIKit:** poke around `UINavigationController` in the debugger
  and you'll find **SwiftUI building core UI components used across apps that were never
  rebuilt in SwiftUI.**
- **"Last `UIWindow` wins"** is the canonical distributed-ownership bug SwiftUI was built to
  eliminate — used on-air as a reason to reconsider "always on top" overlay designs.
- **Drawing outside your reported bounds in a scroll view can get your view culled** — an
  engineer admitted learning this "this year."
- **Cultural color:** the lab's nickname **"SwiftUI After Dark"**; **"mitosis" / "schlerp"**
  as the in-house names for the Liquid Glass blob effect; the running floor-tile/terrazzo
  and "fish-feeding tracker" gag about sample apps; "9:41 is always somewhere — in Apple
  Park"; the framework engineers naming talks by **presenter** ("Paul's talk," "Matt's
  talk," "Hao-Chien's talk," "Renz's talk") not session number; and the closing affection
  for "laughing about horrible API things… like tonight."
