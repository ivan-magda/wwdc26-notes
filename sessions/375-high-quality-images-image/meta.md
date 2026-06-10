# Session 375 — Create high quality images using Image Playground

- **URL:** https://developer.apple.com/videos/play/wwdc2026/375/
- **Duration:** 14m
- **Speakers:** Antonio (Engineer, Image Playground team)

## Description

The ImagePlayground framework brings high-quality, true-to-life image creation into
your app on devices with Apple Intelligence support. This year the experience is
rebuilt around powerful image models running on Private Cloud Compute, capable of
photorealistic results in virtually any style. The session walks through what the
model can create, how to present the Image Playground sheet and seed it with context
from your app, how to configure size / style / personalization options, and how to
gracefully handle both supported and unsupported devices.

## Key topics

- New image-creation model: high quality, photorealistic, "true to life"; people (incl. multiple per scene), styles, and arbitrary aspect ratios
- All generation now runs on **Private Cloud Compute** — usage limits managed by the system, increased access with most iCloud+ plans, no server/infra for the developer
- **`ImageCreator` (non-UI API) is deprecated** — migrate to the sheet-based framework API
- Adoption is a single SwiftUI view modifier: `.imagePlaygroundSheet(isPresented:concepts:sourceImage:onCompletion:onCancellation:)`
- Completion hands back a **temporary file URL** inside the app container — persist it before the session ends
- Seeding context with `ImagePlaygroundConcept`: `.text`, `.extracted(from:title:)`, `.drawing(PKDrawing)`, plus `sourceImage:`
- UIKit/AppKit path: `ImagePlaygroundViewController` + delegate `imagePlaygroundViewController(_:didCreateImageAt:)`
- Options: `ImagePlaygroundOptions.sizeSpecification = .closest(to: CGSize)`; `.imagePlaygroundGenerationStyle(default, in: allowed)`; `personalization = .disabled`
- Styles: `.illustration`, `.sketch`, `.animation`, `.emoji`, plus `.externalProvider` (opt-in, surfaces a configured third-party provider like ChatGPT)
- Genmoji/emoji style produces an `NSAdaptiveImageGlyph` via `onAdaptiveImageGlyphCreation` (embeddable inline with text) instead of a URL
- Availability gate: `@Environment(\.supportsImageGeneration)` — true only when capability + language/region + user setting all line up; no entitlement required

## Related sessions to fetch (referenced in this talk)

- [ ] Build with the new Apple Foundation Model on Private Cloud Compute
- [ ] Read between the strokes with PencilKit
- [ ] Bring expression to your app with Genmoji

## Chapter summary (Summary tab)

- **0:00 Introduction** — Antonio (Image Playground team); the ImagePlayground framework brings high-quality, true-to-life image creation into your app on Apple Intelligence devices (iOS, iPadOS, macOS, visionOS).
- **2:03 Capabilities** — text-to-image (specific or open-ended); people incl. multiple per scene with personalization; styles (none / text-described / presets: animation, illustration, sketch, genmoji); multiple sizes and aspect ratios (model picks closest supported resolution); all on Private Cloud Compute; usage limits handled by the system; `ImageCreator` deprecated.
- **5:02 Adopt Image Playground** — `Postcards` demo app; one modifier `.imagePlaygroundSheet` driven by a `@State` bool; completion gives a temporary file URL; seed with `ImagePlaygroundConcept.text` / `.extracted` / `.drawing` and `sourceImage:`; UIKit/AppKit via `ImagePlaygroundViewController`.
- **8:29 Options** — `ImagePlaygroundOptions` size via `.closest(to:)`; `imagePlaygroundGenerationStyle(default, in: allowed)`; `externalProvider` opt-in style; `emoji` style returns `NSAdaptiveImageGlyph` via `onAdaptiveImageGlyphCreation`; `personalization = .disabled`.
- **12:15 Availability** — `@Environment(\.supportsImageGeneration)`; branch to full editor vs. a Photos-picker fallback; no entitlement or capability check needed.

## Code

See `code.md` — 12 snippets from the Code tab.
