# Session 8013 — Xcode Tips and Tricks Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8013/
- **Duration:** 01:01:35 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + Xcode engineering panel answering upvoted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so attributions below are inferred from self-introductions and host routing.

## Panel

Roles are taken from the round of self-introductions at the top of the lab (00:00:21–00:01:18). Whisper has no diarization and renders some first names inconsistently across the hour; the host's closing thank-you (01:00:36) lists the panel as "Chris, Jake, John, Casper, other Chris."

- **Angelica** — host; introduces panelists, reads developer questions, routes them to the panel.
- **Chris Miles** — works on live coding experiences in Xcode: previews, playgrounds, and integrating those technologies into agentic workflows.
- **Jake** — designer on the Xcode team; most recently worked on themes and toolbar customization.
- **John** — manages several front-end Xcode teams: window chrome, source editor, search, code completion, and design tools.
- **Casper** — Instruments team; builds profiling tools so developers can optimize their apps. (Rendered "Katsper" / "casper" in places by whisper.)
- **Chris** (the second Chris on the panel) — works on the Organizer window for crash reporting and uploading to the App Store, and on some of the signing UI in Xcode.

> Name caution: mid-sentence whisper artifacts like "FRANCESC CAMPOY" (00:08:16) and "Phanteks/Pichaya/Pichaya" appear in the raw transcript. These are not panelist names; they are either garbled developer usernames the host is reading aloud or transcription noise, and are treated as such below.

## Description

A roughly 62-minute live, on-demand Xcode group lab. After a warm-up round where each panelist names a favorite lesser-known Xcode feature, the panel answers upvoted developer questions covering editor navigation and keyboard shortcuts, previews and playgrounds, the new themes and customizable toolbar in Xcode 27, project-file merge conflicts (groups vs. the newer blue folders), running Xcode 26 and 27 side by side, build performance, Instruments and profiling, source control, code signing, DocC, agentic/LLM workflows, and the recurring "delete derived data" ritual. A constant throughline: **file feedback in Feedback Assistant with a concrete use case**, and **use Git / source control**. A second refrain: the panelists build Xcode with Xcode, so most tips come from their own daily use.

## Key topics

- **Inline playgrounds** — the `#playground` macro (added "last year") drops a runnable playground into any Swift file; `File > New > Playground` gives a single Swift file with the macro; works for Foundation Models and SwiftUI preview code.
- **Themes (new in Xcode 27)** — per-workspace themes so two projects look different; customizable diff colors (Xcode now defaults to red/green diffs).
- **Blue folders vs. gray groups** — folders don't record a file list, so adopting them can cut project size by roughly a third and "drastically" reduce `.xcodeproj` merge conflicts. Right-click > Convert to Folder; hold Option to pre-flight; resolve errors first, commit, then migrate.
- **Toolbar customization / simplified new-project flow (Xcode 27)** — toolbar controls promoted to top level and reorderable; new blank projects without a template modal.
- **Keyboard shortcuts** — a long round: Cmd-Shift-J, Cmd-Shift-Y, Cmd-Shift-O, Cmd-R/U/I, Cmd-Ctrl-R (run without building), Cmd-Option-P (refresh preview), Cmd-Option-Up/Down, Control-6, Control-backslash, Cmd-slash, and "all three modifiers + G" to re-run a test.
- **Previews** — pin individual canvas tabs (new this year); pass arguments to previews for a grid of variants; refresh with Cmd-Option-P.
- **Instruments & profiling** — Memory Graph exported into Instruments; flame graph + top functions; SwiftUI / concurrency / Foundation Models templates; profile on a real device, not the simulator, and on a release (optimized) build, not debug.
- **Build performance** — modularize into packages/frameworks; XCConfig files for build settings; build-timeline assistant; type-check timing warnings; declare script-phase inputs.
- **Derived data** — both a superstition and a real workaround for build-system race conditions or hidden target dependencies; Xcode 27 should reduce the need; Swift Build is open source.
- **Source control** — use Git even solo ("best undo system ever"); redone navigator infrastructure; much faster with millions of tags; multi-select stash deletion requested.
- **Code signing** — prefer automatic signing; don't distribution-sign locally; Xcode Cloud handles signing for CI.
- **Agentic / LLM workflows** — agents to organize files, write/refine docs, reach for plan mode and on-disk artifacts so context survives compression; Claude Code (read as "Cloud Code") usage noted by a developer.
- **DocC** — documentation preview assistant; host a DocC archive as a static site for PR review; light/dark images that auto-swap.
- **Running Xcode 26 + 27 side by side** — supported on macOS Tahoe; stick to the public SDK; watch `xcode-select`; consider Xcode Cloud (25 free hours).

