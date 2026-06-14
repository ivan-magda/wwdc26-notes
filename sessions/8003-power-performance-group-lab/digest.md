---
title: "Power and Performance Group Lab — Full Digest"
session: WWDC26 · 8003
url: https://developer.apple.com/videos/play/wwdc2026/8003/
duration: 01:00:58
sources: transcript.md (whisper.cpp ggml-large-v3), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and context, and proper nouns may be mis-heard.
---

# WWDC26 · 8003 — Power and Performance Group Lab

> A 61-minute live developer Q&A with a six-person panel from Apple's performance,
> power, MetricKit, and Instruments teams. Not a feature talk: it's the unscripted
> layer under the WWDC26 perf sessions. The constant refrain is **measure first**,
> combine local profiling (Instruments) with field telemetry (MetricKit, Xcode
> Organizer), and trust Apple's own measurement plumbing over hand-rolled timers.

## TL;DR

- **MetricKit is being rebuilt from the ground up.** A new **Swift-first API** (works
  with Swift concurrency) lands this year; **much of the old MetricKit API is marked
  deprecated**. The headline addition is **finer granularity** (not just one daily
  report but smaller intervals) plus **state reporting** and new diagnostic/metric
  types that are **exclusive to the new API**.
- **State reporting is the year's connective tissue.** It lets you tag metrics with
  the app's current state so a regression comes with context ("here's what the app was
  doing when it went wrong"), cutting out the first triage step. Best practice: don't
  log high-cardinality fast-changing numbers (e.g. item counts); **bucket them**
  (small / medium / large batch).
- **Instruments adds run comparison (diffing).** Record and name multiple runs, then
  **diff call trees** to see at a glance what regressed or improved, instead of eyeballing
  two windows side by side.
- **Launch time got faster "for free" this year**, and the panel stressed you should
  **measure launch with MetricKit/Organizer, not your own kernel-API timers** — Apple's
  measurement spans from the home-screen tap (before your process exists) to first frame.
- **Xcode Organizer now has metric goals benchmarked against comparable apps.** Answers
  the classic "my video app uses a lot of power, is that normal?" by showing how similar
  apps perform, so you can set a baseline.
- **Background tasks under Apple Intelligence are usually fine.** Many AI features run on
  the **Neural Engine** or in **Private Cloud Compute**, so your CPU work can run
  concurrently. Chunk background work so the system can pause/resume it and you keep
  making progress.
- **Power Profiler has an untethered mode** (the "performance trace" in developer
  settings): record multi-hour traces on-device, AirDrop to your Mac, open in
  Instruments. Great for catching accidental background work and real-world conditions.
- **Thermal answer for sun-baked AR/Metal apps:** listen to **`ProcessInfo.thermalState`**,
  then back off frame rate, resolution, animation richness, and fetch lighter network
  assets. The system also throttles on its own. "There's no getting around thermodynamics."

## Panel

Host **Cole** (Core Technologies Evangelist) with **Terry** (performance, worked on this
year's launch-time wins), **Yanni** (MetricKit), **Kacper** (Instruments; the transcript
also spells it "Caspar"/"Kasper"), **Kunal** (power: core OS power, battery, thermals;
visionOS + iOS third-party), and **Marco** (performance: render pipeline, animations,
scrolling). A behind-the-scenes team triaged questions.

## Warm-up: favorite '27 performance features (02:11–06:03)

- **Run comparison / diffing in Instruments** (Kacper) — name multiple runs, diff call
  trees, see what regressed or improved without side-by-side windows.
- **Metric goals + state reporting + Organizer support** (Kunal) — slice metrics by app
  state; new **Xcode Organizer metric goals** for battery-life and historical
  foreground-energy targets, so you can tell whether your app is doing well or poorly.
- **State reporting + MetricKit context** (Marco) — the hardest part of perf work is
  contextualizing what you're looking at; knowing what the app was doing when a problem
  hit "cuts out the whole first part of triage."
- **Brand-new MetricKit Swift-first API** (Yanni) — "a very big year for us"; the
  contextualizing piece helps developers understand what's happening in their app.
- **Across-the-board optimizations, especially launch time** (Terry) — his team worked
  directly on launch this year and "all apps get it for free."

On the MetricKit rewrite (04:36): the Swift-first API works seamlessly with Swift
concurrency for better ergonomics, but the bigger motivation is **new granularity** —
smaller-interval breakdowns instead of only a daily report — and **new diagnostic/metric
types available only on the new API**, given as the incentive to migrate.

## Developer Q&A

### SwiftUI efficiency and power (beginner-friendly)

