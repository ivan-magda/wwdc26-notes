---
title: "Profile, fix, and verify: Improve app responsiveness with Instruments — Full Digest"
session: WWDC26 · 268
url: https://developer.apple.com/videos/play/wwdc2026/268/
duration: 27m
speakers: Art (Xcode), Harjas (Instruments)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 268 — Profile, fix, and verify: Improve app responsiveness with Instruments

## TL;DR

Instruments 27 ships a mental model plus four new/improved capabilities for chasing
down app hangs. The whole talk is a guided teardown of a prototype note-taking app
with three distinct hangs, each a different class of problem:

1. **A diagnostic flow.** Always start with the **Time Profiler**, then ask one
   question: *what is the CPU doing during the hang?* High CPU → a code bottleneck
   (optimize the algorithm or move it off the main thread). Idle CPU → the main
   thread is **blocked** on a resource (File I/O, a lock, or IPC), and no amount of
   algorithm tuning helps.
2. **Top Functions** — a new sampling-analysis mode that discards the call hierarchy
   and merges every scattered instance of a function into one block ranked by
   **self** time, surfacing runtime overhead that a flame graph fractures and hides.
3. **Run Comparisons** — baseline vs optimized trace in a single document, with a
   node-by-node delta (green = improvement, red = regression) so you can *verify* a
   fix instead of eyeballing two windows.
4. **Swift Executors instrument** + a redesigned **Inspector** panel — visualize the
   Main Actor / global executor / custom executors, and inspect exact syscall
   arguments (file descriptor, buffer, size) and on-/off-core timing.

The three fixes also double as a Swift-performance primer: drop `any` existentials,
move CPU-heavy tasks off the Main Actor with `@concurrent`, and never do a synchronous
file write on the main thread.

---

## 1. The diagnostic flow (1:12)

The software stack hides a lot under "simple" Swift: the compiler and runtime inject
dynamic dispatch, safety checks, and reference counting; the OS handles memory,
scheduling, and I/O routing; below that sit the platform and hardware. To triage a
dropped frame or hang, Instruments 27 proposes a decision tree:

```
Frame drop / hang
   └─ Time Profiler  (orient yourself)
        └─ What is the CPU doing during the hang?
             ├─ CPU high  → code is executing but too slow  → code bottleneck
             │     ├─ optimize the algorithm, OR
             │     └─ offload the heavy work to a background task
             └─ CPU idle  → main thread blocked on a resource
                   (File I/O · lock · IPC) — Time Profiler is blind here,
                   because it only samples active CPU cycles
```

Two ground rules stated up front and repeated at the end:

- **Profile a release build.** Debug builds trade runtime performance for
  debuggability, so their data is misleading. (Product ▸ Profile builds release.)
- **Mark your workflows with signposts** so the relevant intervals are easy to find
  and so Run Comparisons can line two runs up on the exact same span.

```swift
import os.signpost

let signposter = OSSignposter(subsystem: "Demo App", category: .pointsOfInterest)
var lassoIntervalState: OSSignpostIntervalState? = nil

func lassoSelectionUpdated() {
    lassoIntervalState = signposter.beginInterval("Lasso Selection")
    // Update selection in canvas…
}

func lassoSelectionEnded() {
    // Finalize lasso selection...
    signposter.endInterval("Lasso Selection", lassoIntervalState!)
}
```

Setting `category: .pointsOfInterest` makes Instruments surface the interval
automatically in the Points of Interest track. The demo profiles with the **Swift
Concurrency** template (which still includes the Time Profiler instrument), records
three workflows on an iPad — save, scroll, lasso — and AirDrops the trace for
analysis.

New UI to know: timeline **tracks** (tasks, actors, executors) at the top, a
**detail area** keyed to the selected track, a middle-bar **pop-up** to switch
detail views, and a brand-new right-hand **Inspector** panel that surfaces extra
details and actions based on the current selection.

## 2. Sampling data visualization (7:06)

At ~thousands of samples/second, raw data needs good views. Instruments builds a
**call tree** from a hardware timer that fires every **1 ms** by default, recording
the current call stack on every core. Each sampled frame gets +1 weight; the
function at the bottom of the stack also gets +1 **self** weight. Anything that
starts and finishes between samples (e.g. a quick `swift_retain`) is simply never
recorded — though the more often it runs, the more likely it is to be caught.

