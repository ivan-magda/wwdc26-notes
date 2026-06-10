# Session 203 — Read between the strokes with PencilKit

- **URL:** https://developer.apple.com/videos/play/wwdc2026/203/
- **Duration:** 15m
- **Speakers:** Yichen (PencilKit / drawing features engineer)
- **Platforms:** iOS, iPadOS, macOS, visionOS 27

## Description

Meet the PencilKit APIs behind handwriting in Notes and Freeform, now available to
your apps in iOS 27 (and iPadOS / macOS / visionOS 27). The session covers on-device
handwriting recognition (`PKStrokeRecognizer`), conversion between PencilKit stroke
paths and standard Bézier paths, deeper access to the drawing model (stable stroke
identity, selection control, render groups), and new stroke slicing APIs (programmatic
erasing and substroke extraction). All new APIs also work with PaperKit.

## Key topics

- `PKStrokeRecognizer` — a Swift actor (thread-safe), fully async; three capabilities: recognized text, indexable content, and search
- On-device, offline recognition; **29 languages** as of iOS 27; `supportedLanguages` / `preferredLanguages`; Simulator limited to Latin-script languages
- Indexable content returns *all* candidate interpretations (for Spotlight indexing); `recognizerVersion` for re-index decisions; throttle calls to save power
- `search()` returns bounded results and pairs with `UIFindInteraction` / `UIFindInteractionDelegate` for system find-and-replace UI; powers VoiceOver/accessibility
- Path conversion — PencilKit stores paths as cubic uniform B-splines; `PKStrokePath` now converts to/from Bézier paths losslessly (control points round-trip); bring recognition to any canvas, not just `PKCanvasView`
- Drawing model access — `PKStroke` and `PKStrokePath` conform to `Identifiable` (stable UUID surviving transforms/edits/undo); controllable canvas selection + `canvasViewSelectionDidChange` delegate; controllable `renderGroupID` (wet-ink compositing)
- Stroke slicing — programmatic erasing (provide a `PKStrokePath` eraser; cuts one stroke into independent masked strokes); substroke extraction via subscript with parametric ranges; texture-particle consistency preserved; Metal rendering for smooth animation

## Related sessions to fetch (referenced in this talk)

- [ ] Meet PaperKit (WWDC25)
- [ ] Unwrap PaperKit (WWDC26)
- [ ] Inspect, modify, and construct PencilKit drawings (WWDC20)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Meet the PencilKit APIs behind handwriting in Notes and Freeform, now available to your apps in iOS 27.
- **3:25 Handwriting recognition** — Use the stroke recognizer API for on-device text recognition, indexing, search, and accessibility.
- **8:38 Path conversion** — Converting `PKStrokePath` to and from Bézier paths without losing fidelity, so apps that store strokes as Bézier can build `PKDrawing`s and use handwriting recognition on any canvas, not just `PKCanvasView`.
- **10:21 Improved model access** — Deeper access to the drawing model in iOS 27: stable `Identifiable` stroke IDs that survive edits and undo, controllable canvas selection with a change delegate, and adjustable wet-ink render groups.
- **11:25 Stroke slicing** — Two ways to slice strokes — programmatic erasing that cuts one stroke into independent strokes, and substroke extraction with parametric ranges — along with performance considerations for complex drawings.
- **13:48 Next steps** — Adopt `PKStrokeRecognizer`, convert existing Bézier paths, track strokes with stable identity, and use stroke slicing for erasing and animation.

## Code

See `code.md` — 3 snippets from the Code tab (the three `PKStrokeRecognizer` capabilities). Additional APIs are named in the transcript but not shown as code; they are listed in `code.md` under "Spoken APIs".
