---
title: "Use foveated streaming to bring immersive content to visionOS — Full Digest"
session: WWDC26 · 286
url: https://developer.apple.com/videos/play/wwdc2026/286/
duration: 14m
speakers: Adrian (Apple)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 286 — Use foveated streaming to bring immersive content to visionOS

## TL;DR

Foveated Streaming is a new visionOS **26.4** framework for bringing **OpenXR**
PC/cloud experiences onto Apple Vision Pro as a low-latency video+audio stream. Four
headline themes:

1. **Eye-tracking-driven streaming, done for you.** The system compresses the video
   stream based on where you're looking — the focused region stays high-detail — using
   Apple Vision Pro's eye tracking. visionOS sends input (hands, controllers, mic) up;
   the endpoint renders and streams the experience back. You don't implement any of the
   foveation yourself.
2. **NVIDIA CloudXR is built into visionOS.** It provides the OpenXR runtime on the
   Windows side and handles the streaming transport, so content can run cable-free over
   Wi-Fi from a local PC on your home network or from the cloud.
3. **Three-piece architecture.** A visionOS **receiver app** (`FoveatedStreaming`
   framework + `FoveatedStreamingSession`), a **streaming endpoint** that implements
   Apple's **Foveated Streaming Protocol** (pairing + auth) and uses the CloudXR OpenXR
   runtime. Apple ships open-source samples for both sides.
4. **Native visionOS on top.** Compose the stream with SwiftUI windows + progressive
   immersion, anchor it in the room with ARKit, and composite native RealityKit content
   (with depth occlusion). Apple's pitch: streaming in **one day**, visionOS-only
   enhancements in **one week**.

Already shipping with it: **X-Plane 12** (Laminar Research), **iRacing Connect**, and
**Innoactive + Autodesk VRED** (used by Kia for 1:1-scale design review).

---

## 1. What Foveated Streaming is (and the demos)

Some spatial apps need an external device like a PC, or are built on **OpenXR** rather
than native visionOS frameworks. Foveated Streaming is a new way to bring those onto
Apple Vision Pro: visionOS connects to the external device, **sends input data** (hands,
controller positions, microphone), and the device **streams the OpenXR content back as
video and audio**.

The name comes from how it optimizes the stream: it **compresses video based on where
you're looking**, using eye tracking to keep the focused region in higher detail. This
"sophisticated stream processing is built in to visionOS" — the heavy lifting is done
for you, and it happens fast enough that you don't notice.

Shipping examples called out in the talk:

- **X-Plane 12** (Laminar Research) — premium flight sim; the visionOS app uses ARKit to
  understand your space and a physical flight simulator, and streams the sim from a PC.
- **iRacing Connect** — sim racing; ARKit hand tracking matches a physical racing wheel
  to the virtual cockpit so you see your hands on the virtual wheel.
- **Innoactive + Autodesk VRED** — visualize massive 3D assets/simulations; Kia designers
  reviewing cars at 1:1 scale in detail.

## 2. How it works — the architecture

Block diagram, two sides:

**Streaming endpoint (your OpenXR app, Windows):**
- Implements **Apple's Foveated Streaming Protocol** (handles pairing).
- Uses the **OpenXR runtime provided by the NVIDIA CloudXR SDK**.

**visionOS (your receiver app):**
- Uses the **`FoveatedStreaming` framework** to connect to the endpoint.
- Integrates with **ARKit, SwiftUI, and RealityKit**.

Under the hood, **NVIDIA CloudXR streaming is built into visionOS**, giving a
high-performance protocol with minimal latency — cable-free over Wi-Fi, streaming from a
local PC over your home network or from the cloud.

## 3. Set up the streaming endpoint

Apple provides an open-source, end-to-end example on its **GitHub** page:

- A **Windows** sample with a **reference implementation of the Foveated Streaming Protocol**.
- An **example OpenXR application**.
- Guides for setting up the **NVIDIA CloudXR runtime**.

"Copy our reference implementation" — you can have the endpoint set up in an afternoon.

## 4. Build the visionOS receiver app

The receiver app welcomes people in, connects via the `FoveatedStreaming` framework, and
adds visionOS-only features. The API is **session-based**: create a
`FoveatedStreamingSession` and call `connect()`.

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

