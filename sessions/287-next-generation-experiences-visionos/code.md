# Code samples — Session 287

_No Code tab for this session._ This is a platform-overview keynote-style talk with
no code snippets. The APIs, frameworks, and tools named in the transcript are listed
below for reference.

## Spoken APIs, frameworks & tools

**Scene model**
- Shared Space — windows, volumes
- Immersive Space — Mixed / Progressive / Full immersion styles

**Build paths**
- App Store Connect compatibility flag (iOS/iPadOS app on Vision Pro)
- visionOS deployment target in Xcode (recompile path)
- SwiftUI, RealityKit, Reality Composer Pro (native spatial)
- CompositorServices framework (custom / third-party rendering engines)

**RealityKit (visionOS 27)**
- Physical space lighting
- Projective Textures API (textured spotlights)
- Cloth Simulation
- Custom Reverb Mesh (acoustic ray tracing for spatial audio)
- Gaussian Splatting

**Reality Composer Pro 3**
- Reality Composer Pro Assistant (AI 3D asset generation)
- Animation Graph (state machines)
- Navigation Meshes
- Script Graph (node-based logic, live preview on Vision Pro)
- Shader Graph (subsurface scattering; skin/eyes/hair; portals)
- Prototypes, Behavior Trees, Compute Graphs, custom Script Graph nodes

**Game engines**
- Unity PolySpatial (volumes + immersive; RealityKit or CompositorServices)
- Unreal Engine (immersive, static foveation)
- Godot (CompositorServices + RealityKit + PHASE audio plug-ins)
- Spatial accessory, ARKit, and PHASE audio plug-ins (Apple GitHub)

**Mac/PC paths**
- Spatial Preview framework (macOS 27; Quick Look; SharePlay; built into Preview)
- USD (live 3D editing in Spatial Preview)
- Foveated Streaming framework (OpenXR streaming; NVIDIA CloudXR; launched visionOS 26.4)

**Interaction**
- Object tracking API (visionOS) — high-frame-rate tracking; metric-space pose (no display corrections)
- ARKit object tracking API (now on iOS)
- Create ML — reference object training; extended training option; platform-agnostic reference objects
- Game Controller framework (spatial accessories)
- Spatial accessories: LED constellation + IMU + Bluetooth; buttons/touchpads/haptics
  - Reference hardware: DFRobot seeMote Cap, MikroE Spatial Anchor R1

**Immersive media**
- Apple Immersive Video (AIV) — 90fps stereoscopic 180°, >100MP/frame
- Immersive Media Support framework (IMS) — read/write AIV metadata
  - Set Camera Command Overrides (camera presentation override commands)
  - ImmersivePreviewRenderer API
  - Wide-aspect-ratio portals
- AVPlayerViewController (AVKit) / VideoPlayerComponent (RealityKit) — custom aspect ratios
- Static foveation (dual-track QuickTime sample for streamable AIV)
- SMPTE 2110 (live production)
- ASAF (Apple Spatial Audio Format) Production Suite — AAX plug-ins, Scene Compressor

**Other platform updates**
- Safari wider/curved windows; Web Environments (default on)
- Control Center redesign; High Quality Capture (4K, no Mac)
- Accessory widgets on visionOS
