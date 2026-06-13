# Session 8003 — Power and Performance Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8003/
- **Duration:** 01:00:58 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + engineering panel answering upvoted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization.

## Panel

- **Cole** — host; Core Technologies Evangelist at Apple.
- **Terry** — performance engineer; helps teams across Apple optimize features announced at WWDC (his team worked on this year's launch-time improvements).
- **Yanni** — works on MetricKit, the framework for on-device metric and diagnostic data to improve power and performance.
- **Kacper** — Instruments team (part of the Xcode suite); recording, visualizing, and diagnosing app performance and power. (Transcript also mis-hears the name as "Caspar" / "Kasper".)
- **Kunal** — power engineer; core OS power, battery life, thermals; supports visionOS and iOS third-party apps.
- **Marco** — performance engineer; render pipeline, animations, scrolling.

A separate triage team behind the scenes sorted the incoming questions.

## Description

A live, on-demand power and performance group lab. After a round of "what are you most excited about in the '27 releases," the panel answers roughly twenty upvoted developer questions spanning SwiftUI efficiency, battery/power debugging, MetricKit's ground-up rewrite, Instruments workflows, app launch time, background-task scheduling under Apple Intelligence load, thermal management, and learning paths for deep Swift performance work. The recurring throughline: **measure before you optimize**, combining local profiling with field telemetry, and lean on Apple's own tools instead of rolling your own measurement.

## Key topics

- **SwiftUI efficiency** — separate views from inputs; the `@Observable` macro gives per-field update granularity "for free"; minimize redraws and over-invalidation; flatten deep view hierarchies; environment *churn* (not environment use) is the real cost.
- **MetricKit rewrite** — new Swift-first API; much of the old API marked deprecated; finer-than-daily granularity; new state reporting (slice metrics by app state) and new diagnostic/metric types exclusive to the new API.
- **Instruments** — new run-comparison/diffing of call trees; Time Profiler + flame graph as the beginner starting point; a top-functions view for flat lists of offenders; SwiftUI instrument cause-and-effect graph; Power Profiler (tethered and untethered "performance trace" modes); system trace now shows thread priorities and what preempted a thread.
- **Power debugging** — biggest common mistake is insufficient instrumentation/telemetry; background vs foreground energy; everything (file system, network, precise location) costs power; condition inducers and Low Power Mode to simulate stress.
- **App launch** — draw the first frame with the minimal data set; defer non-critical work; don't block the main thread waiting on background work; MetricKit/Organizer measure tap-to-first-frame (don't roll your own via kernel process-creation APIs).
- **Background tasks under Apple Intelligence** — many AI features run on the Neural Engine or in Private Cloud Compute, so app CPU work can run concurrently; chunk background tasks so they pause/resume and keep making progress.
- **Thermals** — listen to `ProcessInfo.thermalState`; back off frame rate, resolution, animation richness; fetch lighter network assets; the system also throttles automatically; "no getting around thermodynamics."
- **Xcode Organizer** — field/aggregate data; metric goals benchmarked against comparable apps; launch call stacks from real users; energy logs.
- **Learning path** — hands-on immersion in Instruments templates; the Apple Silicon CPU Optimization Guide; last year's CPU/SwiftUI optimization talks.

## Related sessions and resources (referenced on-air)

The panel referenced talks and resources mostly by description, not by number. No `wwdc2026` session numbers were spoken aloud, so none are cross-linked.

- Last year's "Optimize SwiftUI performance with Instruments" talk (named by description).
- Last year's "Optimize CPU performance with Instruments" talk (Time Profiler, CPU Profiler, processor trace).
- An accessibility session this year covering `AnyLayout` to switch horizontal/vertical layouts with larger text (named by description).
- A dedicated session for the new long-running Metal app profiling tool, which shipped in 26.4 (number not stated).
- The **SwiftUI group lab** (pointed to repeatedly for SwiftUI-architecture questions; "tomorrow at 9 a.m.").
- The **Power and Performance Q&A on the Developer Forums** (Thursday 10 a.m. Pacific).
- A MetricKit-tag **Developer Forums post** summarizing the new Perf & Power tooling.
- The **Apple Silicon CPU Optimization Guide**.
- The **Instruments Tutorials** (with an associated project that has performance issues built in).

## Chapter summary

No published chapters (group lab). Rough arc:

- **00:00** — Intros (Cole + Terry, Yanni, Kacper, Kunal, Marco).
- **02:11** — Warm-up: favorite '27 performance features (run comparison, metric goals, state reporting, MetricKit Swift API, launch-time wins).
- **04:36** — Why MetricKit is being rebuilt (Swift-first, granularity).
- **06:04** — Developer Q&A begins (SwiftUI power basics for beginners).
- **~14:00** — Biggest power mistakes; Instruments documentation and beginner workflow.
- **~24:50** — Background tasks under Apple Intelligence; thread hopping; app-launch measurement.
- **~35:45** — Silent battery killers; large lists; `AnyView`; new-tools roundup.
- **~46:50** — Deep-expertise learning path; escaping closures; older-device testing; condition inducers.
- **~55:10** — Thermal management in direct sunlight.
- **~58:55** — Environment-property performance; wrap-up and where to follow up.

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API and tool names are captured in `digest.md`.
