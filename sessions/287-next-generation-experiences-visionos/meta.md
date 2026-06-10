# Session 287 — Build next-generation experiences with visionOS 27

- **URL:** https://developer.apple.com/videos/play/wwdc2026/287/
- **Duration:** 32m
- **Speakers:** Norman (director, Vision Products Group)
- **Audio:** English. (Subtitle languages not enumerated in supplement.)

## Description

A platform-wide tour of what's new in visionOS 27. It recaps the visionOS scene
model (Shared Space — windows and volumes — vs. Immersive Spaces with Mixed,
Progressive, and Full immersion styles) and the now-three paths to build a
visionOS experience: bring existing iOS/iPadOS apps via compatibility or
recompilation; build natively for spatial computing (SwiftUI + RealityKit +
Reality Composer Pro, custom renderers via CompositorServices, or third-party
game engines); and a brand-new third path that brings existing macOS or PC
experiences to visionOS via Spatial Preview and Foveated Streaming. The session
then surveys RealityKit and Reality Composer Pro 3 advances, game-engine updates,
new interaction models (object tracking, custom spatial accessories), the
Apple Immersive Video media pipeline, and a round-up of smaller platform updates.

## Key topics

- **Hardware baseline** — latest Apple Vision Pro on M5; >4K pixels/eye; 90Hz hand tracking.
- **Three build paths** — iOS/iPadOS compatibility/recompile; native spatial (SwiftUI/RealityKit/CompositorServices or Unity/Unreal/Godot); new macOS/PC path via Spatial Preview + Foveated Streaming.
- **RealityKit (visionOS 27)** — physical space lighting (Projective Textures API), Cloth Simulation, Custom Reverb Mesh for acoustic ray tracing, Gaussian Splatting.
- **Reality Composer Pro 3** — AI-assisted Reality Composer Pro Assistant; Animation Graph (state machines); Navigation Meshes; Script Graph (node-based logic, live preview); upgraded Shader Graph (subsurface scattering, skin/eyes/hair, portals); Prototypes, Behavior Trees, Compute Graphs.
- **Game engines** — Unity PolySpatial (volumes + immersive via RealityKit or CompositorServices; spatial accessory plug-ins), Unreal (immersive + static foveation), Godot (CompositorServices + RealityKit + PHASE audio plug-ins on GitHub); custom engines via CompositorServices.
- **Spatial Preview** — new macOS 27 framework; preview spatial content (spatial photos, AIV, live USD 3D edits) on Vision Pro from a Mac with SharePlay; built into Preview app; no visionOS app required.
- **Foveated Streaming** — stream OpenXR content from PC/cloud to Vision Pro; eye-tracked foveated video compression; powered by NVIDIA CloudXR over Wi-Fi; launched in visionOS 26.4.
- **Object tracking** — high-frame-rate tracking, extended Create ML training, metric-space pose API; now on iOS via new ARKit API with platform-agnostic reference objects.
- **Custom spatial accessories** — build-your-own tracked hardware (LED constellation + IMU + Bluetooth); Game Controller framework; reference kits from DFRobot and MikroE.
- **Immersive media / AIV** — 90fps stereoscopic 180°, >100MP/frame; Immersive Media Support (IMS) framework gains camera presentation overrides, ImmersivePreviewRenderer, wide-aspect-ratio portals; static foveation sample; ASAF Production Suite updates.
- **Other updates** — wider/curved Safari windows; Web Environments on by default; redesigned Control Center; High Quality Capture (4K, no Mac); accessory widgets; previews of Siri, Iceland environment, Spatial Panoramas, Personal Environments, Freeform.

## Related sessions to fetch (referenced in this talk)

- [ ] Explore advances in RealityKit
- [ ] Discover the Spatial Preview framework
- [ ] Foveated Streaming (dedicated session)
- [ ] Explore enhancements to visionOS object tracking
- [ ] Build live production tools for Apple Immersive Video
- [ ] Learn about Apple Immersive Video technologies (WWDC25)
- [ ] Support immersive video playback in visionOS apps (WWDC25)
- [ ] Reality Composer Pro 3 sessions ("Check out these sessions" — IDs TBD)
- [ ] Object tracking API session (WWDC24)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Norman; platform momentum across consumer, enterprise, creative (YouTube, Valve/Steam Link, Resolution Games, Kia/Innoactive, Laminar/X-Plane). M5 hardware baseline. Session roadmap.
- **2:00 visionOS overview** — scene model recap: Shared Space (windows/volumes) and Immersive Spaces (Mixed/Progressive/Full). Three paths to build.
- **3:13 Paths to build a visionOS experience** — (1) iOS/iPadOS compatibility or recompile; (2) native spatial computing (native frameworks, game engines, or custom renderers); (3) new path for existing macOS/PC via Spatial Preview or Foveated Streaming.
- **6:39 RealityKit and Reality Composer Pro** — physical space lighting (Projective Textures), Cloth Simulation, Custom Reverb Mesh (acoustic ray tracing), Gaussian Splatting; Reality Composer Pro 3 refresh — Assistant, Animation Graph, Navigation Meshes, Script Graph, upgraded Shader Graph.
- **13:42 Third-party game engines** — Unity PolySpatial, Unreal (static foveation), Godot; new spatial controller, ARKit, and PHASE audio plug-ins on GitHub; custom rendering via CompositorServices.
- **15:47 Spatial Preview** — macOS framework to preview spatial content on Vision Pro from a Mac via Quick Look; SharePlay; live USD editing; built into Preview on macOS 27.
- **17:28 Foveated Streaming** — stream macOS/PC OpenXR experiences to Vision Pro with native spatial rendering; eye-tracked foveated compression; NVIDIA CloudXR over Wi-Fi.
- **20:36 Object tracking and spatial accessories** — high-frame-rate tracking, extended Create ML training, metric-space pose; ARKit object tracking on iOS; custom build-your-own spatial accessories with reference kits (DFRobot, MikroE).
- **25:32 Immersive media** — AIV pipeline; Immersive Media Support (IMS) framework; live production via SMTPE 2110; wide-aspect-ratio portals; static foveation for streamable AIV; ASAF Production Suite.
- **30:46 Other visionOS 27 updates** — wider Safari windows; Web Environments default-on; redesigned Control Center; High Quality Capture; accessory widgets; preview of Siri, Iceland, Spatial Panoramas, Personal Environments.
- **32:05 Next steps** — round-up across the platform and pointers to related deep-dive sessions.

## Code

See `code.md` — no Code tab for this session; the file lists the concrete APIs named in the transcript.