- **Flame Graph** — maps the call tree into spatial blocks. Vertical axis = call
  stack (callers on top, callees downward); horizontal axis = aggregated CPU time
  (wider = more samples). Great for spotting fat code paths at a glance.
- **The flame graph's blind spot** — functions called from many places (Swift
  runtime helpers, utilities) get their cost *fractured* across every branch that
  calls them, so no single bar tells you which function burned the most cycles.
- **Top Functions (new)** — discards the call hierarchy entirely, extracts every
  scattered node, and merges them into one block ranked by **self** time (time spent
  executing instructions directly inside that function). The left pane lists top
  functions by self weight; selecting one shows a flame graph of all the code paths
  that called into it.

### Hang 1 — lasso selection (CPU saturation)

The hang lines up with the Lasso Selection signpost. Expanding the process track
shows the **main thread near 100% CPU** during the hang → code is running, just too
long → a Time Profiler problem. The flame graph shows cost smeared across many canvas
rendering paths with no single offender. **Top Functions** surfaces the real culprit:

```
swift_project_boxed_opaque_existential   ← #1 by self weight
```

This runtime function unwraps an **existential** so code can operate over it.

```swift
// The slow path: an `any`-typed existential parameter
protocol Foo { }
struct TypeA: Foo { }
struct TypeB: Foo { }

func bar(_ foo: any Foo) { }
```

Because the concrete type behind `any Foo` can vary in size, the runtime does extra
work to access and operate over the boxed value. The fix is to give the compiler more
type information so it can optimize, using one of three alternatives:

```swift
// Concrete types
func bar(_ a: TypeA) { }
func bar(_ b: TypeB) { }

// Generics
func bar<T: Foo>(_ generic: T) { }

// Enums
enum Foo { case a(TypeA); case b(TypeB) }
func bar(_ value: Foo) { }
```

Art has Xcode's coding assistant rewrite the drawing code to drop existentials.
(Background: "Embrace Swift generics," WWDC22.)

### Verifying with Run Comparisons (new)

Rather than diffing two windows by eye, **Run Comparisons** cross-references all
samples from the baseline and optimized traces, matches each old function to its new
counterpart, computes a delta per node, and sorts by performance difference —
**red = regression, green = improvement.**

Workflow: filter *both* runs to the same `os_signpost` interval (lasso selection),
select the main-thread track, click **Compare** in the middle bar, pick the baseline
from the dropdown. A comparison tab is added to the sidebar (multiple comparisons are
saved into the document, which helps collaboration). Findings:

- Overall lasso-selection execution time **decreased**.
- The flame-graph comparison shows improved paths in green, regressed in red. The
  assistant's switch to generics introduced **new** functions, flagged as regressions
  (regressions sort to the top by default; you can flip the order).
- `swift_project_boxed_opaque_existential` is **gone entirely**, and the improvements
  outweigh the new-function regressions — fix confirmed.

(Deeper: "Optimize CPU performance with Instruments," WWDC25.)

## 3. Execution contention (16:01)

Back to the baseline run for the remaining hangs — which have **no** Points of
Interest signposts to anchor them. Instead, use the new **Swift Executors**
instrument, which visualizes the **Main Actor**, the **global concurrent executor**,
and any **custom executors**. For each remaining hang, the Main Actor track shows a
`renderThumbnail` task; the track summary reveals **several render-thumbnail tasks on
the Main Actor each taking a few hundred ms** — exactly why scrolling the notes list
stutters.

Following the flow: filtering to one hang and pinning the main thread via the
Inspector shows **~100% CPU** — not blocked on a resource, just too much work on the
Main Actor.

Why it happens: the Main Actor owns all UI updates and interaction. The thumbnails
render asynchronously, but because the code was called from SwiftUI it **inherited
the Main Actor context**, so those tasks compete with critical UI work.

