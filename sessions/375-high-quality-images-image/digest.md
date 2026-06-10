---
title: "Create high quality images using Image Playground — Full Digest"
session: WWDC26 · 375
url: https://developer.apple.com/videos/play/wwdc2026/375/
duration: 14m
speakers: Antonio
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 375 — Create high quality images using Image Playground

## TL;DR

The `ImagePlayground` framework gets a major upgrade: the image-creation model is
rebuilt to produce **high-quality, photorealistic, "true-to-life"** results in
virtually any style, and **all generation now runs on Private Cloud Compute** instead
of on-device. Headline points:

1. **One modifier to adopt** — `.imagePlaygroundSheet(isPresented:…)`. No SDK init,
   no API keys, no server endpoints, no usage UI. You get the same sheet, styles, and
   quality as Apple's own Image Playground app.
2. **PCC, not on-device** — generation moved to Private Cloud Compute for quality and
   privacy. The system manages usage limits per user (increased access with most
   iCloud+ plans). As a developer you provision nothing.
3. **`ImageCreator` is deprecated** — the old non-UI generation API is gone; the
   sheet-based framework API is the path forward.
4. **Seed it with what only your app knows** — `ImagePlaygroundConcept.text` /
   `.extracted(from:)` / `.drawing(PKDrawing)`, plus a `sourceImage:`, prime the sheet
   so results feel made for one specific card / person / message.
5. **Configurable, then graceful** — `ImagePlaygroundOptions` for size + personalization,
   `imagePlaygroundGenerationStyle(_:in:)` for styles (incl. an opt-in external
   provider like ChatGPT and an emoji/Genmoji path that returns an adaptive image
   glyph), and `@Environment(\.supportsImageGeneration)` to branch supported vs.
   unsupported devices.

The running demo is **Postcards**, a greeting-card creator where Image Playground
generates the artwork on the front of each card.

---

## 1. Capabilities — what the model can create

