# Code samples — Session 375

Extracted from the Code tab. Timestamps map loosely to the transcript chapters.

## `imagePlaygroundSheet` modifier signature

```swift
func imagePlaygroundSheet(
    isPresented: Binding<Bool>,
    concepts: [ImagePlaygroundConcept] = [],
    sourceImage: Image? = nil,
    onCompletion: @escaping (URL) -> Void,
    onCancellation: (() -> Void)? = nil
) -> some View
```

## Adopt Image Playground (minimal)

```swift
@State private var showingPlayground = false

var body: some View {
    Button("Create image") {
        showingPlayground = true
    }
    .imagePlaygroundSheet(
        isPresented: $showingPlayground,
        onCompletion: { url in
            var updated = currentCard
            store.saveImage(url, for: &updated)
        }
    )
}
```

## Seeding the sheet with context (concepts)

```swift
var concepts: [ImagePlaygroundConcept] {
    [
        .text(card.theme),
        .extracted(from: card.message, title: card.theme),
    ]
}

var body: some View {
    Button("Create image") {
        showingPlayground = true
    }
    .imagePlaygroundSheet(
        isPresented: $showingPlayground,
        concepts: concepts,
        onCompletion: { url in
            var updated = card
            store.saveImage(url, for: &updated)
        }
    )
}
```

## Starting from a reference photo (`sourceImage`)

```swift
@State private var sourceImage: Image?

var body: some View {
    Button("Create image") {
        showingPlayground = true
    }
    .imagePlaygroundSheet(
        isPresented: $showingPlayground,
        concepts: concepts,
        sourceImage: sourceImage,
        onCompletion: { url in
            var updated = card
            store.saveImage(url, for: &updated)
        }
    )
}
```

## Providing a visual suggestion with a PencilKit drawing

```swift
@State private var drawing = PKDrawing()

var concepts: [ImagePlaygroundConcept] {
    var result: [ImagePlaygroundConcept] = [
        .text(card.theme),
        .extracted(from: card.message)
    ]
    if !drawing.strokes.isEmpty {
        result.append(.drawing(drawing))
    }
    return result
}
```

## Adopt Image Playground in UIKit or AppKit

```swift
func presentViewController() {
    let viewController = ImagePlaygroundViewController()
    viewController.concepts = [
        .text(card.theme),
        .extracted(from: card.message)
    ]
    viewController.delegate = self
    present(viewController, animated: true)
}

func imagePlaygroundViewController(
    _ viewController: ImagePlaygroundViewController,
    didCreateImageAt url: URL
) {
    var updated = card
    store.saveImage(url, for: &updated)
    dismiss(animated: true)
}
```

## Size specification (`.closest(to:)`)

```swift
var options: ImagePlaygroundOptions {
    var options = ImagePlaygroundOptions()
    options.sizeSpecification = .closest(to: card.format.size)
    return options
}

var body: some View {
    Button("Create image") { showingPlayground = true }
        .imagePlaygroundSheet(
            isPresented: $showingPlayground,
            concepts: concepts,
            onCompletion: { url in
                var updated = card
                store.saveImage(url, for: &updated)
            }
        )
        .imagePlaygroundOptions(options)
}
```

## Default + allowed styles

```swift
var options: ImagePlaygroundOptions {
    var options = ImagePlaygroundOptions()
    options.sizeSpecification = .closest(to: card.format.size)
    return options
}

var body: some View {
    Button("Create image") { showingPlayground = true }
        .imagePlaygroundSheet(
            isPresented: $showingPlayground,
            concepts: concepts,
            onCompletion: { url in
                var updated = card
                store.saveImage(url, for: &updated)
            }
        )
        .imagePlaygroundOptions(options)
        .imagePlaygroundGenerationStyle(
            pendingStylePreset.defaultStyle,
            in: pendingStylePreset.allowedStyles
        )
}
```

## Offering the external provider style

```swift
var options: ImagePlaygroundOptions {
    var options = ImagePlaygroundOptions()
    options.sizeSpecification = .closest(to: card.format.size)
    return options
}

var body: some View {
    Button("Create image") { showingPlayground = true }
        .imagePlaygroundSheet(
            isPresented: $showingPlayground,
            concepts: concepts,
            onCompletion: { url in
                var updated = card
                store.saveImage(url, for: &updated)
            }
        )
        .imagePlaygroundOptions(options)
        .imagePlaygroundGenerationStyle(
            pendingStylePreset.defaultStyle,
            in: pendingStylePreset.allowedStyles + [.externalProvider]
        )
}
```

## Generating an adaptive image glyph (emoji style)

```swift
@State private var showingIconPlayground = false

var body: some View {
    Button("Create icon") {
        showingIconPlayground = true
    }
    Color.clear
        .imagePlaygroundSheet(
            isPresented: $showingIconPlayground,
            concepts: concepts,
            onCompletion: { _ in
            },
            onAdaptiveImageGlyphCreation: { glyph in
                var updatedCard = card
                store.saveIcon(glyph, for: &updatedCard)
            }
        )
        .imagePlaygroundGenerationStyle(.emoji, in: [.emoji])
}
```

## Disabling personalization

```swift
var options: ImagePlaygroundOptions {
    var options = ImagePlaygroundOptions()
    options.sizeSpecification = .closest(to: card.format.size)
    options.personalization = .disabled
    return options
}
```

## Availability gate (`supportsImageGeneration`)

```swift
@Environment(\.supportsImageGeneration)
private var supportsImageGeneration

var body: some View {
    NavigationLink(card.recipient) {
        if supportsImageGeneration {
            CardEditorView(card: card)
        } else {
            CardPickerView(card: card)
        }
    }
}
```

---

## Useful API facts surfaced by the code

- One entry point: `.imagePlaygroundSheet(isPresented:concepts:sourceImage:onCompletion:onCancellation:)`; an `onAdaptiveImageGlyphCreation:` variant for the emoji/Genmoji path.
- `ImagePlaygroundConcept` factories: `.text(_)`, `.extracted(from:title:)`, `.drawing(PKDrawing)`.
- `onCompletion` receives a **temporary file `URL`** in the app container — persist before the session ends.
- `ImagePlaygroundOptions`: `.sizeSpecification = .closest(to: CGSize)` and `.personalization = .disabled`.
- `imagePlaygroundGenerationStyle(_:in:)` sets the default style + the allowed list; a single-element list locks the picker.
- Styles named: `.illustration`, `.sketch`, `.animation`, `.emoji`, `.externalProvider`.
- UIKit/AppKit: `ImagePlaygroundViewController` with `concepts`/`options`/`delegate`; delegate method `imagePlaygroundViewController(_:didCreateImageAt:)`.
- Gate the whole experience with `@Environment(\.supportsImageGeneration)`.