- **"As a beginner, what affects app power/performance in SwiftUI, and how do I avoid
  drain and lag?"** Separate views from their inputs so you don't redraw things that
  didn't change; the **`@Observable` macro** gives updates only for the fields you
  actually read, so you get separation "for free." Profile with the **SwiftUI
  instrument** and read its **cause-and-effect graph** for view updates to find and break
  dependencies. The panel plugged last year's "optimize SwiftUI with Instruments" talk.
  Kunal's framing: SwiftUI work is **compute-bound**, so reducing compute helps latency
  *and* power; deep SwiftUI call stacks show up in energy logs *and* in hangs. Terry's
  caution: a beginner shouldn't assume SwiftUI is the culprit — profile the **overall
  picture** first; it might be an algorithm churning CPU somewhere else. Use **Power
  Profiler alongside the SwiftUI instrument** to confirm a CPU issue is landing in SwiftUI
  code (you may even see GPU energy from rendering).

- **"Theme object (colors/tokens) injected via environment object, read in every atomic
  component, dozens of nesting levels, hundreds of components — at what scale is this a
  bottleneck, and is there an alternative?"** Hard to give a number; behavior depends on
  the views. Environment is a useful abstraction for passing things down the tree. Use the
  **SwiftUI instrument** to see the downstream cost on a case-by-case basis. (Marco)

- **"Does using a lot of `@Environment` properties in a large app hurt rendering/update
  performance?"** The real cost is **environment churn**, not environment use. Putting
  values in and reading them out is cheap; **updating them at high frequency** forces every
  reading view to re-evaluate whether its environment changed. Visible in the SwiftUI
  instrument. (Marco)

- **"Silent battery killer in SwiftUI besides background tasks? How much does view
  over-invalidation drain, and what Instruments workflow catches it?"** Over-invalidation
  is a big and *silent* killer: by definition you're recreating a view that looks
  identical, so nothing changes on screen while the CPU churns. Fix by minimizing redraws
  and **flattening the UI hierarchy**; watch background work the SwiftUI threads dispatch
  to fetch data, and cache. The **SwiftUI instrument** is the tool. (Marco, Kunal)

- **"Large lists/tables in SwiftUI for macOS 27 — best practices for tens/hundreds/
  thousands of items?"** Keep list/cell sizes **constant** so SwiftUI can cache and manage
  layout; constantly changing cell heights cascades (everything below a resized cell moves,
  forcing recalculation of what's on screen). Use **lazy stacks** (`LazyHStack`/`LazyVStack`)
  so items load as they scroll on; move filtering into the **model** (compute before handing
  the list to SwiftUI) instead of `if` statements inside a `ForEach`. (Marco)

- **"How would you load 50,000–500,000 records in SwiftUI tables, and analyze regressions
  with MetricKit?"** First question: do you actually need to load that much for the user
  experience? Load only what you need (this also feeds launch-time strategy: minimal data
  to draw the first frame). For analysis, use **state reporting** to bucket the batch size
  (small/medium/large) and see in which range performance changed — don't log per-item
  counts (1,000 vs 1,001 rarely matters and costs you to record). Yanni noted this exact
  small-vs-large-batch experiment appears in his session video. (Yanni, with Marco on lazy
  stacks)

- **"`AnyView` for type erasure — how expensive, is there a threshold, or is avoiding it
  premature optimization?"** Type erasure adds some overhead at view creation; avoid it
  when easy, but don't contort your architecture around it. Treat it as a "might become a
  problem later" item and **fix it if measurement shows it matters**. The panel's broader
  point: Apple tries hard "not to build API that are a rake to step on"; **measure**, the
  features exist for a reason and sometimes the cost is worth it. (Marco; example given:
  `AnyLayout` for accessibility larger-text layouts is fine to use.)

- **"Performance event taught me to avoid escaping closure parameters (closures are hard to
  compare → more invalidations), but passing view-builder content is limiting."** Call the
  closure in your **`init`** rather than in the view `body` so it doesn't run on every
  re-evaluation; if closures cause issues, look for an alternative API/structure. Panel
  deferred deeper SwiftUI architecture to the **SwiftUI group lab** ("tomorrow at 9 a.m.").

### Power debugging and common mistakes

- **"Biggest power mistake developers don't realize they're making?"** No single one. The
  most common pitfall is **insufficient telemetry/instrumentation**, so you over-index on
  one customer report while field analytics shows a bigger problem elsewhere. Always
  **measure before you optimize**. Second theme: **not accounting for app state** — e.g.
  energy logs showing a database write taking too long/too much power, often because the
  app was never tested on **large data sets** you can't reproduce at your desk. Marco's
  reminder: **everything uses power** — file system, network, precise location — so doing
  network requests half as often is a free power win. Condition inducers and untethered
  traces help reproduce real conditions (thermal state, cellular switching while
  traveling). (Kunal, Kunal/Marco)

