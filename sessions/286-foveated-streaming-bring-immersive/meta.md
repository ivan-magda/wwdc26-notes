# Session 286 — Use foveated streaming to bring immersive content to visionOS

- **URL:** https://developer.apple.com/videos/play/wwdc2026/286/
- **Duration:** 14m
- **Speakers:** Adrian (Apple engineer)

## Description

Foveated Streaming is a new framework, introduced in visionOS 26.4, that lets Apple
Vision Pro connect to an external device such as a PC and stream immersive OpenXR
content as video and audio. visionOS sends input (hands, controller positions,
microphone) to the endpoint and receives the rendered stream back. The system
compresses video based on where the wearer is looking — using Apple Vision Pro's eye
tracking to keep the focused region in higher detail — and ships NVIDIA CloudXR
streaming built in for low-latency Wi-Fi or cloud delivery. The session shows how to
set up the Windows streaming endpoint, build a visionOS receiver app, wire the OpenXR
client through the Foveated Streaming Protocol, measure performance, and enhance the
experience with SwiftUI, ARKit, and RealityKit.

## Key topics

- **Foveated Streaming** — new visionOS 26.4 framework; eye-tracking-driven video
  compression (focused region streamed at higher detail) handled entirely by the system.
- **Architecture** — three pieces: visionOS receiver app (`FoveatedStreaming` framework),
  the streaming endpoint (implements Apple's **Foveated Streaming Protocol**), and
  **NVIDIA CloudXR** (OpenXR runtime + Wi-Fi/cloud streaming, built into visionOS).
- **Shipping examples** — X-Plane 12 (Laminar Research), iRacing Connect, Innoactive +
  Autodesk VRED (Kia design review at 1:1 scale).
- **Streaming endpoint** — open-source Windows sample on Apple's GitHub with a reference
  Foveated Streaming Protocol implementation, an example OpenXR app, and CloudXR setup guides.
- **Receiver app** — `FoveatedStreamingSession` (session-based API); `connect()` shows an
  endpoint picker; QR-code (barcode) pairing scanned by looking; present stream via
  `ImmersiveSpace(foveatedStreaming:)`; add SwiftUI windows + progressive immersion.
- **Protocol** — lightweight TCP connection, separate from the stream; JSON-encoded
  request/acknowledge messages; Bonjour discovery; barcode carries a client token + a
  hash of the secure connection's certificate (both from CloudXR SDK); session-status
  reporting (sleep → pause → reconnect).
- **CloudXR integration** — auto-provides hand tracking (OpenXR extension), PSVR2 Sense
  controller pass-through; recommend a depth buffer + alpha channel for compositing.
- **Performance** — Foveated Streaming instrument in Xcode (bandwidth, pose latency, frame rate).
- **visionOS enhancements** — message channels (opaque data blobs both directions),
  ARKit anchoring + OpenXR↔ARKit coordinate conversion, RealityKit compositing with depth occlusion.

## Related sessions to fetch

- [ ] Get started with building apps for spatial computing (WWDC23)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of Foveated Streaming, the visionOS 26.4 framework
  that connects Apple Vision Pro to a PC to stream immersive OpenXR content with
  eye-tracking-based video optimization. Shipping examples: X-Plane 12, iRacing, VRED.
- **4:08 How Foveated Streaming works** — Architecture: the visionOS receiver app uses
  the `FoveatedStreaming` framework, the streaming endpoint implements the Foveated
  Streaming Protocol, and NVIDIA CloudXR handles the OpenXR runtime and Wi-Fi/cloud streaming.
- **4:46 Set up the streaming endpoint** — Use the open-source Windows sample on GitHub:
  a reference protocol implementation, an example OpenXR app, and CloudXR setup guides.
- **5:18 Create a visionOS receiver app** — Build with `FoveatedStreamingSession` to
  connect/pair; present streamed content in an `ImmersiveSpace`; enrich the UI with
  SwiftUI windows and progressive immersion.
- **8:02 Integrate with the streaming endpoint** — Configure the OpenXR client with the
  Foveated Streaming Protocol (auth + pairing) and integrate the CloudXR runtime for
  input data, hand tracking, depth buffers, and alpha-channel compositing.
- **11:28 Measure performance** — Use the Foveated Streaming instrument in Xcode to
  measure bandwidth, pose latency, and frame rate, and diagnose issues before shipping.
- **11:56 Enhance with visionOS features** — Message channels to exchange data between the
  visionOS and OpenXR apps, ARKit to anchor virtual content to the physical world, and
  RealityKit to composite native 3D objects alongside the streamed content.
- **13:56 Next steps** — Download the sample code on GitHub and developer.apple.com, set
  up your own receiver app, and integrate your OpenXR client with the protocol.

## Code

See `code.md` — 4 snippets extracted from the Code tab.
