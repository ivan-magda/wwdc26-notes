# Session 372 — Unwrap PaperKit

- **URL:** https://developer.apple.com/videos/play/wwdc2026/372/
- **Duration:** 8m
- **Speakers:** Matt (engineer, Pencil and Paper team)

## Description

PaperKit is the canvas engine behind Notes, Preview (PDF markup), and Freeform.
In iOS, macOS, and visionOS 27 it opens up to third-party apps. This session shows
how to take full control of the canvas: the `PaperMarkup` data model (read/write
every element via the new `subelements` ordered set), concrete element types
(shapes, images, links, loupes, pencil strokes), per-element interaction control
with `allowedInteractions`, and `MarkupAdornment` overlays for interactive,
non-persisted UI. The running example is a comic-book editor.

## Key topics

- `PaperMarkup.subelements` — new property exposing all canvas elements as a `MarkupOrderedSet` (readable + writable, ordered) for programmatic read/modify
- `Markup` protocol — common properties (`frame`, `rotation`) shared by every element
- `allowedInteractions` — new per-element `MarkupInteractions` option set (move/resize/rotate, delete, style, select); `.readOnly` locks everything
- Concrete element types: `ShapeMarkup`, `ImageMarkup`, links, loupes, pencil strokes — each with its own properties (e.g. corner radius, control points, stroke/fill color)
- Built on PencilKit — Apple Pencil strokes become markup elements; PencilKit model APIs now add character recognition and Bézier path conversion
- `MarkupAdornment` — visual overlay anchored to canvas coordinates; tracks zoom/scroll; separate from persisted markup (never saved/printed/exported); ideal for buttons, annotations, collaboration UI
- `PaperMarkupViewController` delegate `didTapAdornmentWithID` for handling adornment taps
- Integration with Image Playground (`ImagePlaygroundViewController`) to generate artwork into panels

## Related sessions to fetch (referenced in this talk)

- [ ] Reading Between the Strokes with PencilKit
- [ ] Create high-quality images using Image Playground

## Chapter summary (Summary tab)

- **0:00 Introduction** — PaperKit is the canvas behind Notes, Preview, and Freeform, now open to your apps in iOS/macOS/visionOS 27. Agenda: data model, elements, adornments.
- **1:22 Data model** — `PaperMarkup.subelements` exposes every canvas element as a readable/writable ordered set (`MarkupOrderedSet`); `allowedInteractions` gives fine-grained control over what each element permits. Demo: turn comic templates into read-only panels.
- **3:41 Elements** — each element has a concrete type (shapes, images, links, loupes, pencil strokes) with its own properties; PaperKit builds on PencilKit so Apple Pencil strokes become markup elements. Demo: recolor panels via stroke/fill + background.
- **5:17 Adornments** — `MarkupAdornment` is a visual overlay anchored to canvas coordinates (buttons, annotations, collaboration UI) that tracks zoom/scroll and stays out of persisted markup.
- **7:11 Next steps** — build a fully interactive canvas: use the data model to read/modify contents, and add adornments for interactive overlays tailored to your app.

## Code

See `code.md` — 6 snippets extracted from the Code tab.