**What `connect()` does:** the framework automatically presents a **list of endpoints**.
Apple Vision Pro must **pair** before streaming — the endpoint shows a **QR code**
carrying pairing info, and the framework presents a scan UI; you scan it just by
**looking at it**. Once paired, the stream begins.

**Present the stream** with an `ImmersiveSpace`. Passing the session to the immersive
space includes the streamed content:

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

Because it's plain SwiftUI, you get the full toolbox — extra windows (including
volumetric windows), views inside the immersive space, native spatial gestures, and the
visionOS look and feel. The sample adds a main window (pause/resume) and an in-space
widget to re-open it, and uses a **progressive immersion** style — a portal grounded in
the wearer's physical environment, which Apple calls a great fit for Foveated Streaming.

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

(New to SwiftUI? The speaker recommends "Get started with building apps for spatial
computing" from WWDC23.)

## 5. Integrate the streaming endpoint

Your OpenXR app does two things: implement the **Foveated Streaming Protocol** (auth +
pairing) and use the **CloudXR OpenXR runtime**.

### The Foveated Streaming Protocol

- A **lightweight, TCP-based** connection, established **separately from the streaming
  connection**.
- **Authenticates** the secure foveated stream and **communicates session state**
  between visionOS and the endpoint.
- Details are in an article on developer.apple.com; a reference implementation is on GitHub.

**Connection flow on `connect()`:** endpoints advertise on the local network via
**Bonjour** → someone selects one → visionOS establishes the connection → **pairing**
occurs → the stream begins and `connect()` returns.

**Barcode pairing messages** are **JSON-encoded** and use a **request-acknowledge**
pattern. visionOS requests a connection and, if unpaired, requests a pairing barcode.
The (also JSON-encoded) barcode contains **two things**: a **client token** and a **hash
of the secure connection's certificate** — both provided by the CloudXR SDK.

**Session status matters.** visionOS keeps the endpoint updated; once the endpoint
reports content is ready, streaming begins. When you take the device off it sleeps —
just before, visionOS tells the endpoint the session is **paused**, and while asleep
**all connections are severed**. Keep the endpoint available so the wearer can reconnect
when they put the device back on.

### NVIDIA CloudXR integration

- CloudXR provides the **OpenXR runtime for Windows**; your OpenXR app connects to it
  automatically and CloudXR handles the streaming details.
- **Input data is provided automatically**: hand tracking (via the OpenXR hand-tracking
  extension) and **PlayStation VR2 Sense controller** pass-through.
- Recommended for best results: supply a **depth buffer**, and provide an **alpha
  channel** to mix your content with the wearer's surroundings.

## 6. Measure performance

Apple ships a **Foveated Streaming instrument in Xcode** that reports the stream's
**bandwidth, pose latency, frame rate, and more** — use it to diagnose issues with the
streamed content before shipping. (See the article on developer.apple.com.)

## 7. Enhance with visionOS-only features

The experience is two apps — the visionOS **receiver** and the endpoint **host**. They
talk via the **message channels API**.

**Message channels:**
- On visionOS, an API on `FoveatedStreamingSession`; on the endpoint, an OpenXR
  extension from CloudXR.
- Messages are **completely opaque data blobs** — send whatever you want.
- Example: a SwiftUI level-picker sends a load command to the OpenXR app, which reports
  load progress back. Or the visionOS app sends ARKit data to align the scene in the
  wearer's real space.

**ARKit anchoring** — X-Plane 12 uses ARKit to find a physical flight simulator's
location and message channels to sync it to the PC so real and virtual are aligned.
`FoveatedStreamingSession` offers an API to **convert between OpenXR and ARKit coordinate
frames**. Controller and hand tracking are already built in; use the alpha channel to
blend content with the environment.

**RealityKit compositing** — add a `RealityView` to your `ImmersiveSpace` and native
RealityKit content composes with the stream in the same render pass. If you supply
**depth** to the OpenXR scene, streamed and native content even **occlude each other**.

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

## Next steps

Download the sample code from GitHub and developer.apple.com, set up your own receiver
app, and integrate your OpenXR client with the Foveated Streaming Protocol.
