---
title: "Explore enhancements to visionOS object tracking — Full Digest"
session: WWDC26 · 283
url: https://developer.apple.com/videos/play/wwdc2026/283/
duration: 14m
speakers: Nathan Kong
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 283 — Explore enhancements to visionOS object tracking

## TL;DR

visionOS 27 turns "object tracking" into a spectrum of four physical-object tracking
strategies, and lets developers build their own tracked hardware for the first time.

1. **Object tracking grows up** — high frame rate tracking for objects *in motion*
   (handheld items, not just stationary props), a new **extended training mode** in
   Create ML for accuracy/robustness, and a **metric-space pose API** so you can
   actually *measure* with tracked objects instead of just decorating them.
2. **It leaves the headset** — object tracking comes to **iOS 27**, and the same
   trained `.referenceobject` files work on both iOS and visionOS unchanged.
3. **Anyone can build a spatial accessory** — the LED-constellation + IMU + Bluetooth
   devices Apple shipped first-party (Logitech Muse, PSVR2 Sense) are now an open
   hardware category. Apple gives you a design spec, a debug view, a USDZ → CLI →
   `.referenceaccessory` pipeline, and plug-and-play reference hardware from DFRobot
   and MIKROE.
4. **Four approaches, one decision** — object tracking (precision/measurement),
   marker-based tracking (no 3D model), spatial accessories (speed/low latency), and
   custom accessories (buttons/haptics) trade accuracy, latency, and interactivity.

Recurring demos: a medical probe measuring vertebrae on a spine model, a flashlight
that relights the room via RealityKit, a Spatial Anchor mounted in a steering wheel
aligning a full-scale digital car, and the WWDC24 globe returning on iOS.

---

## 1. What object tracking is (recap)

Introduced in **visionOS 2.0**: turn a real-world object into a virtual anchor. You
provide a **USDZ model**, train a **reference object** via machine learning in
**Create ML** on the Mac, and pass it to ARKit; your app then gets the object's
position and orientation to anchor spatial content.

## 2. High frame rate tracking

New in visionOS 27: track objects **in motion**, giving the app a better
understanding of an object's position as it moves through space. Enabled per object
through a new **`ReferenceObject.Configuration`** in ARKit on visionOS — set
`highFrameRateTrackingEnabled` before creating the session, then pass the config when
loading the object. It is **not a training setting**, so it applies to any reference
object based on the app's needs.

```swift
var configuration = ReferenceObject.Configuration()
configuration.highFrameRateTrackingEnabled = true

let refObjURL = Bundle.main.url(forResource: "flashlight", withExtension: ".referenceobject")
let refObject = try? await ReferenceObject(from: refObjURL!, configuration: configuration)
```

## 3. Extended training mode (Create ML)

When training a reference object you now choose **standard** or **extended** mode.
Extended increases tracking accuracy and robustness — particularly for **handheld**
objects — and is recommended *together with* high frame rate tracking. Caveat: it
**takes significantly longer** than standard. The setting lives in the Object
Tracking template in the Create ML app, just below the viewing angle settings;
everything else about training is unchanged. You can also set it from the CLI, which
lets you run training on a remote machine.

```bash
% xrun createml objecttracker --source flashlight.usdz --output flashlight.referenceobject --training-mode extended --all-angles
```

> Note: the Code tab shows the command as `xrun createml …`. This reads like a typo
> for `xcrun` — see Open Questions.

## 4. Metric-space poses (Coordinate Space Correction API)

By default, object anchor transforms are **slightly altered to match the displayed
camera images** so virtual content stays visually aligned in the mixed immersion
style. That display correction reduces accuracy in absolute world coordinates — a
problem for measuring tasks.

visionOS 27 adds the **ARKit Coordinate Space Correction API** with two options when
querying a tracked object's pose:

- **`.rendered`** — pose *with* display corrections, keeps virtual content visually
  aligned with the real object.
- **`.none`** — pose in true **metric space**, no corrections — for measuring the
  distance between tracked objects or where an object truly sits in physical space
  (the medical-probe-on-a-spine demo).

```swift
let renderingPose = myObjectAnchor.coordinateSpace(correction: .rendered)
let metricPose     = myObjectAnchor.coordinateSpace(correction: .none)
```

## 5. Object tracking on iOS

New in iOS 27: ARKit gains support for reference objects. **ML training is not
platform specific**, so a reference object trained once works in both iOS and
visionOS apps from the same `.referenceobject` files.

The iOS flow uses `ARWorldTrackingConfiguration` and a clean split:

- **`detectionObjects`** — objects that are mostly **stationary** (low frame rate).
- **`trackingObjects`** — **moving** objects (high frame rate).