```swift
// Before: Task inherits the Main Actor
let drawingData = note.drawingData
let canvasImages = note.decodeCanvas()
thumbnail = await Task(name: "Render Thumbnail") {
    await renderThumbnail(drawingData: drawingData, canvasImages: canvasImages,
                          size: CGSize(width: 300, height: 240))
}.value

// After: @concurrent moves the body onto the global executor
thumbnail = await Task(name: "Render Thumbnail") { @concurrent in
    await renderThumbnail(drawingData: drawingData, canvasImages: canvasImages,
                          size: CGSize(width: 300, height: 240))
}.value
```

Adding `@concurrent` to the task initializer moves the rendering off the Main Actor
onto the global executor; the Swift compiler verifies the change introduces no data
races. In the new trace, the Swift Executors instrument shows the tasks migrate from
the Main Actor track to the global executor track — which not only stops the hangs
but lets thumbnails render **in parallel**. (Deeper: "Embracing Swift Concurrency,"
WWDC25.)

## 4. System blocking (20:29)

The last hang is the stutter on **save**, found via the "Write to File" signpost.
Checking CPU usage this time: **~20%, low**. Low CPU is deceptive — it doesn't mean
the code is slow, it means the **thread stopped running**, blocked on a system
resource. The right template is **System Trace**, built to show *when and why* the OS
pauses your app.

### Thread state model

```
running (on a core)
   │  resource not immediately available
   ▼
blocked  → kernel evicts the thread from the processor
   │  hardware finishes the job
   ▼
runnable → resource ready, but waiting in line for the scheduler to assign a core
   │
   ▼
running again (briefly, to coordinate the next stage)
```

A thread spends the vast majority of a block waiting for an external dependency,
waking only briefly to coordinate the next stage — those brief moments are exactly
the ~20% CPU you see. A single syscall often depends on multiple underlying
resources, forcing the main thread to wait for the OS to resolve every one.

### Diagnosing in System Trace

Profiling the save with System Trace: the main thread's activity lane shows large
**blank space** (blocked, UI can't update). **Purple intervals** mark a running
syscall — but an active syscall doesn't mean app code is executing. Selecting one
interval highlights more than the clicked segment: it's **one continuous `write`
syscall spanning on- and off-core time**. **Opaque** segments = on-core (actively
running); **translucent** segments = off-core (blocked).

The new **Inspector** gives the exact syscall arguments — file descriptor, buffer
memory address, and, crucially, **size**:

- Writing **over 1.7 GB** of data on the **main thread**.
- The single operation took **over 500 ms**, with **~300 ms off-core** waiting for
  the disk.

Because the write was issued **synchronously on the main thread**, the app freezes
until storage responds.

### The fix

```swift
// Before: synchronous atomic write on the main thread
let encoder = PropertyListEncoder()
encoder.outputFormat = .binary
guard let data = try? encoder.encode(snapshots) else { return }
let id = signposter.beginInterval("Writing To File")
try? data.write(to: fileURL, options: .atomic)   // ← blocks the Main Actor
signposter.endInterval("Writing To File", id)

// After: push encoding + file write to the background pool
Task { @concurrent in
    let encoder = PropertyListEncoder()
    encoder.outputFormat = .binary
    guard let data = try? encoder.encode(snapshots) else { return }
    let id = signposter.beginInterval("Writing To File")
    try? data.write(to: fileURL, options: .atomic)
    signposter.endInterval("Writing To File", id)
}
```

Re-profiling and navigating with the "Writing to File" signpost: the **main thread no
longer shows the `write` syscall** — the same syscall now appears on a **background
thread**. Opening the app confirms all three wins: lasso, scrolling, and save are all
responsive.

## 5. Next steps (26:07)

Match the tool to the symptom:

- **CPU overloaded** → **Top Functions** to isolate scattered software overhead;
  **Run Comparison** to verify the improvement.
- **Tasks fighting for resources** → the **Swift Concurrency / Swift Executors**
  instrument to spot Main Actor congestion.
- **Thread idle (blocked)** → **System Trace** + the **Inspector** to find
  synchronous blocking like file I/O.

And always: profile a **release build**, and use **`os_signpost`** so your Run
Comparison intervals are reliable. (Go deeper: "Analyze hangs with Instruments,"
WWDC23.)
