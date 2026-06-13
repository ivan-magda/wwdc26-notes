---
title: "Dub Dub Daily: Day 5 — Digest"
session: "WWDC26 · 400"
url: https://developer.apple.com/videos/play/wwdc2026/400/
duration: 00:07:35
sources:
  - transcript.md (whisper.cpp ggml-large-v3, machine-generated, no diarization)
compiled: 2026-06-13
---

> Compiled from a machine-generated transcript (whisper.cpp `ggml-large-v3`, no speaker diarization). Uncertain names/titles are flagged "(transcript unclear)".

## TL;DR

- Final-day episode of the daily recap show; mostly a reflection on the week plus one closing interview.
- Two more group labs that morning; reminder that all group labs are available on demand.
- The bulk of the episode: **Jeff (Apple) interviews Holly Borla**, Engineering Manager on the Swift team, about **Swift 6.4**.
- Swift 6.4 themes: removing friction (`some`/`any` with optionals, `async` in `defer`), and big **diagnostic improvements** including a new `@diagnose` attribute.
- Outro points to worldwide WWDC recap events and Apple developer centers (new one coming to Berlin).

## What Day 5 covered

**Open and week wrap.** The host welcomes viewers to the final day of WWDC26, notes two more good group labs that morning, and reminds everyone that all group labs are available on demand to rewatch anytime.

**Setup for the close.** Swift is called the "through line" of the whole week, which leads into the episode's main segment: a previously recorded conversation between Apple's **Jeff** and **Holly Borla**, Engineering Manager on the Swift team.

**Holly Borla on Swift 6.4 (main segment).**

- *The theme.* This year's Swift is "designed to help developers get back to having fun." For Holly that means two things: writing clear and beautiful code that reads well, and having confidence the code is correct, with the compiler there to catch mistakes.
- *Friction removal.* Many Swift 6.4 updates remove friction points you may not even notice until pointed out:
  - A more streamlined way to use `some` and `any` types together with optionals: previously needed extra parenthesis syntax, now "what people naturally try to write will just work."
  - Calling an `async` method inside a `defer` block: previously a compiler limitation, now works like async calls elsewhere in a function body. Framed as "addition by subtraction."
- *Diagnostic improvements (her under-the-hood pick).* Better compiler error messages, since diagnostics are the main feedback loop when writing Swift. Swift 6.4 brings infrastructural improvements plus more ways to control diagnostics in your code. Her favorite: the **`@diagnose` attribute** (transcript: "at diagnose") to control warning behavior, e.g. suppress deprecations, or opt into strict memory safety / strict concurrency diagnostics on a granular basis to ease migration to the Swift 6 language mode.
- *The infamous error.* "The compiler is unable to type check this expression in reasonable time" is a type-checker fallback tied to type inference / overload resolution hitting time and memory thresholds. This year the team focused on mitigating it in **nested closures and SwiftUI view bodies**, a common place to hit it. More work remains; it's tracked in the open-source Swift project with public roadmaps and updates.
- *What she's excited about.* Developers trying the new diagnostic-control features for migration workflows (especially enabling strict concurrency checking). She enjoys reading blog posts and watching talks where people share migration strategies and ways to use language features in real codebases.

**Outro.** Thanks to Holly and Jeff. The host points to WWDC recap activities worldwide and developer.apple.com/events for sessions, workshops, and labs (online and in person), including Apple developer centers in Bengaluru, Cupertino, Shanghai, Singapore, and soon Berlin (opening later this year). Sign-off: "Time to go check in on what my agent built in Xcode."

## Notable mentions & pointers

- **Holly Borla** — Engineering Manager, Swift team (interviewee). **Jeff** — Apple, interviewer (last name not given).
- **Swift 6.4** — the release discussed throughout the segment.
- **`@diagnose` attribute** (transcript "at diagnose") — new warning-control feature; title is plausible but transcript-derived, so verify exact spelling.
- **State of the Union** — referenced as "SO2" / "so too" (transcript unclear); Holly's slides and the "having fun" framing, plus the type-check-time error callout, came from it.
- **Open-source Swift project** — where the type-checker work and roadmaps are tracked publicly.
- **Group labs** — two more on Day 5 morning, all available on demand.
- **WWDC recap events** — developer.apple.com/events; developer centers in Bengaluru, Cupertino, Shanghai, Singapore, and Berlin (opening later this year).
- No specific session numbers are named in this episode.