You handle `ARObjectAnchor`s through the standard `ARSessionDelegate` lifecycle:
`didAdd` (attach an `AnchorEntity`), `didUpdate` (latest poses; toggle `isEnabled`
off `anchor.isTracked`), `didRemove` (clean up).

```swift
let configuration = ARWorldTrackingConfiguration()
configuration.detectionObjects = [stationaryObject]   // Low frame rate
configuration.trackingObjects = [movingObject]        // High frame rate
arView.session.run(configuration)
```

Deep-dive: **"Explore object tracking for visionOS."**

## 6. Spatial accessories — what they are

Introduced in **visionOS 26** (Logitech Muse, PSVR2 Sense controllers): electronic
devices that communicate with Apple Vision Pro, which tracks their **real-time
position and orientation**. A spatial accessory must contain a board with:

- a **constellation of LEDs** visible to Vision Pro for tracking,
- an **IMU** for orientation and acceleration,
- a **Bluetooth chip** to send signals to Vision Pro.

It can also host inputs (buttons, touchpad) and outputs (haptics). Any device with
those key components is compatible.

**Benefits over plain object tracking:**

- tracked at high frequency, **up to the full display rate**, with low latency
  (good for fast motion),
- robust even when **temporarily occluded**,
- works in **lower light**,
- buttons + haptics add interactivity and immersion.

In the demo, embedding the components into the flashlight makes the device itself a
spatial accessory: a quickly-waved beam follows smoothly thanks to the embedded IMU,
and a physical button toggles the digital light.

## 7. Building your own spatial accessory

**The big unlock in visionOS 27: anyone can build one.**

### Design considerations

- Spread the LEDs so they form a **distinct, unique pattern from many angles**.
- **Rigidly fix** both LEDs and IMU to the board for accurate tracking.
- Consider how users hold it — keep most LEDs **where hands won't cover them**;
  mind battery size/position for ergonomics.
- For **larger accessories used out of arm's reach**, tune LED count, size, and
  spacing so it tracks accurately from far away.
- Full spec: the **"Spatial Accessories" chapter of the Accessory Design
  Guidelines** for Apple Devices.

### Validate with the debug view

Connect over Bluetooth, then use the **ARKit accessory tracking debug view** (in
Settings, device in developer mode) to:

1. **Verify LEDs** via the headset's IR camera — bright, distinct, synchronized.
2. **Validate the IMU** with live frequency / latency / per-axis metrics (scale,
   alignment, motion response).
3. **Debug timing** between accessory and headset using the device's IR
   illuminators as a sync reference.

### Generate the reference accessory bundle

Train the accessory with the **Create ML bundle**, which combines the device's
physical appearance and LED locations into a **`.referenceaccessory`** file:

1. Create a **USDZ** with a photorealistic 3D model **annotated with IMU and LED
   positions**.
2. Run the **CLI** on that annotated USDZ to generate the `.referenceaccessory`.
3. Bundle the file in your app and declare a UTType in **Info.plist**:
   - **Manufacturer** → **exported** type → registers the accessory **system-wide**
     so any Vision Pro app can use it.
   - **App developer using a third-party accessory** → **imported** type → your app
     works independently.

## 8. Plug-and-play accessories

Before building from scratch, you can develop against off-the-shelf reference
hardware and dev kits from **DFRobot** and **MIKROE**, shipping later this year.
Usable immediately for testing or production integration. Demo: mounting a
**seeMote Cap** to the flashlight drives the same relighting experience through the
Spatial Accessories API.

## 9. Connecting an accessory in your app

Discover accessories with the new **`GCSpatialAccessory`** class — works with any
device that has a `.referenceaccessory` bundle. `Accessory(device:)` lets ARKit
resolve it automatically, then you run an **`AccessoryTrackingProvider`** as before.
New **`updateAccessories(_:)`** swaps accessories **while the session runs**, with no
tracking interruption (hot-swap).

```swift
if let device = GCSpatialAccessory.spatialAccessories.first {
    let accessory = try await Accessory(device: device)
    let provider = AccessoryTrackingProvider(accessories: [accessory])
    try await arkitSession.run([provider])
}

try await provider.updateAccessories([newAccessory])
```

Deep-dive: **"Explore spatial accessory input on visionOS."**

## 10. Choosing an approach (the four options)

| Approach | Best for | Notes |
| --- | --- | --- |
| **Object tracking** | Accurate, precise tracking; **measurement** | Needs a photorealistic 3D model; metric-space API |
| **Marker-based object tracking** | When you have **no 3D model** | 3D-print a marker, mount it, train on the marker |
| **Spatial accessories** | **Fast-moving** objects | Higher refresh rate + lower latency than object tracking |
| **Custom accessory** | Maximum **interactivity/immersion** | Add custom buttons + haptics |
