# Session 8120 — SwiftUI Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8120/
- **Duration:** 01:02:35 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + UI Frameworks engineering panel answering forum questions live. Hosts call it "SwiftUI After Dark."
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so attributions below come from self-introductions and host routing only.

> Note: this is the **second** SwiftUI group lab in the WWDC26 catalog (the other is 8006). The panel and a developer's question both reference "the last two SwiftUI group labs this week," so 8120 is a distinct later session with its own roster.

## Panel

From the self-introductions (cues 2–32). Names beyond these intros are unreliable (no diarization), so the roster is built only from the intro round and host routing.

- **Kurt** — host; Worldwide Developer Relations. Before WWDR he worked on presentations and navigation, and shared an office with Russell.
- **Sam** — programming-languages background; works at the intersection of Swift and SwiftUI, including this year's type-checking performance work.
- **Russell** — long-time UIKit engineer; worked on animations and on the UIKit/SwiftUI interop and bridging layer. (Not given an explicit intro turn, but identified by Kurt and by his own later "worked on UIKit for a very long time" remark.)
- **Nick** — did stints in AppKit and UIKit; now a manager on SwiftUI. Works on presentations (Sheet) and containers (Inspector, Navigation, Controls); this year spent time in data flow.
- **Stephen** — formerly an engineer on the Apple Music team doing app-side SwiftUI; now focused on SwiftUI performance and the new agentic coding skills. (Transcript also renders his name "Steven"; treated as the same person.)
- **Anna** — works on navigation, bars, and tabs: anything touching how someone navigates an app. Works alongside Nick.

A triage team is described as working "behind the scenes" on inbound questions but is not named.

## Description

A roughly 62-minute live, on-demand SwiftUI / UI Frameworks group lab. There is no warm-up "favorite features" round: after intros the panel goes straight to upvoted developer-forum questions, about a dozen of them, spanning list/collection performance, navigation transitions, Liquid Glass in toolbars and on buttons, adaptive layout and window sizing, data flow and `@Observable`, the cost of `if` and `AnyView`, presenting full-screen overlays above sheets, building custom controls, view-decomposition granularity, and a closing "what are you most proud of" round. The recurring throughline is the same as the visionOS lab: **file feedback with your concrete use case**, because the team already knows the bugs and wants to understand *why* developers hit them so the API can be shaped to fit.

## Key topics

