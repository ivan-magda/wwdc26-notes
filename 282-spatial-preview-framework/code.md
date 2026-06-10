# Code samples — Session 282

Extracted from the Code tab. Snippets are grouped by the workflow they appear in.

## Start and update a Document Preview session

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

## Gallery view — swap files in one scene, observe session state, close

```swift
import SwiftUI
import SpatialPreview

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

## Open and share a USD stage with a USD Preview session

```swift
import SpatialPreview
import USDKit

let deviceObserver = ConnectedSpatialEndpointObserver()

var usdSession: USDPreviewSession?

func shareStage(to endpoint: SpatialPreviewEndpoint) async throws -> USDPreviewSession {
    let endpoint = try await deviceObserver.endpoint

    let stageURL = Bundle.main.url(forResource: "sampleScene", withExtension: "usdz")
    let stage = try USDStage.open(stageURL)
    usdSession = USDPreviewSession(stage: stage)

    try await usdSession?.start(endpoint: endpoint)
}
```

## Opt out of optimization and handle unshareable assets

```swift
import SpatialPreview

let endpoint = try await deviceObserver.endpoint
do {
    try await usdSession.start(endpoint: endpoint, parameters: .unmodified)
} catch USDPreviewSession.Error.assetUnshareable {
    // Handle Asset Unshareable error
}
```

## USD layout variants (`LayoutVariants.usda`)

```usd
#usda 1.0
over "furniture" (
    variantSets = "Layout"
    variants = { string Layout = "LayoutA" }
)
{
    variantSet "Layout" = {
        "LayoutA" {
            // Default furniture position and rotation
        }
        "LayoutB" {
            // Moves furniture prims to a different position and rotation
        }
        ...
    }
}
```

## Apply a layout variant from Swift

```swift
import SpatialPreview
import USDKit

func applyLayoutVariant(named layoutVariantName: String) throws {
    let prim = stage.prim(at: SdfPath("/root/furniture"))
    try prim.variantSets?.setSelection("Layout", variantName: layoutVariantName)
}
```

## Observe stage changes (e.g. annotations) via USD notices

```swift
import SpatialPreview
import USDKit

let observerToken: ObservationToken

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

## Annotation spec — `AppleTextAnnotation`

```usd
AppleTextAnnotation {
    // The textual representation of this annotation
    string text

    // The identifier for this specific author
    uniform string author

    // An identifier that is unique to your data tracking system
    uniform string identifier
}

/__documentAnnotationGroup__
```

## Mark a prim editable on visionOS — `spatialEditable` metadata

```usd
customData = {
    dictionary apple = {
        bool spatialEditable = 1
    }
}
```

## Session options and event listening

```swift
import SpatialPreview
import USDKit

session.start(endpoint: endpoint, options: [.annotations, .perObjectManipulation, .export])

func listenForEvents(session: USDPreviewSession) async {
    for await event in session.events {
        if case .timeChanged(let time) = event {
            playbackModel.timeCode = time
        } else if case .playbackStateChanged(let isPlaying) = event {
            playbackModel.playbackStateChanged(isPlaying)
        }
    }
}
```

## Observe sync progress with `ProgressReporter`

```swift
import SpatialPreview
import USDKit

@State private var sessionProgress: Double = 0

var body: some View {
    ...
    .task(id: usdSession.map { ObjectIdentifier($0) }) {
        guard let session = usdSession else { return }
        for await fraction in Observations({ session.progress.fractionCompleted }) {
            sessionProgress = fraction
        }
    }
    .overlay(alignment: .bottom) {
        ProgressView(value: sessionProgress)
            .padding()
    }
}
```

---

## Useful API facts surfaced by the code

- Two session types: `DocumentPreviewSession(name:contentType:)` and `USDPreviewSession(stage:)`.
- Endpoints come from `ConnectedSpatialEndpointObserver().endpoint` (active Mac Virtual Display) or `SpatialPreviewDevicePicker`.
- `DocumentPreviewSession`: `start(endpoint:)`, `updateContents(url:)` (reuses the scene), `close()`; `state.isInvalidated` signals the scene closed on visionOS.
- `USDPreviewSession`: `start(endpoint:)` or `start(endpoint:parameters: .unmodified)`; opting out of optimization can throw `USDPreviewSession.Error.assetUnshareable`.
- `start(endpoint:options:)` toggles features — `.annotations`, `.perObjectManipulation`, `.export` (all on by default).
- `session.events` yields `.timeChanged(_)` and `.playbackStateChanged(_)`; `session.progress.fractionCompleted` drives a loading bar.
- USD authoring hooks: `variantSets` / `setSelection(_:variantName:)`, `AppleTextAnnotation` under a `__documentAnnotationGroup__`, and `apple.spatialEditable = 1` custom metadata for gesture-movable prims.
- Content type `.aivu` is Apple Immersive Video.
