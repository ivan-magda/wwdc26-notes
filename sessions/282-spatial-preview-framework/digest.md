---
title: "Discover the Spatial Preview framework — Full Digest"
session: WWDC26 · 282
url: https://developer.apple.com/videos/play/wwdc2026/282/
duration: 15m
speakers: Quincy German
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 282 — Discover the Spatial Preview framework

## TL;DR

**Spatial Preview** is a new macOS / visionOS 27 framework that lets a Mac app push
content onto a nearby Vision Pro — over Mac Virtual Display — and have Vision Pro render
it spatially with **zero code on the visionOS side**. It powers the new Preview app for
Mac and is exposed as a public API. Headline themes:

1. **Three primitives, then done.** Pick a `SpatialPreviewEndpoint`, create a preview
   session, call `start`. Quick Look opens on Vision Pro and shows your content. "Only
   a few lines of code."
2. **Two session types.** `DocumentPreviewSession` for files (Apple Immersive Video
   frames, spatial photos, PDFs, images) and `USDPreviewSession` for live, editable 3D
   scenes backed by a USDKit stage.
3. **Live, bidirectional 3D editing.** A live USD stage replicates edits both ways:
   change furniture layout, drop annotations, or move objects on either device and the
   other follows — using regular USDKit APIs.
4. **Batteries-included asset review.** Camera viewpoints, material overrides
   (wireframe), layout variants, annotations, per-object manipulation, automatic
   performance optimization, playback events, sync-progress reporting — and **SharePlay
   collaboration built in** on visionOS.

Real-world adopters named: **Cinema 4D** and **SketchUp**.

---

## 1. What Spatial Preview is

Mac Virtual Display already lets you drive your Mac through a virtual screen on Vision
Pro. Spatial Preview goes a step further: it extends *content* — not just a flat screen
— from the Mac into the space around you. The new Preview app for Mac is the flagship
adopter (3D content editing, photorealistic rendering, camera viewpoints, and spatial
media output like Apple Immersive Video frames and spatial photos), but the same
capability is a framework you can fold into new or existing macOS apps.

The promise the talk keeps returning to: **no visionOS code required.** Quick Look on
Vision Pro receives whatever your Mac app sends and provides the immersive viewer,
camera navigation, and editing affordances for free.

## 2. The three components

1. **Select an endpoint** pointing at a target device.
   - If Mac Virtual Display is already active, reuse that connected device.
   - Otherwise present a device picker to choose any nearby Vision Pro on the same
     iCloud account.
2. **Create a preview session** for the content. Two kinds:
   - **Document preview session** — spatial photos, videos, PDFs, standard images.
   - **USD preview session** — 3D content.
3. **Start the session.** Quick Look launches on Vision Pro and the content appears.

## 3. Document Preview — send and update files

The walkthrough: take a still from an Apple Immersive Video generated in the Mac Preview
app, grab the Mac Virtual Display endpoint, start a document preview session, and hand
the image URL to the session so it shows in Quick Look.

```swift
import SwiftUI
import SpatialPreview

let deviceObserver = ConnectedSpatialEndpointObserver()

let previewSession = DocumentPreviewSession(name: "Immersive.aivu", contentType: .aivu)

func startPreview(contentURL: URL, endpoint: SpatialPreviewEndpoint) async throws {
    let endpoint = try await deviceObserver.endpoint
    try await previewSession.start(endpoint: endpoint)
    try await previewSession.updateContents(url: contentURL)
}
```

Because Mac Virtual Display may not be active, present `SpatialPreviewDevicePicker` from
a SwiftUI sheet to let the user pick a device, then start the session with that endpoint:

```swift
@State var showDevicePicker: Bool = false

var body: some View {
    ...
    .sheet(isPresented: $showDevicePicker) {
        SpatialPreviewDevicePicker(isPresented: $showDevicePicker) { endpoint in
            showDevicePicker = false
            Task {
                try await startPreview(filename: filename, endpoint: endpoint)
            }
        }
    }
}
```

### Galleries: reuse one scene with `updateContents`

Key distinction: **`updateContents(url:)` swaps the file inside the existing scene**,
while making a new session and calling `start` launches a *new* scene. For a gallery of
immersive architectural renderings you want the former — a row of buttons, each calling
`updateContents` to swap the displayed frame live. A `.task` observes session state so
that if someone closes the scene on Vision OS you learn the session was invalidated and
can tear down. Call `close()` when you're done; visionOS dismisses the scene.

```swift
ForEach(contentURLs, id: \.self) { url in
    Button {
        Task { try await previewSession?.updateContents(url: url) }
    }
}
.task(id: previewSession.map { ObjectIdentifier($0) }) {
    for await state in Observations({ session.state }) {
        if state.isInvalidated {
            previewSession = nil
            break
        }
    }
}

try await previewSession?.close()
```

Beyond Apple Immersive Video, document preview also handles spatial photos, PDFs,
standard images and files, and 3D content.

## 4. USD Preview — live 3D content

For 3D, the content is a **USDKit stage** rather than a file URL. The flow mirrors
document preview: choose an endpoint, open USD content into a stage, hand the stage to a
`USDPreviewSession`, and start.

```swift
import SpatialPreview
import USDKit

func shareStage(to endpoint: SpatialPreviewEndpoint) async throws -> USDPreviewSession {
    let endpoint = try await deviceObserver.endpoint

    let stageURL = Bundle.main.url(forResource: "sampleScene", withExtension: "usdz")
    let stage = try USDStage.open(stageURL)
    usdSession = USDPreviewSession(stage: stage)

    try await usdSession?.start(endpoint: endpoint)
}
```