- **"App shows UIKit/SwiftUI screens without much background work but still uses high
  battery per Xcode (energy gauges)."** SwiftUI/UIKit compute should show up as
  **foreground** energy; high **background** energy points to **background tasks or
  location work scheduled when the app isn't foregrounded**. Use **Power Profiler untethered
  mode** (record a trace on-device, not connected, move around, then open in Instruments) to
  catch accidental background scheduling. The trace also breaks energy down **by subsystem**
  (CPU / GPU / display) — could be high brightness, lots of color swaps, or **networking in
  the background**. (Kunal, Marco)

- **"What's a condition inducer? Does it make the device hot?"** No — it **artificially
  induces** the state without heating the device. It's an Xcode feature (start a debug
  session; possibly also in the new **Device Hub**) that simulates what would happen if the
  device were warm by reducing CPU and other resources, and can also limit **network speed**
  and other variables. (Kacper)

### Instruments, documentation, and workflow

- **"Best documentation for Instruments? Any written guides?"** Start with the **Instruments
  Tutorials**, written a few years ago by an Instruments engineer to feel like having an
  engineer beside you; they ship an **associated project with performance issues built in**
  so you can learn to spot hangs visually and walk the full profile-fix-verify loop. Reach
  for tool-specific guides as you go, and **file Feedback Assistant** for missing docs.
  (Kacper)

- **"For a first-timer, start with Time Profiler or something else?"** **Time Profiler with
  the flame graph view.** Flame graphs make it visually obvious where time goes versus a
  textual outline of numbers. There's also a new **top-functions view** giving a **flat list
  of top helper and compiler-runtime functions**, easier than deeply nested outlines. (Marco,
  Kacper)

- **"Headlining dev tools that didn't make the updates page?"** **Xcode Organizer** (field
  data aggregated across users, less flexible aggregation than MetricKit, but **metric goals
  vs comparable apps** to form a baseline — answers "is my video app's power normal?"). The
  **Foundation Models** Instruments tool is now a **full debugging tool** (inspect requests,
  prompts, token caching), up from last year's basic token-count metrics. A tool **shipped
  in 26.4 for profiling long-running Metal apps** records FPS over **hours**, surfaces
  outliers, and pairs with state reporting to spot frame drops in a specific level. Also a
  **MetricKit-tag Developer Forums post** summarizing all the new Perf & Power tooling.
  (Kunal, Yanni, Kacper, Cole)

- **"One homework assignment from this year's new stuff?"** **Metric goals** (compare your
  metrics against similar apps across power, foreground energy, launches, hitches, hangs)
  and **state reporting** (slice data to find which app states perform poorly). Use them
  together to zoom in on what needs attention. (Kunal)

### App launch and background work

- **"How does iOS 27 prioritize background tasks under heavy Apple Intelligence
  workloads?"** Often no conflict: many AI features run on the **Neural Engine** or in
  **Private Cloud Compute**, so your CPU work can run **concurrently** with them. Depends on
  the resources you're actually using. **Chunk** background tasks so the system can pause and
  resume them and you keep making progress instead of restarting a big task from scratch.
  New in **system trace**: see **thread priorities** and **what preempted your thread** (catch
  mis-assigned QoS on Swift concurrency tasks). "Even at Apple we have a ton of background
  workloads we don't want to stall." (Terry, Kacper)

- **"To avoid blocking the main thread I run expensive tasks on background threads, but
  during launch this causes many thread hops. How costly is frequent hopping vs blocking,
  and is there a better approach?"** Each hop adds switch overhead, but if you're not doing
  it "thousands of times per second" it's negligible. Better: **defer non-critical work**
  past launch, **prioritize** background threads below the work loading main content, and
  **pre-warm** via BG app refresh to fetch launch data ahead of time. Kunal's bigger
  observed problem: it's not spinning off work, it's **spinning off work and then making the
  main thread wait** for all of it before the first frame, freezing the app. Detect via
  **system trace** (main thread shows as blocked) and the Instruments **context-switch count
  histogram** correlated with launch metrics. Tip: don't fetch A/B-test experiments during
  launch — cache the last result and branch on it. (Terry, Kunal, Kacper)

