# Code samples — Session 372

Extracted from the Code tab. Timestamps map to the transcript chapters.

## 1:47 — Generate markup: a shape per panel

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

## 3:03 — Lock template panels with `.allowedInteractions = .readOnly`

```swift
import PaperKit

func generateMarkup(pageSize: CGSize, panelFrames: [CGRect], configuration: ShapeConfiguration) -> PaperMarkup {
    var markup = PaperMarkup(bounds: CGRect(origin: .zero, size: pageSize))
    var subelements: MarkupOrderedSet = markup.subelements
    for panelFrame: CGRect in panelFrames {
        var shape = ShapeMarkup(frame: panelFrame, configuration: configuration)
        shape.allowedInteractions = .readOnly
        subelements.append(shape)
    }
    markup.subelements = subelements
    return markup
}
```

## 4:22 — Style panels: iterate subelements, set stroke/fill + background

```swift
import PaperKit

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

## 5:53 — Add a `MarkupAdornment` button per panel

```swift
import PaperKit

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

## 6:09 — Handle adornment taps: present Image Playground

```swift
import ImagePlayground
import PaperKit

func paperMarkupViewController(_ paperMarkupViewController: PaperMarkupViewController, didTapAdornmentWithID id: UUID) {
    guard let panelIndex = adornmentPanelMapping[id] else { return }
    activeImageGenerationPanelIndex = panelIndex

    let imagePlaygroundViewController = ImagePlaygroundViewController()
    imagePlaygroundViewController.delegate = self
    present(imagePlaygroundViewController, animated: true)
}
```

## 6:21 — Insert the generated image as an `ImageMarkup`

```swift
import ImagePlayground
import PaperKit

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

---

## Useful API facts surfaced by the code

- `PaperMarkup(bounds:)` initializer; `.subelements` is a mutable `MarkupOrderedSet`.
- `MarkupOrderedSet` supports `append(_)` and `updateOrAppend(_)` (set semantics — update by identity or append).
- `ShapeMarkup(frame:configuration:)` with `ShapeConfiguration`; mutable `strokeColor`, `fillColor` (`CGColor`), `allowedInteractions`.
- `PaperMarkup.backgroundColor` is a `CGColor`.
- `MarkupAdornment` init params: `id: UUID`, `anchor: .canvas(location:)`, `imageConfiguration: .systemImage(_)`, `dragRegion: .fixed`, `scalesWithZoom: Bool`.
- `PaperMarkupViewController` has an `adornments: [MarkupAdornment]` property and a delegate method `didTapAdornmentWithID:`.
- `ImageMarkup(frame:image:)` takes a `UIImage`.
- App-side bookkeeping (`adornmentPanelMapping`, `activeImageGenerationPanelIndex` / `activeGenerationPanelFrame`) maps adornment IDs back to panels.

> Note: the Code tab uses both `activeImageGenerationPanelIndex` (in the tap handler)
> and `activeGenerationPanelFrame` (in the did-create handler) — two different
> identifiers across the two snippets; reconcile to one source of truth when reproducing.