On Vision Pro the scene appears in a **bounded volumetric view** first; the user can
rotate to inspect it, then go **immersive** to see it at full scale. Two review tools
ship for free with no extra Mac-app code:

- **Camera viewpoints** — selecting a camera in the scene jumps the viewer to it.
- **Material overrides** — e.g. switch to wireframe to inspect geometry.

(For USD background, the talk points to *Understand USD Fundamentals*, *Discover
USDKit*, and *What's new in OpenUSD*.)

### Automatic optimization

High-fidelity USD can be too heavy for Vision Pro to render, so Spatial Preview
**optimizes by default** before sharing: mesh decimation, texture downsampling, and —
if necessary — full scene reconstruction. A reconstructed scene is **not editable** but
can still be viewed and annotated. Opt out with `.unmodified`; if optimization is off, a
complex scene may not be shareable and `start` throws `assetUnshareable`:

```swift
do {
    try await usdSession.start(endpoint: endpoint, parameters: .unmodified)
} catch USDPreviewSession.Error.assetUnshareable {
    // Handle Asset Unshareable error
}
```

## 5. Live editing across devices

A **live USD stage replicates content both ways** between macOS and visionOS when you
call ordinary USDKit APIs — edit on the Mac and see it spatially on Vision Pro, or
capture edits made during a Vision Pro review. Quick terminology: a *stage* has one or
more *layers*; layers hold *prims* (a prim can be a transform, a mesh, etc.); *variant
sets* swap alternate data on a prim.

### Layout variants

The demo scene defines a `Layout` variant set on the furniture (`LayoutA`, `LayoutB`,
…). Selecting a variant moves furniture to new positions/rotations and syncs both ways —
toolbar buttons on the Mac, or the Quick Look menu on Vision Pro.

```usd
// LayoutVariants.usda
#usda 1.0
over "furniture" (
    variantSets = "Layout"
    variants = { string Layout = "LayoutA" }
)
{
    variantSet "Layout" = {
        "LayoutA" { /* Default furniture position and rotation */ }
        "LayoutB" { /* Moves furniture prims to a different position/rotation */ }
        ...
    }
}
```

```swift
func applyLayoutVariant(named layoutVariantName: String) throws {
    let prim = stage.prim(at: SdfPath("/root/furniture"))
    try prim.variantSets?.setSelection("Layout", variantName: layoutVariantName)
}
```

### Observing changes from visionOS

Stage changes are synchronized automatically; observe them with **standard USD
notices**. Subscribe to `ObjectsDidChange`, walk the resynced paths, and react to
annotation prims to update your UI:

```swift
observerToken = stage.addObserver(for: UsdStage.ObjectsDidChange.self) { notice in
    for path in notice.resyncedPaths {
        let prim = notice.stage.prim(at: path)
        guard prim.isValid else { continue }
        if prim.isAnnotation {
            // Handle annotation change
            break
        }
    }
}
```

### Annotations

A text annotation carries `text`, `author`, and a unique `identifier`. Annotations
appear on visionOS as long as they're children of a prim designated as a **document
annotation group**.

```usd
AppleTextAnnotation {
    string text                  // the annotation text
    uniform string author        // identifier for this author
    uniform string identifier    // unique to your data-tracking system
}

/__documentAnnotationGroup__
```

### Object manipulation

To let a prim be moved by **gestures** on visionOS, set the `spatialEditable` custom
metadata (also settable via Preview on macOS). In the demo, the speaker drags a chair
"out the window" on Vision Pro and the move shows up on the Mac.

```usd
customData = {
    dictionary apple = {
        bool spatialEditable = 1
    }
}
```

## 6. Session options, events, and progress

**Feature options** are set on `start`. By default `.annotations`,
`.perObjectManipulation`, and `.export` are all enabled; pass a narrower set to lock
features down.

```swift
session.start(endpoint: endpoint, options: [.annotations, .perObjectManipulation, .export])
```

**Events** — subscribe to non-USD events like animation playback through
`session.events` (e.g. `.timeChanged`, `.playbackStateChanged`). In the demo, hooking
these up lets a play button animate a hummingbird outside the window in both apps at
once.

```swift
for await event in session.events {
    if case .timeChanged(let time) = event {
        playbackModel.timeCode = time
    } else if case .playbackStateChanged(let isPlaying) = event {
        playbackModel.playbackStateChanged(isPlaying)
    }
}
```

**Progress** — monitor sync with `ProgressReporter` / `session.progress.fractionCompleted`
to show a loading bar on the Mac while large data streams to Vision Pro.

```swift
.task(id: usdSession.map { ObjectIdentifier($0) }) {
    guard let session = usdSession else { return }
    for await fraction in Observations({ session.progress.fractionCompleted }) {
        sessionProgress = fraction
    }
}
.overlay(alignment: .bottom) {
    ProgressView(value: sessionProgress).padding()
}
```

## 7. SharePlay collaboration

SharePlay support is **built in on visionOS**: collaborators join the same live session
to review and edit spatial content simultaneously, with every participant's view
updating immediately — "eliminating the back and forth of traditional asset review."

## 8. Getting started

- Use the **USDKit Swift APIs**; Spatial Preview is built to work seamlessly with them.
- If your Mac app already has its own USD installation, set up **bridging** to transfer
  edits between it and USDKit.
- Adopt the session type you need (document vs USD), layer in live editing, then turn on
  the asset-review tools (material overrides, camera viewpoints, object manipulation,
  variants, annotations) and SharePlay.
