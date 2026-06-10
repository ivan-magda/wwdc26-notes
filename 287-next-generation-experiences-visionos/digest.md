---
title: "Build next-generation experiences with visionOS 27 — Full Digest"
session: WWDC26 · 287
url: https://developer.apple.com/videos/play/wwdc2026/287/
duration: 32m
speakers: Norman (Vision Products Group)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 287 — Build next-generation experiences with visionOS 27

## TL;DR

A breadth-first map of visionOS 27 for developers. Five things to take away:

1. **A new third build path.** Beyond (1) bringing iOS/iPadOS apps and (2) building natively for spatial computing, visionOS 27 adds a path to bring **existing macOS and PC experiences** to Apple Vision Pro — via **Spatial Preview** (Mac spatial content, no visionOS app needed) and **Foveated Streaming** (PC/cloud OpenXR streaming over NVIDIA CloudXR).
2. **RealityKit gets a fidelity jump** — physical space lighting (Projective Textures), real-time **Cloth Simulation**, **Custom Reverb Mesh** acoustic ray tracing, and **Gaussian Splatting** to scan real objects into scenes.
3. **Reality Composer Pro 3 is a major refresh** — an AI **Assistant** that generates placeable 3D assets, **Animation Graph** state machines, **Navigation Meshes**, **Script Graph** node-based logic with live preview, and an upgraded **Shader Graph**. The pitch: do more "without ever opening Xcode."
4. **New interaction models** — object tracking gains high-frame-rate and metric-space poses and **comes to iOS via ARKit**; and you can now **build your own custom spatial accessories** (LED constellation + IMU + Bluetooth) with reference kits from DFRobot and MikroE.
5. **Immersive media pipeline matures** — the **Immersive Media Support (IMS)** framework adds camera presentation overrides, an `ImmersivePreviewRenderer`, and wide-aspect-ratio portals; plus a static-foveation sample for streamable AIV and ASAF audio updates.

Hardware baseline this year: Apple Vision Pro on the **M5 chip**, **>4K pixels per eye**, **90Hz hand tracking**.

---

## 1. visionOS scene model & the three build paths

visionOS renders app content in two broad arrangements:

- **Shared Space** — your app runs as a **window** or a **volume**, coexisting with other apps across the infinite canvas.
- **Immersive Space** — your app runs exclusively and can place 2D/3D content anywhere in the field of view, with three immersion styles:
  - **Mixed** — anchor 3D objects in the real world (e.g. an object on a table).
  - **Progressive** — the viewer dials in how immersive it feels.
  - **Full** — a completely virtual world.

Three paths to ship:

1. **Existing iOS / iPadOS apps** — likely runs on Vision Pro today. Either flip the **compatibility** flag in App Store Connect, or **recompile** by adding visionOS as an Xcode deployment target. Lowest friction.
2. **Apps designed for spatial computing** — build natively with **SwiftUI + RealityKit + Reality Composer Pro**, bring your own renderer via **CompositorServices**, or use a third-party engine (**Unity, Unreal, Godot**).
3. **(New) Existing macOS / PC experiences** — extend a Mac app's spatial content via the **Spatial Preview** framework, or stream a PC/cloud 3D experience via **Foveated Streaming**. The example: a flight sim rendering a detailed cockpit natively in RealityKit while streaming a heavy landscape from a remote machine.

## 2. RealityKit (visionOS 27)

RealityKit is the native rendering engine. New in visionOS 27:

- **Physical space lighting** — blends virtual lighting with the real world. Powered by the new **Projective Textures API** (add textures to spotlights). Demo: a planetarium projector whose stars/nebulae conform to every surface. Enables stained-glass projections, underwater caustics, etc.
- **Cloth Simulation** — real-time, physically realistic fabric. Demos: a flowing dress on a walking mannequin; a bed cover drawn back with realistic weight and drape.
- **Custom Reverb Mesh** — acoustic ray tracing for spatial audio. Sound is absorbed/scattered by modeled materials (wood, metal, stone), so audio fills the space from every direction rather than just originating in front of you.
- **Gaussian Splatting** — scan a real object (e.g. a potted plant) and render it as a 3D Gaussian splat, capturing detail that's hard to model by hand.

