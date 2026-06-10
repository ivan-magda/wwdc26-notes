# Session 268 — Profile, fix, and verify: Improve app responsiveness with Instruments

- **URL:** https://developer.apple.com/videos/play/wwdc2026/268/
- **Duration:** 27m
- **Speakers:** Art (Xcode team), Harjas (Instruments team)

## Description

Instruments 27 introduces a mental model and a set of new tools for triaging app
responsiveness problems. Working through a prototype note-taking app with three
real hangs — a laggy lasso tool, choppy scrolling, and a stutter on save — Art and
Harjas demonstrate a four-step diagnostic flow (CPU saturation → sampling
visualization → execution contention → system blocking) and the new Instruments 27
features that support it: the Top Functions analysis mode, Run Comparisons, the
Swift Executors instrument, and the redesigned Inspector panel.

## Key topics

- Diagnostic flow: start with Time Profiler, then ask "what is the CPU doing during the hang?" — high CPU points to a code bottleneck; idle CPU points to the main thread blocked on a resource (File I/O, locks, IPC)
- Always profile a **release build**; debug builds give misleading data
- `OSSignposter` intervals (subsystem + `.pointsOfInterest` category) to mark workflows in the Points of Interest track
- New Instruments 27 UI: timeline tracks for tasks/actors/executors, detail area, and a brand-new **Inspector** panel
- Sampling visualization: how a call tree is built from 1 ms samples; **Flame Graph**; the new **Top Functions** mode (self-weight, hierarchy discarded)
- Hang 1 — CPU saturation: `swift_project_boxed_opaque_existential` as top function → replace `any` existentials with concrete types / generics / enums
- **Run Comparisons** — baseline vs optimized trace in one document; red = regression, green = improvement; node-by-node delta
- Hang 2 — execution contention: new **Swift Executors** instrument shows `renderThumbnail` tasks saturating the Main Actor → fix with the `@concurrent` attribute on the `Task` to move work to the global executor
- Hang 3 — system blocking: **System Trace** + Inspector reveal a synchronous 1.7 GB `data.write` on the main thread (>500 ms, ~300 ms off-core) → wrap in a `Task { @concurrent }` to push file I/O to the background
- Thread state model: running → blocked (evicted by kernel) → runnable → back on core; off-core/on-core segments in System Trace
- Closing rule: match the profiling tool to the symptom; verify with Run Comparisons; rely on `os_signpost` for reliable intervals

## Related sessions to fetch (referenced in this talk)

- [ ] Embrace Swift generics (WWDC22)
- [ ] Optimize CPU performance with Instruments (WWDC25)
- [ ] Embracing Swift Concurrency (WWDC25)
- [ ] Analyze hangs with Instruments (WWDC23)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of how Instruments 27 helps developers understand and optimize app responsiveness across the software-stack abstraction layers.
- **1:12 Diagnostic flow** — The four-step mental model: CPU saturation, sampling data visualization, execution contention, and system blocking — for systematically triaging hangs and frame drops. Start with Time Profiler; profile a release build; mark workflows with `OSSignposter`.
- **7:06 Sampling data visualization** — How Instruments turns raw CPU samples into a Call Tree, Flame Graph, and the new Top Functions mode for spotting bottlenecks. Hang 1 traced to `swift_project_boxed_opaque_existential`; fixed by removing existentials. Run Comparisons verifies the win.
- **16:01 Execution contention** — The Swift Executors instrument reveals `renderThumbnail` tasks saturating the Main Actor; adding `@concurrent` moves the work to the global executor and resolves the scrolling hang.
- **20:29 System blocking** — System Trace and the new Inspector panel diagnose a low-CPU hang caused by a synchronous 1.7 GB file write on the main thread; fixed by moving the work into a background Swift task.
- **26:07 Next steps** — Match the right Instruments template to each class of problem; always profile a release build; use `os_signpost`; links to related CPU, concurrency, and hang-analysis sessions.

## Code

See `code.md` — 9 snippets from the Code tab (OSSignposter setup, existentials vs concrete/generics/enums, thumbnail rendering on/off the Main Actor, synchronous vs backgrounded file write).
