---
title: "Read between the strokes with PencilKit — Full Digest"
session: WWDC26 · 203
url: https://developer.apple.com/videos/play/wwdc2026/203/
duration: 15m
speakers: Yichen
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 203 — Read between the strokes with PencilKit

## TL;DR

PencilKit opens up the machinery behind handwriting in Notes and Freeform. Four
headline themes, all landing in **iOS / iPadOS / macOS / visionOS 27**:

1. **Handwriting recognition is finally public.** `PKStrokeRecognizer` (a Swift actor,
   fully async, fully on-device) turns strokes into text via three capabilities:
   **recognized text** (one best answer), **indexable content** (all candidates, for
   Spotlight), and **search** (bounded hits you can highlight, wired to
   `UIFindInteraction`). 29 languages.
2. **Bézier ⇄ PKStrokePath conversion, lossless.** PencilKit stores paths as cubic
   uniform B-splines; you can now round-trip to/from Bézier without losing fidelity —
   which means recognition works on *any* canvas, not just `PKCanvasView`.
3. **Deeper model access.** `PKStroke` / `PKStrokePath` are now `Identifiable` with
   stable UUIDs that survive transforms, edits, and undo; canvas selection is
   controllable with a `canvasViewSelectionDidChange` delegate; wet-ink `renderGroupID`
   is controllable.
4. **Stroke slicing.** Programmatic erasing (a `PKStrokePath` acts as the eraser,
   cutting one stroke into independent masked strokes) and substroke extraction
   (subscript access with parametric ranges) — texture particles stay consistent.

All of it also works with **PaperKit**. The running demo is the speaker's app for his
son to practice writing words in **Chinese and English**, checking handwriting against
a flashcard prompt before the word is even finished.

---

## 1. Handwriting recognition — `PKStrokeRecognizer`

`PKStrokeRecognizer` is a **Swift actor** (thread-safe by design) and every method is
**async**, because recognition takes time. It runs **entirely on device** against an
offline model bundled with the OS, is fast, and works on all devices that support
iOS 27. As of iOS 27 it covers **29 languages** (`supportedLanguages`); by default it
uses the device's languages, but you can set `preferredLanguages` to match your app's
context. Recognition can target the whole drawing or a subset of stroke IDs.

> Note: in **Simulator**, recognition only supports Latin-script languages.

Three capabilities:

### Recognized text — the single best answer

```swift
import PencilKit

let recognizer = PKStrokeRecognizer()
await recognizer.updateDrawing(drawing)
myLabel.text = await recognizer.recognizedText()
```

In the demo's open-ended practice view the speaker writes "Hello WWDC" and it comes
back recognized.

### Indexable content — every candidate, for indexing

```swift
import PencilKit

let recognizer = PKStrokeRecognizer()
await recognizer.updateDrawing(drawing)
if let indexedContent = await recognizer.indexableContent {
    index(text: indexedContent)
}
```

Where recognized text gives one best answer, **indexable content gives all the
candidates concatenated** — so a stroke that's ambiguously "1" or "l", or "101" vs
"lol", lands every interpretation in your Spotlight index and the user finds it no
matter what they type. With multiple active languages it can contain results in more
than one language.

Two persistence gotchas called out in the talk:

- Recognition results **improve over time** as the underlying models update. Store
  `recognizerVersion` alongside your indexed content and compare on load to decide
  whether to re-index.
- **Throttle** your recognizer calls — re-running on every stroke burns more power
  than an indexing feature needs.

### Search — locate and highlight a string

```swift
import PencilKit

let recognizer = PKStrokeRecognizer()
await recognizer.updateDrawing(drawing)
let results = await recognizer.search("apple")
for result in results {
    highlight(bounds: result.bounds)
}
```

Given a target string, `search()` returns results indicating where the word likely
appears (considering all candidates), each with `bounds` you can draw a highlight
around. It pairs naturally with **`UIFindInteraction`**: implement
`UIFindInteractionDelegate`, drive it with `search()` under the hood, and you get the
system find-and-replace UI — result navigation and highlighting — directly in your
canvas. In the flashcard demo, search bounds become a box drawn over the matched
strokes.

### Accessibility

These same APIs make handwritten content accessible: connect **VoiceOver** to speak
handwriting aloud, and let assistive features locate/navigate specific words via
search — bridging handwritten and typed text.

## 2. Path conversion — Bézier ⇄ `PKStrokePath`

PencilKit represents stroke paths as **cubic uniform B-splines** (see "Inspect,
modify, and construct PencilKit drawings", WWDC20). B-splines are great for drawing but
less common than Bézier paths, so in iOS 27 `PKStrokePath` supports converting between
the two.

- PencilKit handles the **geometry** of the conversion.
- Bézier paths **don't carry PencilKit properties** (size, opacity, force), so you
  supply those per control point when going back.
- Starting from a `PKStrokePath`, converting **to Bézier and back yields the same
  control-point locations** — so you can store PencilKit strokes in a Bézier-based
  format and reconstruct them with **no loss of fidelity**.

The payoff: if your app already has its own canvas storing strokes as Bézier paths,
convert them to `PKStrokePath`s, build a `PKDrawing`, and feed that into
`PKStrokeRecognizer`. **Handwriting recognition now works on any canvas, not just
`PKCanvasView`.**

## 3. Deeper drawing-model access

iOS 27 adds several model-level hooks for custom experiences:

- **Stable identity:** `PKStroke` and `PKStrokePath` conform to **`Identifiable`** with
  a stable **`UUID`**, so you can track a stroke across transforms, edits, and **undo**.
- **Selection control:** with stable identity in place, you can now **control the
  selection state** on `PKCanvasView`, plus a new delegate method
  **`canvasViewSelectionDidChange`** that fires whenever the user's selection changes.
- **Render groups:** when inks are drawn together quickly, PencilKit composites them as
  if still wet via an equal **`renderGroupID`**. In iOS 27 that grouping is
  **controllable**.

## 4. Stroke slicing

Two ways to cut through strokes.

### Programmatic erasing

PencilKit represents partial erasure with a **mask** — when the pixel eraser removes
part of a stroke, the remaining visible portions are defined by that mask. In iOS 27
you can do the same operation **programmatically**: provide a `PKStrokePath` as the
eraser, and it cuts through the drawing, **slicing a single stroke into multiple
independent strokes, each with its own mask** — exactly as if the user had used the
eraser tool.

> Performance: slicing can be **expensive on complex drawings**. With a large number of
> strokes, consider erasing on a **background thread** rather than blocking the UI.

### Substroke extraction

Where erasing visually cuts strokes, substroke extraction efficiently grabs a section
as a new stroke or path. In iOS 27 both `PKStroke` and `PKStrokePath` support
**subscript access with parametric ranges** anywhere along the path, giving precise
control over where a slice begins and ends. PencilKit ink features like **pencil
texture particles** (positioned relative to the full stroke) stay **consistent** when
you take a substroke.

The demo: for Chinese characters, stroke order matters, so the speaker built a feature
that uses substrokes to **replay how a word was written** to check the order.
PencilKit renders in **Metal** with optimizations that keep the animation smooth.

## Next steps (from the talk)

- Adopt `PKStrokeRecognizer` for handwriting recognition.
- Try PencilKit in new places — convert existing Bézier paths to `PKStrokePath`s and
  get recognition even without `PKCanvasView`.
- Dive into the model: track strokes with stable identity, respond to selection
  changes, build custom experiences.
- Use stroke slicing — programmatic erasing and substroke animations.
- **Sample code** for the demo is in the video's resources.