- **List / collection performance at scale** — a known, acknowledged issue where a List over 1M reorderable items still traverses the whole collection on first display and on every count/order change; fix in progress, feedback wanted for the use case.
- **Navigation transitions** — `NavigationTransition` protocol is publicly empty in iOS 27 beta 1; no public API yet for custom ones. Built-ins: crossfade, zoom, automatic, plus a new composable "any navigation transition" that lets you switch between transitions. Crossfade and zoom also apply to sheets.
- **Liquid Glass discipline** — toolbar/navigation-bar buttons take on glass automatically; don't put glass in the content area (nothing scrolls under it, nothing to refract). For a glass button use `glassButtonStyle` / glass-prominent, not a raw `glassEffect`; pair with a button border shape. `sharedBackgroundHidden(true)` removes glass behind e.g. a profile photo; new API removes just the toolbar content margin so refraction sits right at the photo edge. "Mitosis" (a.k.a. "schlerp") is their term for the liquid blob/merge effect.
- **Agentic coding skills** — Xcode ships skills (new-API insight, best practices, "modernize your UIKit app") usable with the model of your choice and exportable to other agentic systems via a command listed in What's New / Docs. They are also "fun for humans to read."
- **Adaptive layout & window sizing** — don't hard-code window sizes; lean into size classes (regular = multi-column iPad/Mac-like, compact = show less) but handle true continuous flexibility too. Tools: system containers, custom layouts + custom containers, `ViewThatFits` (sparingly), adaptive `TabView` with sidebar, `AnyLayout` (animates between layouts while preserving structural identity).
- **`GeometryReader` vs `onGeometryChange`** — prefer `onGeometryChange`; its second closure lets you collapse per-frame geometry into breakpoints so the action fires only on threshold crossings. `GeometryReader` invalidates its subviews every frame; for precise placement use a custom layout (single layout pass).
- **Data flow & `@Observable`** — SwiftUI is deliberately architecture-agnostic; model your data, keep views light. Prefer `DynamicProperty` over piling up `onChange`; its `update()` runs right before `body`, saving a render cycle (great for cache-hit image loads via the same trick). Don't use `onChange` to mutate view state. SwiftUI Instrument visualizes data flow, not just performance.
- **Avoiding `if` / `AnyView` pitfalls** — toggling visibility: use opacity (with an inert value like `1`) rather than removing from the hierarchy, unless you genuinely need to reclaim layout space. Don't bury conditionals inside view-modifier bodies. In lazy containers (List, LazyStack), a top-level `if`, `AnyView`, group, or custom layout can resolve to a variable number of subviews and break the fast path; wrap in a unary container (VStack) to guarantee one view. Changing `AnyView`'s underlying type tears down and rebuilds the hierarchy.
- **Full-screen overlays above sheets** — hard / not really expressible in pure SwiftUI today; coordinate navigation + data model, or drop to a UIKit `UIWindow` (root = `UIHostingController`). Caution: "last window wins" is the exact distributed-ownership bug SwiftUI exists to solve, so reconsider the design brief.
- **Custom controls** — prefer interop with an existing UIKit/AppKit control (you inherit design updates for free) or a custom *style* (you keep accessibility + all the standard initializers). Build a fully custom control only when it adds real value; expect corner cases (a view drawing outside its reported bounds can be culled by a scroll view). `swipeActions` now works in any container via a swipe-action container modifier, and the swipe buttons can be arbitrary views.
- **View decomposition granularity** — split views to isolate *dependencies*, not just for smaller files; `@ViewBuilder` computed properties give no isolation benefit (same one big body). Views are cheap value-type structs; don't fear having many.
- **Timing detail** — new Swift concurrency features make `task` and `onAppear` fire in top-down order (previously `onAppear` ran first because `task` incurred an isolation hop).

## Related sessions (referenced on-air — by title/presenter, no session numbers stated)

The panel cited these talks by name only; none came with a session number on air, so none is asserted here.

- [ ] "What's New in SwiftUI" (this year) — Stephen and Julia; covers the new `swipeActions` container.
- [ ] Custom layouts in SwiftUI ("Paul's talk," WWDC 2022).
- [ ] "Demystify SwiftUI containers" (WWDC 2024, "Matt's talk," karaoke-themed) — counting how many views a view expands to, plus the container-values API.
- [ ] Advanced graphics / "Advanced graphics in SwiftUI" (this year, "Hao-Chien's talk") — alignment guides as a one-pass alternative to custom layout.
- [ ] Lazy Stacks talk (this year, "Renz's talk") — moving image loading earlier, the `if`-in-lazy-container counting problem, off-screen culling of out-of-bounds views.

## Chapter arc (rough, from timestamps)

No published chapters (group lab). Approximate flow:

- 00:00 — Welcome ("SwiftUI After Dark"), housekeeping (forums + Feedback Assistant)
- 01:01 — Panel introductions
- 02:53 — Q&A begins: List/collection at 1M items
- 04:36 — Navigation transitions / empty `NavigationTransition` protocol
- 07:21 — Agentic coding skills + Liquid Glass on buttons / in toolbars
- 13:53 — Adaptive layout, size classes, window resizing, `AnyLayout`
- 18:53 — `GeometryReader` vs `onGeometryChange`
- 22:08 — Learning data management; Instruments; `DynamicProperty`; task/onAppear ordering
- 30:31 — Conditionally hiding elements / `if` and modifier-body conditionals / lazy-container counting
- 38:13 — Cost of `AnyView` type erasure
- 39:30 — Adaptive layouts with resizable Xcode previews
- 42:10 — Full-screen overlays above sheets (drop to UIKit `UIWindow`)
- 47:23 — Building a pure-SwiftUI custom control; overlays; `swipeActions` container
- 54:50 — How small should views be? structs vs `@ViewBuilder` properties
- 58:13 — Closing: what the team is most proud of
- 01:01:55 — Wrap-up

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API/feature names are captured in `digest.md`.