- **"Best way to measure app launch time?"** Don't roll your own using **kernel APIs** for
  process-creation time (that adds overhead). Use **MetricKit or Xcode Organizer**: the
  system measures **from the home-screen tap to the first frame**, including the interval
  before your process even exists, efficiently and at no extra cost to your app. Organizer's
  **launch area** also shows the **top call stacks** where real users hit slow launches, so
  you can see if you're blocking on other work or just doing too much CPU at launch — no
  local profiling required. "That's the same thing we use for our own apps, like Mail."
  (Yanni, Terry, Kacper)

### Older devices, learning, and deep expertise

- **"Beginner (mom of nine, former nurse) — best practices so view updates don't cause
  hitches/CPU spikes on older devices, with dynamic data or heavy assets?"** Right-size
  assets (no 2000×2000 image for a thumbnail); **cache** once-shown assets so you don't
  re-fetch/re-parse network data; for non-load-bearing assets show a **placeholder** and
  load off the main thread for better **perceived performance**. For testing older devices
  you don't own: **turn on Low Power Mode** (CPUs run slower, surfaces issues similar to old
  hardware, and lots of users run it anyway), use **condition inducers**, and rely on
  **field data** (MetricKit/Organizer) across your real users' device mix. Also: **profile on
  a physical device, never the simulator** — the simulator runs on your Mac so it tells you
  nothing about on-device performance. (Marco, Kunal, Terry, Kacper)

- **"I want to go deep on Swift performance — beyond sessions and Swift Evolution, what's
  the pathway? Codebases, compiler internals, books, benchmarks?"** Mostly **experience**:
  build things and look at them through Instruments templates (Time Profiler, SwiftUI
  instrument) to form a mental model of what the frameworks do behind the scenes. Resources:
  last year's **"optimize CPU performance with Instruments"** (Time Profiler, CPU Profiler,
  processor trace) and the **Apple Silicon CPU Optimization Guide** — understand how the
  CPUs and hardware work and you can write Swift algorithms that perform well on Apple
  silicon. (Marco, Kacper, Terry)

### Thermals

- **"Our app heavily uses ARKit/Metal, runs outdoors in direct sunlight, and hits elevated
  thermal states often — strategies to manage thermal pressure?"** Listen to
  **`ProcessInfo.thermalState`** and **back off** when it rises: request **lighter network
  resources** to cut decoding/parsing on-device, simplify **animations/transitions**, and
  **reduce frame rate and resolution** (fewer pixels = less work). The **system already**
  throttles animation and display frame rates in higher thermal states, so you get some
  benefit by default and add to it. Ultimately, optimize the part you control (your app's
  compute/draw) because devices "can't run at 100 degrees Celsius" — "there's no getting
  around thermodynamics." (Kunal, Marco, Cole)

## Unconventional facts & takeaways (the live-Q&A gold)

- **MetricKit's old API is being deprecated**, not just extended. The Swift-first rewrite
  is the migration path, and **some new diagnostic/metric types are new-API-only** — a
  concrete incentive, stated on-air, that isn't obvious from the updates page.
- **State reporting wants buckets, not raw numbers.** The panel explicitly warned against
  logging fast-changing high-cardinality values (item counts); categorize into small/medium/
  large. Logging 1,000-vs-1,001 "incurs a lot of cost just to record that."
- **Simulator profiling is worthless for performance** — it runs on your Mac, so the
  simulator model makes no difference. Always profile on a physical device.
- **The launch-time bug is the main thread *waiting* on background work it spun off.** Apps
  spin off background work then block the first frame on it, freezing the whole app.
- **Apple Intelligence rarely starves your background tasks** because the AI runs on the
  Neural Engine or in Private Cloud Compute, leaving CPU free for concurrent app work — a
  reassurance straight from the power team.
- **Condition inducers don't heat the device** — they artificially simulate a warm/throttled
  state (and can throttle network), possibly surfaced in the new Device Hub.
- **Untethered Power Profiler** ("performance trace" in developer settings) records
  multi-hour traces you AirDrop to your Mac — the lesser-known flow for catching accidental
  background scheduling and real-world conditions.
- **Apple measures its own launch the same way it tells you to** ("the same thing we use for
  Mail"): MetricKit/Organizer tap-to-first-frame, not hand-rolled kernel timers.
- **Organizer metric goals are peer-relative.** It benchmarks you against comparable apps,
  finally answering "is my power normal for a video app?"
- **Foundation Models profiling grew up** from last year's token counts into a full request/
  prompt/token-cache debugging tool in Instruments.
- **Cultural color:** the panel's near-universal answer to almost every question is
  "measure it first / it's case-by-case"; a recurring nudge to file Feedback Assistant for
  missing docs and bugs; and the candid "there's no getting around thermodynamics."
