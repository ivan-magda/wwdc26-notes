# Session 283 — Explore enhancements to visionOS object tracking

- **URL:** https://developer.apple.com/videos/play/wwdc2026/283/
- **Duration:** 14m
- **Speakers:** Nathan Kong (Strategic Partnerships Manager, visionOS team)

## Description

Learn the new object tracking and spatial accessory capabilities in visionOS 27.
Object tracking gains high frame rate tracking of objects in motion (including
handheld items), an extended training mode in Create ML, a metric-space pose API
for measurement use cases, and iOS support. Spatial accessories — electronic
devices with an LED constellation, IMU, and Bluetooth that Vision Pro tracks in
real time — expand from Apple's first-party set to fully custom third-party
hardware you can design, validate, and ship yourself.

## Key topics

- **High frame rate object tracking** — new `ReferenceObject.Configuration` with `highFrameRateTrackingEnabled` (per-object, not a training setting); better understanding of moving objects.
- **Extended training mode in Create ML** — standard vs. extended; extended boosts accuracy/robustness (especially handheld), takes significantly longer, recommended alongside high frame rate tracking; available in the Create ML app and via CLI for remote training.
- **Metric-space poses** — new ARKit Coordinate Space Correction API: `.rendered` (display-corrected, visually aligned) vs `.none` (true metric space, no corrections) for measurement tasks.
- **Object tracking on iOS** — same `.referenceobject` files work on both platforms; `ARWorldTrackingConfiguration` with `detectionObjects` (stationary, low frame rate) vs `trackingObjects` (moving, high frame rate); `ARObjectAnchor` via `ARSessionDelegate`.
- **Spatial accessories** — board with LED constellation + IMU + Bluetooth; optional buttons/touchpad/haptics; tracked up to full display rate, low latency, robust under occlusion and low light.
- **Building a custom accessory** — LED placement/design considerations; ARKit accessory tracking debug view (developer mode) to verify LEDs, IMU, timing; annotated USDZ → CLI → `.referenceaccessory` file; bundle + declare as exported (manufacturer) or imported (app developer) UTType in Info.plist.
- **Plug-and-play accessories** — off-the-shelf reference hardware/dev kits from DFRobot and MIKROE later in the year (e.g. seeMote Cap).
- **App integration** — `GCSpatialAccessory` discovery, `Accessory(device:)`, `AccessoryTrackingProvider`, new `updateAccessories(_:)` to hot-swap without interrupting the ARKit session.
- **Choosing an approach** — four tracking options: object tracking (precision/measurement), marker-based object tracking (no 3D model), spatial accessories (speed/low latency), custom accessory (interactivity via buttons/haptics).

## Related sessions to fetch (referenced in this talk)

- [ ] Explore object tracking for visionOS
- [ ] Explore spatial accessory input on visionOS

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of new visionOS object tracking enhancements: high-frame-rate tracking of handheld objects and the expansion of spatial accessories to third-party developers. Demos: medical probe measuring a spine model; Spatial Anchor mounted in a steering wheel aligning a full-scale digital car.
- **2:20 Object tracking** — Recap of the visionOS 2.0 object tracking API and what's new in visionOS 27: tracking objects in motion, extended training mode in Create ML, metric-space poses, and iOS support. Flashlight relighting demo (Physical Surroundings Light in RealityKit); 3D-printed marker alternative.
- **7:20 Spatial accessories** — Electronic devices with an LED constellation, IMU, and Bluetooth that Vision Pro tracks in real time. First-gen accessories (Logitech Muse, PSVR2 Sense) and the visionOS 27 expansion to custom third-party hardware. Benefits: high frequency/low latency, robust under occlusion and low light, buttons/haptics.
- **7:47 Creating a spatial accessory** — Design considerations (LED spread, rigid LED/IMU mounting, battery placement, ergonomics), the ARKit accessory tracking debug view, and generating a reference accessory bundle from an annotated USDZ.
- **11:48 Plug-and-play accessories** — Off-the-shelf reference hardware from DFRobot and MIKROE usable immediately for testing or integration without custom hardware development (seeMote Cap demo).
- **12:22 Implementing in your app** — Discover/connect via `GCSpatialAccessory` and `AccessoryTrackingProvider`; hot-swap accessories with `updateAccessories` without interrupting the ARKit session.
- **13:03 Next steps** — Choosing the right tracking approach among the four options; links to "Explore object tracking for visionOS" and "Explore spatial accessory input on visionOS."

## Code

See `code.md` — 5 snippets extracted from the Code tab.
