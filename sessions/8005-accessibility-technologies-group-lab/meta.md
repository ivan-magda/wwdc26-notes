# Session 8005 — Accessibility Technologies Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8005/
- **Duration:** 01:00:31 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + accessibility panel answering inbound developer questions live, triaged behind the scenes.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so panelist attributions below come from self-introductions and host routing only.

## Panel

- **Cole** — host; core technologies evangelist at Apple. Routes questions, demos enthusiasm, no deep API answers.
- **Julia Sonnen** — product marketing manager for accessibility (worldwide product marketing); covers accessibility features across all Apple products. Fresh off Global Accessibility Awareness Day (GAD).
- **Drew** — software engineer on the accessibility team; works across iOS, visionOS, iPadOS with a focus in cognitive accessibility. Co-presented a visionOS accessibility session with a teammate named Dan when visionOS shipped.
- **Greg** — manager of a software engineering team that works on many accessibility products; self-described user of "many of our low vision products."
- **Syl** — software engineering accessibility quality team; a VoiceOver user who spends a lot of time testing VoiceOver and braille. (whisper renders this name inconsistently as "Syl," "Sil," "Scylla," "Sylla" — treated as one person, the QA/VoiceOver-user panelist.)

> Note: whisper inserted a stray "BRIAN DORSEY:" speaker tag at cue 247 mid-answer. There is no Brian on the introduced roster; treat it as a transcription artifact, not a panelist.

## Description

A roughly one-hour live accessibility group lab. Julia opens with a tour of what's new for accessibility in the 27 releases (much of it announced around GAD on May 21), then the panel works through inbound developer questions covering testing strategy, VoiceOver behavior, dynamic type, accessibility nutrition labels, cognitive features, mobility/input alternatives, cross-platform pitfalls, and developer-tool accessibility. Recurring throughline: **test VoiceOver first for the most bang for your buck, design accessibility in from day one, get feedback from real users, and file feedback in Feedback Assistant.**

## Key topics

- **What's new in 27 for accessibility** — Apple Intelligence brought into VoiceOver image descriptions and Live Recognition, more personalized Accessibility Reader, more intuitive Voice Control (natural-language on-screen description), automatic subtitles in personal videos, spatialized vehicle motion cues on visionOS.
- **Testing strategy** — Accessibility Inspector to audit elements; Device Hub for screen sizes; test VoiceOver first (shared backend APIs mean a great VoiceOver pass largely covers Switch Control / Voice Control); get real-user feedback.
- **Image descriptions** — built-in VoiceOver image descriptions + Image Explorer (describes charts and data-rich images, supports follow-up questions). Removing the image trait suppresses the system-appended description but also denies the user the new description features; a subtle sound marks where author alt text ends and auto-description begins.
- **VoiceOver developer APIs** — layout-changed vs screen-changed notifications; announcement notification for completion; accessibility representation (SwiftUI) and accessibility children / custom elements (AppKit) to expose custom controls.
- **Dynamic Type** — now on tvOS in 27; large-text nutrition label for tvOS; relayout when accessibility font sizes are enabled.
- **Accessibility Nutrition Labels** — surface app accessibility before download; come with testing guidelines that act as a benchmark and an internal advocacy tool.
- **Cognitive accessibility** — Guided Access, Assistive Access (now includes a TV app; developer API for custom optimized app versions), Speak Screen / Speak Selection, Accessibility Reader, new streamlined Touch Accommodations setup with a calibration activity in iOS 27.
- **Mobility / input alternatives** — Voice Control, head tracking, on-device eye tracking, Sound Actions, Switch Control, Touch Accommodations, AssistiveTouch, Reachability; on visionOS, Pointer Control (head / finger / wrist as pointer instead of gaze).
- **Cross-platform pitfalls** — Mac users expect keyboard shortcuts and hierarchical/grouped VoiceOver navigation; iOS VoiceOver is linear and hit-testing matters more; per-platform tweaking and testing required even with SwiftUI.
- **Developer-tool accessibility** — improved Terminal accessibility for VoiceOver this year; Xcode VoiceOver bug fixes in seed 1; SwiftUI levels the playing field for blind developers (no WYSIWYG required); new Xcode AI skills for VoiceOver and Dynamic Type; new XCTest API to drive VoiceOver from a Mac against iOS.
- **Announced-but-not-yet-shipped** — FaceTime video interpreting feature (no API in iOS 27 beta yet); no new text-to-speech APIs this year.

## Related sessions (referenced on-air)

The panel mentioned WWDC sessions by topic, mostly without numbers. Identifiable references:

- A **new WWDC26 session on large text / Dynamic Type for tvOS** (attributed on-air to "ECs," likely a garbled team/presenter name). Session number not stated.
- A **visionOS accessibility session from the visionOS launch year**, co-presented by Drew and a teammate "Dan," covering Pointer Control and making RealityKit content accessible. Session number not stated.
- A prior-year **Assistive Access** session (developer API for optimized app versions). Number not stated.
- A prior-year **Accessibility Nutrition Labels** session ("last year"). Number not stated.
- A prior-year **Mac accessibility** session demoing inspector-pane grouping. Number not stated.
- A **tech talk by "Ryan"** on preparing your app for accessibility nutrition labels (Control Center / larger-text controls example). Not a numbered WWDC session.

> No session numbers were spoken on-air, so none are asserted here.

## Chapter summary

No published chapters (group lab). Rough arc with timestamps:

- 00:00 — Intros (Cole + panel self-introductions).
- 01:34 — What's new for accessibility in the 27 releases (Julia) + panel favorites.
- 04:51 — Developer Q&A begins (testing usability).
- 05:45 — Prioritizing across assistive technologies / VoiceOver-first.
- 09:26 — Image descriptions override question.
- 12:03 — EHR / patient-portal: where to start.
- 15:02 — VoiceOver progress/completion announcements.
- 16:29 — Text-to-speech API updates (none).
- 16:56 — Dynamic Type on tvOS + nutrition labels.
- 21:39 — Device Hub VoiceOver option + new XCTest VoiceOver API.
- 23:07 — macOS VoiceOver, accelerators, accessibility shortcut.
- 26:03 — Custom AppKit controls (accessibility representation / children).
- 28:30 — Designing for limited/no hand use; mobility alternatives; Touch Accommodations calibration.
- 32:09 — Neurodivergent features (Guided Access, Assistive Access).
- 34:38 — SwiftUI reorderable + drag-and-drop in VoiceOver.
- 35:44 — FaceTime video interpreting (not yet shipped).
- 36:22 — Stable accessibility identifiers for reusable SwiftUI views.
- 37:38 — Building accessibility in from the start; large-team buy-in.
- 44:38 — Cross-platform pitfalls across iOS/macOS/iPadOS/watchOS/tvOS.
- 50:54 — AI voices for VoiceOver.
- 56:04 — Preventing accessibility regressions / automated testing.
- 53:43 — Xcode + developer-tool accessibility (Terminal, AI skills).
- 57:21 — visionOS gaze alternatives (Pointer Control).
- 58:55 — Thank-you note; "accessibility is for everyone."
- 59:18 — Wrap-up (forums, Feedback Assistant, accessibility@apple.com).

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API/feature names are captured in `digest.md`.
