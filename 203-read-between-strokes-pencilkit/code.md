# Code samples — Session 203

Extracted from the Code tab. The three snippets correspond to the three
`PKStrokeRecognizer` capabilities walked through from ~3:25.

## Recognized text — single most likely result

```swift
import PencilKit

let recognizer = PKStrokeRecognizer()
await recognizer.updateDrawing(drawing)
myLabel.text = await recognizer.recognizedText()
```

## Indexable content — all candidates concatenated, for Spotlight indexing

```swift
import PencilKit

let recognizer = PKStrokeRecognizer()
await recognizer.updateDrawing(drawing)
if let indexedContent = await recognizer.indexableContent {
    index(text: indexedContent)
}
```

## Search — locate a target string and highlight its bounds

```swift
import PencilKit

let recognizer = PKStrokeRecognizer()
await recognizer.updateDrawing(drawing)
let results = await recognizer.search("apple")
for result in results {
    highlight(bounds: result.bounds)
}
```

---

## Spoken APIs (named in the transcript, no Code-tab snippet)

These were described but not shown as code on screen — names and shapes are taken
from the narration, so treat exact signatures as approximate until confirmed in the
sample project / docs.

- `PKStrokeRecognizer` — a **Swift actor**; all methods async.
- `PKStrokeRecognizer.supportedLanguages` — list of supported languages (29 as of iOS 27).
- `preferredLanguages` — configurable to match the app's language context (defaults to device languages).
- Recognition can target the whole drawing or a subset of `strokeID`s.
- `recognizerVersion` — store alongside indexed content; compare on load to decide whether to re-index.
- `search()` results expose `bounds`; pair with `UIFindInteraction` / `UIFindInteractionDelegate` for the system find UI.
- **Path conversion:** `PKStrokePath` ⇄ Bézier path. PencilKit handles geometry; Bézier paths don't carry size/opacity/force, so you supply those per control point. Round-trips losslessly (same control-point locations).
- Convert app-owned Bézier strokes → `PKStrokePath` → build a `PKDrawing` → feed into `PKStrokeRecognizer` (recognition on any canvas, not just `PKCanvasView`).
- **Model access:** `PKStroke` and `PKStrokePath` conform to `Identifiable` (stable `UUID` across transforms/edits/undo).
- Controllable selection on `PKCanvasView`; new delegate method `canvasViewSelectionDidChange`.
- Controllable `renderGroupID` (wet-ink compositing of strokes drawn together quickly).
- **Stroke slicing — programmatic erasing:** provide a `PKStrokePath` as the eraser; slices one stroke into multiple independent strokes, each with its own mask. Can be expensive on complex drawings — consider a background thread.
- **Stroke slicing — substroke extraction:** `PKStroke` and `PKStrokePath` support **subscript access with parametric ranges** to obtain a section as a new stroke/path; texture-particle consistency is preserved.
