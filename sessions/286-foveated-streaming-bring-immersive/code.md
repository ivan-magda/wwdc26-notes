# Code samples — Session 286

Extracted from the Code tab. Four snippets, all SwiftUI/visionOS receiver-side.

## Connect to a streaming endpoint

```swift
import SwiftUI
import FoveatedStreaming

struct ConnectView: View {
    let session: FoveatedStreamingSession

    var body: some View {
        Button("Connect") {
            Task {
                try await session.connect()
            }
        }
    }
}
```

## Display a Foveated Streaming session in an immersive space

```swift
import SwiftUI
import FoveatedStreaming

@main struct FoveatedStreamingSampleApp: App {
    private let session = FoveatedStreamingSession()

    var body: some SwiftUI.Scene {
        ImmersiveSpace(foveatedStreaming: session)
    }
}
```

## Compose SwiftUI content with Foveated Streaming

```swift
import SwiftUI
import FoveatedStreaming

@main struct FoveatedStreamingSampleApp: App {
    private let session = FoveatedStreamingSession()
    private let appModel = AppModel()

    var body: some SwiftUI.Scene {
        Window("Main", id: appModel.mainWindowId) {
            ContentView(session: session)
                .environment(appModel)
                .environment(session)
                // ...
        }

        ImmersiveSpace(foveatedStreaming: session) {
            SpatialContainer {
                ReopenMainWindowView().environment(appModel)
                TransformStreamWidgetView().environment(session)
            }
        }
    }
}
```

## Compose RealityKit content with Foveated Streaming

```swift
import SwiftUI
import RealityKit
import FoveatedStreaming

@main struct FoveatedStreamingSampleApp: App {
    private let session = FoveatedStreamingSession()
    private let appModel = AppModel()

    var body: some SwiftUI.Scene {
        ImmersiveSpace(foveatedStreaming: session) {
            RealityView { content in
                // ...
            }
        }
    }
}
```

---

## API facts surfaced by the code + transcript

- `FoveatedStreaming` framework; the entry point is `FoveatedStreamingSession`.
- `session.connect()` is `async throws` — it presents the endpoint picker, runs barcode
  pairing, and returns once the stream begins.
- New SwiftUI scene initializer: `ImmersiveSpace(foveatedStreaming: session)` — passing
  the session injects the streamed content into the immersive space. It also accepts a
  trailing view-builder closure so you can add your own SwiftUI/RealityKit content
  (e.g. `SpatialContainer`, `RealityView`) alongside the stream.
- `FoveatedStreamingSession` is `@Observable`-friendly — passed via `.environment(session)`.
- Spoken-but-not-in-Code-tab APIs: message channels API on `FoveatedStreamingSession`;
  an API to convert between OpenXR and ARKit coordinate frames; the immersive space is
  configured with a **progressive** immersion style.
