# Session 8001 — Swift Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8001/
- **Duration:** 01:01:10 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + Swift-team panel answering upvoted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization.

## Panel

Self-introductions from the open (00:45–01:47):

- **Angelica** — host; Developer Relations. Frames the questions and names the askers.
- **Holly** — Swift team. Focus: generics, type inference, compiler diagnostics, concurrency. Sits on the open-source language steering group and the Swift core team. (Seen at Platform State of the Union, "in the exact same shirt.")
- **Corey** — Swift server networking team at Apple. Focus: "shuffling packets around reliably and quickly to keep everybody's services up and running."
- **Tony** — Foundation, the Swift standard library, and Swift packages (algorithms, collections, atomics). On the Foundation work group.
- **Doug** — Swift language team. Has worked on Swift "since it started"; sits on the language steering group.

## Description

A live, on-demand Swift group lab. After a warm-up round naming favorite Swift 6.3 / 6.4 features, the panel answers ~15 upvoted developer questions across concurrency, performance and profiling, the ownership model, build performance, Swift Package Manager, and language evolution. Recurring throughline: profile before you optimize, lean on the compiler rather than escape hatches, and participate in the open-source project on the forums and GitHub. The panel repeatedly asks for GitHub issues with sample projects.

## Key topics

- **Region-based isolation & `sending`** — transferring non-sendable data between actors; the active `disconnected` pitch for *storing* such values.
- **Structured concurrency best practices** — avoid unstructured `Task` / `Task.detached`; use task groups, `with`-style scoping, async `defer`, and cancellation shields for async cleanup in canceled contexts.
- **Non-sendable as a design tool** — sometimes you *want* a type non-sendable; new `~Sendable` syntax in Swift 6.4 (vs. an unavailable `Sendable` conformance).
- **Conformance cost** — `Equatable`/`Hashable` carry runtime code kept for `as?` casts; `Sendable` is a compile-time tag with no runtime cost.
- **`@MainActor` "contagion"** — `@MainActor` default mode, leaf-first migration, `nonisolated` methods, making accidental `var` state immutable.
- **The async-function default flip** — the Swift 6.2 change keeping non-isolated async functions on their caller's context (vs. always hopping to the global concurrent pool); the panel's "what we'd do differently."
- **Performance** — profile first (Instruments flame graph + top-functions), `Span`, `UniqueArray`, `borrow`/`mutate`, `@inlinable` + `@inline(never)`.
- **Build performance** — explicit module builds (on by default), the build timeline, unified Swift Build under SwiftPM in 6.4.
- **Lesser-known features** — `@inlinable`+`@inline(never)`, plain `as` annotations for overload resolution, integer-overflow APIs, key paths, Swiftly toolchain installer, Swift as an open-source project.
- **Language evolution** — conditional tuple conformances via parameter packs; the new `Iterable` family of protocols for non-copyable/non-escapable containers.

## Related sessions (referenced on-air by title; session numbers not stated in the transcript)

- [ ] "Migrate your app to Swift 6" — 2024 code-along with a sample project (cited for the `@MainActor` migration question).
- [ ] Swift performance using Instruments — cited twice: a "last year" image-processing talk and a "new this year" session, both attributed to a speaker named **Nate** (flame graph + top-functions; non-copyable type patterns in Instruments).
- [ ] Explicit (build) modules session — "about two years ago."
- [ ] "Migrating to Swift Testing" — this year's session.
- [ ] "What's New in Swift" — this year (source for `Iterable`, `borrow`/`mutate` mentions).
- [ ] "Platform State of the Union" — Holly appeared in it.

## Chapter summary

No published chapters (group lab). Rough arc: intros (00:00) → favorite Swift 6.3/6.4 features (01:47) → developer Q&A (04:21) → "what feature should people know about" round (39:18) → favorite quality-of-life feature round (53:01) → wrap-up (59:59).

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API/feature names are captured in `digest.md`.