## Related sessions (referenced on-air — titles paraphrased, numbers not stated)

The panel referenced sessions by description, not by number. None were given an explicit session number on air, so these are left unverified.

- [ ] Memory analysis session that demonstrates exporting the Memory Graph to Instruments (referenced ~00:08:42).
- [ ] A profiling session this year described as "Profile fixer" (transcript unclear) that explains profiling concepts (referenced ~00:17:11).
- [ ] A SwiftUI Instrument session from "last year" (referenced ~00:17:35).
- [ ] A Swift Concurrency code-along from last year, attributed to "SEMA" (transcript unclear) (referenced ~00:36:14).
- [ ] DocC sessions "from a few years ago" (titles not recalled on air; ~00:39:54).
- [ ] A "build timeline" session "from a few years ago" about using the build timeline (referenced ~00:42:23).

## Chapter summary

No published chapters (group lab). Rough arc:

- **00:00:03** — Welcome; host Angelica introduces the panel.
- **00:00:21** — Self-introductions (Chris Miles, Jake, John, Casper, second Chris).
- **00:01:19** — Warm-up: each panelist's favorite lesser-known Xcode tip.
- **00:05:35** — "Which recent feature deserves more attention?" / underrated features (blue folders, scheme user-defaults, console filtering, jump-bar filtering, preview variants, Memory Graph in Instruments, conditional breakpoints).
- **00:09:21** — Thank-you for the Delete Derived Data menu item; behind-the-scenes on why people delete it.
- **00:10:30** — Running Xcode 27 beta alongside Xcode 26.
- **00:12:28** — Most underrated features even experienced devs miss (folders, pinned canvas tabs, Xcode Cloud, search navigator, type hierarchy, profiling).
- **00:18:08** — Caveats running 26 + 27 on one machine.
- **00:19:05** — Minimizing `.xcodeproj` merge conflicts without third-party tools (Tuist/XcodeGen); folder adoption workflow; cross-project references checkbox; XCConfig files.
- **00:22:41** — Markdown rendering and editing; source-control improvements / multi-select stash request.
- **00:25:57** — SPM macro re-approval / macro trust pinning.
- **00:26:55** — Opening Terminal at the project root; working-copy relative path.
- **00:27:55** — Daily keyboard shortcuts round.
- **00:31:28** — Common beginner mistakes (don't learn everything at once, automatic signing, use Git, profile on device/release, adopt SwiftUI + Swift Concurrency, organize early).
- **00:37:54** — DocC documentation workflows.
- **00:40:06** — Build-time / build-performance settings and practices.
- **00:43:05** — Faster Xcode previews.
- **00:44:08** — Integrating swift-format into Xcode.
- **00:45:39** — Underutilized LLDB commands / breakpoint actions without stopping execution.
- **00:49:11** — Seeing unused vars/properties/functions.
- **00:50:38** — Is deleting derived data engineering or superstition? What else to reach for.
- **00:54:50** — Agent context compression in "Cloud Code" (feature request).
- **00:56:40** — Signing and provisioning-profile mismatches.
- **00:59:05** — Code folding / structural navigation to suppress visual noise (closing question).
- **01:00:32** — Wrap-up; thanks; pointers to Developer Forums, the generative-AI search on developer.apple.com, and Feedback Assistant.

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. Feature, menu, and shortcut names are captured in `digest.md`.
