---
title: "Unwrap PaperKit — Full Digest"
session: WWDC26 · 372
url: https://developer.apple.com/videos/play/wwdc2026/372/
duration: 8m
speakers: Matt (Pencil and Paper team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 372 — Unwrap PaperKit

## TL;DR

PaperKit — the canvas engine behind **Notes**, **Preview** (PDF markup), and
**Freeform** — opens to third-party apps in **iOS, macOS, and visionOS 27**. This
short session "unwraps" it into three controllable layers:

1. **Data model** — `PaperMarkup` now exposes a `subelements` property: a readable
   and writable `MarkupOrderedSet` of everything on the canvas. You can generate,
   read, and mutate canvas contents programmatically. A new `allowedInteractions`
   option set gives per-element control over what users can move, resize, rotate,
   delete, style, or select — with `.readOnly` to lock an element entirely.
2. **Elements** — every element is a concrete type (`ShapeMarkup`, `ImageMarkup`,
   links, loupes, pencil strokes) conforming to the `Markup` protocol, each with its
   own properties. PaperKit is built on **PencilKit**, so Apple Pencil strokes become
   markup elements.
3. **Adornments** — `MarkupAdornment` overlays are anchored to canvas coordinates,
   track zoom/scroll automatically, and stay **out of the persisted document** (never
   saved, printed, or exported). Perfect for buttons, annotations, and collaboration UI.

The whole talk is framed around a **comic-book editor**: templated panels become
read-only shapes, a color picker restyles them, and per-panel adornment buttons call
**Image Playground** to generate artwork straight into the panels.

---

## 1. Data model — `PaperMarkup.subelements`

`PaperMarkup` gains a new `subelements` property: a `MarkupOrderedSet`, an ordered
collection you can read from and write to. It gives access to every element on the
canvas. The comic editor turns its layout templates into markup by creating one shape
per panel and assigning the set back.

```swift
import PaperKit

func generateMarkup(pageSize: CGSize, panelFrames: [CGRect], configuration: ShapeConfiguration) -> PaperMarkup {
    var markup = PaperMarkup(bounds: CGRect(origin: .zero, size: pageSize))
    var subelements: MarkupOrderedSet = markup.subelements
    for panelFrame: CGRect in panelFrames {
        let shape = ShapeMarkup(frame: panelFrame, configuration: configuration)
        subelements.append(shape)
    }
    markup.subelements = subelements
    return markup
}
```

### Per-element interaction control — `allowedInteractions`

Out of the box the canvas is fully interactive — panels can be selected, dragged, and
deleted. For template panels that's wrong. Every element conforms to the `Markup`
protocol (common properties like `frame` and `rotation`) and now also has an
`allowedInteractions` property, a `MarkupInteractions` **option set** covering:

- moving, resizing, rotating
- deleting
- styling
- selecting

These can be combined individually or in any combination; `.readOnly` collapses them
all into a single flag. The comic template just locks everything:

```swift
var shape = ShapeMarkup(frame: panelFrame, configuration: configuration)
shape.allowedInteractions = .readOnly
subelements.append(shape)
```

After this, tapping a panel border does nothing, but the user can still add a speech
bubble, move it, and stylize it — only the template panels are fixed.

## 2. Elements — concrete types with their own properties

Every element in `PaperMarkup` has a concrete type — **shapes, images, links, loupes,
pencil strokes** — all part of the same `MarkupOrderedSet` and all conforming to
`Markup`, but each with custom properties:

- **Shapes**: many types; per-type properties like **corner radius** (rounded
  rectangles) or **control points** (curved lines). Rectangles (used for the comic
  panels) expose `strokeColor` and `fillColor`.
- **Images**: `ImageMarkup`.
- Plus links, loupes, and pencil strokes.

Styling the panels = iterate the subelements, set stroke and fill, and (for extra pop)
match the markup background:

```swift
func updatePanelColor(_ selectedColor: CGColor) {
    guard var markup: PaperMarkup = paperMarkupViewController.markup else { return }
    var subelements: MarkupOrderedSet = markup.subelements
    for element in subelements {
        guard var shape = element as? ShapeMarkup else { continue }
        shape.strokeColor = selectedColor
        shape.fillColor = selectedColor.copy(alpha: 0.15)
        subelements.updateOrAppend(shape)
    }
    markup.subelements = subelements
    markup.backgroundColor = selectedColor.copy(alpha: 0.15)
    paperMarkupViewController.markup = markup
}
```

Note the read-modify-write loop: pull `markup`, mutate value-type shapes, `updateOrAppend`
them back into the ordered set, then reassign `markup` to the view controller.

### Built on PencilKit

PaperKit sits on top of PencilKit, so the Apple Pencil draws directly on the canvas and
**each stroke becomes a markup element**. All PencilKit model APIs apply, and those APIs
now add **character recognition** and **Bézier path conversion**. Deep-dive: *"Reading
Between the Strokes with PencilKit."*

## 3. Adornments — interactive overlays outside the document

The editor wants a per-panel button to create artwork — but those controls should never
become part of the document (not saved, printed, or exported); they should only exist on
top of the canvas while editing. That's a `MarkupAdornment`: a visual overlay anchored to
canvas coordinates, ideal for buttons, annotations, and collaboration UI. Adornments
automatically track zoom and scroll and are completely separate from the persisted markup.

```swift
func addPanelAdornments(for page: Page) {
    var adornments: [MarkupAdornment] = []
    for (panelIndex, panel) in page.panels.enumerated() {
        let adornmentID = UUID()
        adornmentPanelMapping[adornmentID] = panelIndex
        let center = CGPoint(x: panel.midX, y: panel.midY)
        let adornment = MarkupAdornment(
            id: adornmentID,
            anchor: .canvas(location: center),
            imageConfiguration: .systemImage("photo.badge.plus"),
            dragRegion: .fixed,
            scalesWithZoom: false
        )
        adornments.append(adornment)
    }
    paperMarkupViewController.adornments = adornments
}
```

Each adornment is anchored to a panel center, gets an SF Symbol via `imageConfiguration`,
and the array is assigned to the controller's `adornments` property. App-side, a
dictionary (`adornmentPanelMapping`) maps the adornment `UUID` back to its panel index.

### Handling taps → Image Playground

Taps arrive through the delegate method `didTapAdornmentWithID`. The editor presents an
`ImagePlaygroundViewController` for the tapped panel:

```swift
func paperMarkupViewController(_ paperMarkupViewController: PaperMarkupViewController, didTapAdornmentWithID id: UUID) {
    guard let panelIndex = adornmentPanelMapping[id] else { return }
    activeImageGenerationPanelIndex = panelIndex

    let imagePlaygroundViewController = ImagePlaygroundViewController()
    imagePlaygroundViewController.delegate = self
    present(imagePlaygroundViewController, animated: true)
}
```

When an image comes back, wrap it in an `ImageMarkup` and append it to the subelements:

```swift
func imageViewController(_ imageViewController: ImagePlaygroundViewController, didCreateImageAt imageURL: URL) {
    guard let panelFrame = activeGenerationPanelFrame,
          let paperMarkupViewController = pageViewController.paperViewController,
          var markup = paperMarkupViewController.markup,
          let image = UIImage(contentsOfFile: imageURL.path) else { return }

    let imageMarkup = ImageMarkup(frame: panelFrame, image: image)
    markup.subelements.append(imageMarkup)
    paperMarkupViewController.markup = markup
}
```

The generated image fills the panel. Deep-dive on generation: *"Create high-quality images
using Image Playground."*

## Next steps

You can now build a fully interactive, canvas-based experience with PaperKit: use the data
model to programmatically read and modify what's on the canvas, and add adornments for
interactive overlays tailored to your app.