Deep-dive: **"Explore advances in RealityKit."**

## 3. Reality Composer Pro 3

A major refresh, framed as faster, AI-powered, collaborative workflows — "without always needing to touch Xcode."

- **Reality Composer Pro Assistant** — AI inside the editor. Describe what you want; it generates 3D models with textures/materials, ready to place (demo: a dried-fruit assortment dropped into an existing bowl, plus generated candles on a table). Good for prototyping before final assets ship.
- **Animation Graph** — control transitions between animation states via a state machine (e.g. idle ↔ walking), visualized live in the editor.
- **Navigation Meshes** — auto-generate a nav mesh (shown in blue), then add jumps, ladders, obstacles so a character can navigate and avoid obstacles.
- **Script Graph** — node-based logic editor; nodes catch events (like taps) and drive behavior; real-time edits with live preview on Vision Pro, no Xcode. Example: Devs United Games animating an Aquascape fish whose playback adjusts to its swim speed.
- **Shader Graph upgrade** — exposes **subsurface scattering** (previously seen in last year's Amalthea environment), plus lifelike **skin, eyes, hair**, and **portal** looks.
- **More to explore** — Prototypes, Behavior Trees, Compute Graphs, custom Script Graph nodes.

## 4. Third-party game engines

- **Unity** — compatible since launch (requires Unity Pro). Windowed games render via RealityKit; immersive games render via RealityKit **or** CompositorServices. New plug-ins support **spatial accessories** like the PSVR 2 Sense controller. Example: LEGO Builder's Journey running in a volume.
- **Unreal Engine** — available in immersive mode; Polyarc brought *Glassbreakers* with **static foveation** for sharper visuals.
- **Godot** — runs on Vision Pro (e.g. *DogWalk* from Blender Studios); new support for rendering via **CompositorServices**, a **RealityKit** rendering plug-in, and a **PHASE audio** plug-in for spatial audio.
- Game-engine plug-ins are on Apple's **GitHub** page.
- **Custom engines** — connect a proprietary engine to the system with **CompositorServices** and render directly in an immersive space.

## 5. Spatial Preview (Mac → Vision Pro)

A new **macOS 27** framework to preview spatial content from a Mac directly on Apple Vision Pro — **no visionOS app required**.

- Built on **Quick Look**; preview/update **spatial photos** and **Apple Immersive Video**, and edit **3D content live using USD**.
- Move freely around 3D scenes, refine placement, adjust material overrides, and leave **annotations**; collaborate via **SharePlay** (side by side or across the globe).
- Built directly into **Preview on macOS 27**, so users get it out of the box; Preview also gains 3D editing tools that make 3D "as easy to work with as images and PDFs." Examples: Cinema 4D, SketchUp.
- Complements **Mac Virtual Display** (work on your Mac in-headset with privacy).

Deep-dive: **"Discover the Spatial Preview framework."**

## 6. Foveated Streaming (PC/cloud → Vision Pro)

Stream **OpenXR** content from an external device (PC or cloud) to Vision Pro with native-feeling immersion.

- visionOS sends **input** (hands, controller positions, microphone); the remote device streams **video/audio** with full-scale immersion, like a native app.
- **Foveated compression** — the stream is compressed based on where you're looking: high quality in focus, less bandwidth in the periphery, seamlessly.
- Powered by **NVIDIA CloudXR** — high quality, low latency, runs over **Wi-Fi** with no dongles/cables, from a local PC or a cloud instance.
- **Already shipped:** launched in **visionOS 26.4**. Showcases: X-Plane 12 (Laminar Research — ARKit understands your space/equipment, sim streamed from PC), iRacing (iRacing Connect matches a physical wheel to the virtual cockpit via ARKit), Innoactive bringing Autodesk VRED with ray tracing at 1:1 scale.
- Pairs with **SwiftUI** for building streaming apps; Apple says ~one day to start streaming an OpenXR app, ~one week to add visionOS-only features.

Deep-dive: **dedicated Foveated Streaming session.**

## 7. Object tracking & custom spatial accessories

**Object tracking** (introduced in visionOS 2.0) turns physical objects into virtual anchors: start from a **USDZ** model, train a **reference object** in **Create ML** on a Mac, pass it to the object-tracking API, and receive position/orientation updates.

New in visionOS 27:

- **High-frame-rate tracking** — more frequent pose updates as objects move.
- **Extended training option** in Create ML — better accuracy/robustness, especially for **hand-held** objects.
- **Metric-space pose API** — object pose without display corrections, unlocking **high-precision spatial measurement** (e.g. a medical probe for surgical-navigation training).
- **Object tracking on iOS** — a new **ARKit API** with the same functionality; reference objects are **platform-agnostic**, so one Create ML reference object works on both iOS and visionOS with the same quality. (Object tracking API basics: WWDC24 session.)

**Custom spatial accessories** — visionOS 26 shipped the first set (Logitech Muse, PSVR 2 Sense controller), connected via the **Game Controller framework** and tracked with **RealityKit or ARKit**. visionOS 27 lets you **build your own**:

- A spatial accessory is a board with a **constellation of LEDs** (visible to Vision Pro for tracking), an **IMU** (orientation/acceleration), and a **Bluetooth chip**; it can host buttons, touchpads, and haptics.
- You can turn nearly any object into one by installing those components.
- Reference hardware / dev kits coming later this year from **DFRobot** and **MikroE**. Examples: a 3D-printed flashlight with a **DFRobot seeMote Cap** casting a natural virtual beam; a **MikroE Spatial Anchor R1** inside a steering wheel anchoring a digital car.
- Tracked at the **display's native refresh rate** with low latency; robust under occlusion and low light.

Deep-dive: **"Explore enhancements to visionOS object tracking."**

## 8. Immersive media & Apple Immersive Video (AIV)

**Apple Immersive Video (AIV)** is the highest-fidelity immersive video on visionOS: stereoscopic **180°**, very large FOV, fully immersive audio.

- Captured/streamed at **90fps**, near-human acuity at **>100 megapixels per frame** (≈ ten billion pixels/second). Real-world stereoscopic scale preserved via **metadata-driven lens calibration**. Supports both **video-on-demand** and **live broadcast**.
- **Immersive Media Support (IMS)** framework reads/writes rich AIV metadata and supports authoring/modifying content. New in visionOS 27 (plus recently added iOS support):
  - **Camera presentation override commands** (Set Camera Command Overrides) — change camera params in real time during live/complex productions.
  - **`ImmersivePreviewRenderer`** — real-time AIV preview on Vision Pro from a Mac during editorial/live production.
  - **Wide-aspect-ratio portals** — keep a very wide portal when switching from full immersion to portal mode. Set custom aspect ratios via **`AVPlayerViewController`** (AVKit) or **`VideoPlayerComponent`** (RealityKit).
- **Static foveation sample** — a smooth static-foveation function applied before encoding delivers high-acuity AIV in a streamable frame size (full-res stereo 90fps AIV is impractical to stream; plain 4K downscaling loses too much density). Implemented in **dual-track QuickTime**.
- **ASAF (Apple Spatial Audio Format) Production Suite** — AAX plug-ins gain object positioning relative to a reference video, an ambisonics **Scene Compressor** plug-in, and heat-map/spatial-filtering improvements.
- Sample project and ASAF suite are on developer.apple.com. Live production with **SMPTE 2110** is covered in **"Build live production tools for Apple Immersive Video."** Foundations: WWDC25 "Learn about Apple Immersive Video technologies" and "Support immersive video playback in visionOS apps."

## 9. Other visionOS 27 updates

- **Spatial Web** — Safari windows can take a **wider aspect ratio** and **curve** to bring more content into comfortable view; **Web Environments** are now **enabled by default**, letting sites have app-like backgrounds (demo: a Severance environment).
- **Control Center redesign** — notifications, system status, controls, and environments in one place.
- **High Quality Capture** — record your apps in **4K** from inside Apple Vision Pro, **no Mac required**.
- **Accessory widgets** — smaller glanceable widgets that surface relevant info on Vision Pro.
- **Previewed / coming** — Siri enhancements, an all-new **Iceland** environment, **Spatial Panoramas**, **Personal Environments**, and **Freeform** updates.