- **Text to image** — be specific ("a birthday celebration with dogs, balloons and
  confetti") or open-ended ("celebration"); the model fills in the rest.
- **People** — including **multiple people in one scene**. With personalization
  enabled, users pull someone in from their Photos library or describe an appearance
  in text.
- **Styles** — none (let the model choose), text-described (e.g. "oil painting"), or
  presets: **Animation** (playful), **Illustration** (polished/editorial), **Sketch**
  (hand-drawn), **Genmoji** (expressive emoji-scale characters embeddable in text).
- **Sizes & aspect ratios** — landscape (banner), portrait (full-screen iPhone),
  square (thumbnail). The model snaps to the **closest supported resolution** for the
  size you request.

### Private Cloud Compute + the deprecation

- Everything runs on **Private Cloud Compute**, Apple's privacy-preserving cloud.
  Data is never stored or shared, even with Apple. (Deep dive: *"Build with the new
  Apple Foundation Model on Private Cloud Compute."*)
- Because it relies on powerful **server** models, there's a **usage limit** the system
  manages on behalf of users; increased access comes with most **iCloud+** plans. You
  never build any usage-related UI and provision no infrastructure.
- Moving to PCC meant rethinking the API: **`ImageCreator` (the non-UI, in-code
  generation API) is deprecated.** Generation now flows through the new sheet-based
  framework API, with better quality, built-in privacy, and a familiar UX.

## 2. Adopt Image Playground — one modifier

Adoption is a single SwiftUI view modifier bound to a `@State` boolean. When the
binding flips true, the sheet appears; Image Playground owns the UI, the model
interaction, and the style picker.

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

The full signature:

```swift
func imagePlaygroundSheet(
    isPresented: Binding<Bool>,
    concepts: [ImagePlaygroundConcept] = [],
    sourceImage: Image? = nil,
    onCompletion: @escaping (URL) -> Void,
    onCancellation: (() -> Void)? = nil
) -> some View
```

- `onCompletion` hands back a **`URL` to a temporary file inside the app container** —
  save it elsewhere before the session ends, or you lose it.

### Seeding the sheet with context

`ImagePlaygroundConcept` lets you prime the sheet so it opens already relevant to the
current item rather than a blank prompt:

- `.text(_)` — a direct description (the demo passes `card.theme`, e.g. "cherry blossoms").
- `.extracted(from:title:)` — hands the system longer text (the card's message) and
  lets it pull out the most relevant ideas.
- `.drawing(PKDrawing)` — a PencilKit sketch treated as a **visual suggestion** that
  guides composition without locking it in.

```swift
var concepts: [ImagePlaygroundConcept] {
    [
        .text(card.theme),
        .extracted(from: card.message, title: card.theme),
    ]
}
```

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

You can also pass any SwiftUI `Image` to `sourceImage:` as **visual inspiration** —
a starting point the user can replace or refine, not a constraint. (Deep dive on
drawings: *"Read between the strokes with PencilKit."*)

### UIKit / AppKit

`ImagePlaygroundViewController` mirrors the SwiftUI experience: set `concepts` and
`options` as properties before presenting, then implement the delegate.

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

## 3. Options — size, style, personalization

`ImagePlaygroundOptions` and `ImagePlaygroundStyle` configure the sheet — the same API
adapts whether you're making card artwork, a lock-screen wallpaper, a banner, or a
Genmoji icon.

### Size

Each Postcards format stores a `CGSize`; pass it to `.closest(to:)` and the system maps
to the closest supported aspect ratio + resolution. Because the size derives from the
card, it adapts automatically (landscape card → wide image, portrait card → tall image).

```swift
var options: ImagePlaygroundOptions {
    var options = ImagePlaygroundOptions()
    options.sizeSpecification = .closest(to: card.format.size)
    return options
}
// ...
.imagePlaygroundOptions(options)
```

### Styles

`imagePlaygroundGenerationStyle(_:in:)` takes a **default** style (what the picker opens
on) and an **allowed list** (which styles appear). A single-element allowed list locks
the picker to that one style.

```swift
.imagePlaygroundGenerationStyle(
    pendingStylePreset.defaultStyle,
    in: pendingStylePreset.allowedStyles
)
```

In the demo, each card carries a `StylePreset`: a classic card defaults to
`illustration` and allows only illustration + sketch; an expressive card defaults to
`animation` and allows illustration + emoji. The picker reflects whichever card is open.

### External provider (opt-in third-party)

`.externalProvider` surfaces whatever third-party provider the user has configured in
Settings (e.g. ChatGPT). Append it to the allowed list to offer it; if no provider is
configured the system handles setup, so you do no checking on your side. It can also be
the default style where that makes sense.

```swift
.imagePlaygroundGenerationStyle(
    pendingStylePreset.defaultStyle,
    in: pendingStylePreset.allowedStyles + [.externalProvider]
)
```

### Emoji / Genmoji → adaptive image glyph

When `.emoji` is active, the sheet fires a **separate** completion,
`onAdaptiveImageGlyphCreation`, and hands you an **`NSAdaptiveImageGlyph`** instead of a
URL. That glyph embeds inline with text just like an emoji — perfect for a card
thumbnail next to the recipient's name. (Deep dive: *"Bring expression to your app with
Genmoji."*)

```swift
Color.clear
    .imagePlaygroundSheet(
        isPresented: $showingIconPlayground,
        concepts: concepts,
        onCompletion: { _ in },
        onAdaptiveImageGlyphCreation: { glyph in
            var updatedCard = card
            store.saveIcon(glyph, for: &updatedCard)
        }
    )
    .imagePlaygroundGenerationStyle(.emoji, in: [.emoji])
```

### Personalization

Enabled by default — lets people include someone from their Photos library. If the
context doesn't call for it (e.g. a product-image generator), set
`options.personalization = .disabled`; the people picker and name detection disappear
from the sheet entirely.

```swift
options.personalization = .disabled
```

## 4. Availability — handle every device gracefully

Image Playground is available only on Apple Intelligence devices, in a growing set of
languages/regions, and when the user has image generation enabled in Settings. One
environment value rolls all of that into a `Bool`:

```swift
@Environment(\.supportsImageGeneration)
private var supportsImageGeneration

var body: some View {
    NavigationLink(card.recipient) {
        if supportsImageGeneration {
            CardEditorView(card: card)      // full Image Playground experience
        } else {
            CardPickerView(card: card)      // simple Photos-picker fallback
        }
    }
}
```

`supportsImageGeneration` is `true` only when capability **and** language/region **and**
the user's Settings toggle all line up. **No entitlement, no extra capability check, no
setup step** — just the environment value and a conditional.
