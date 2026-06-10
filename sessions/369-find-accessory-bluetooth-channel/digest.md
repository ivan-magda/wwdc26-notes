---
title: "Find your accessory with Bluetooth Channel Sounding — Full Digest"
session: WWDC26 · 369
url: https://developer.apple.com/videos/play/wwdc2026/369/
duration: 8m
speakers: Gretchen
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 369 — Find your accessory with Bluetooth Channel Sounding

## TL;DR

Bluetooth Channel Sounding is a new way to **measure** — not estimate — the distance
to a paired Bluetooth accessory on iOS. It's the answer for accessories that have only
a Bluetooth chipset (no Ultra Wideband). Three headline points:

1. **It measures distance for real.** Where RSSI only *estimates* proximity, Channel
   Sounding ranges by sending a tone from the iPhone (the *initiator*) to the accessory
   (the *reflector*) and measuring the phase change of the reflected tone across the
   channels of the 2.4GHz band. One full measurement cycle is a "procedure."
2. **Two API surfaces, two levels of result.** Use **Core Bluetooth** if you only need
   distance. Use **Nearby Interaction** (with camera assistance) to get distance *and*
   direction — the same delegate callbacks you already get with UWB.
3. **It has real hardware gates on both ends.** iPhone side: the **N1 chip**, iOS 27,
   foreground only. Accessory side: **Bluetooth 6.3**, inline PCT, mode-0 + mode-2
   phase-based ranging, and T_FCS ≥ 100µs.

The running example is a host cooking at a party with two Bluetooth thermometers — the
app reports a probe is "8 meters to my right," so the right one (the backyard smoker)
gets identified instead of being mixed up with the oven probe.

---

## 1. Overview — why Channel Sounding

- **The problem it solves:** telling near-identical accessories apart by *where* they
  are. The thermometer demo: a temperature alert fires, the app shows "8 meters to my
  right," so you know it's the smoker and not the oven.
- **RSSI vs. Channel Sounding:** RSSI (signal-strength) only lets you *estimate*
  distance; Channel Sounding lets you *measure* it. Reach for it "where your app could
  benefit from better accuracy."
- **Where it sits among ranging options:** for the *best* accuracy, add an **Ultra
  Wideband (UWB)** chipset and use Nearby Interaction (see "Explore Nearby Interaction
  with third-party accessories"). If the accessory has **only a Bluetooth chipset**,
  Channel Sounding is the best option.

### How it works

- The iPhone is the **initiator**, the accessory is the **reflector**.
- The iPhone sends a tone; the accessory reflects it back. The iPhone measures how the
  signal changes in transit — out and back.
- Repeating this **across the channels of the 2.4GHz band** and observing the rate of
  change of the reflected tones from one channel to the next yields a distance estimate.
- One complete measurement is called a **procedure**.

## 2. Core Bluetooth API — distance only

Prerequisite: the accessory is paired/set up via **AccessorySetupKit** and connected
through Core Bluetooth (see that documentation for the setup flow).

The flow is: check support → start the session → receive distances → cancel → handle
completion.

```swift
import CoreBluetooth

func isChannelSoundingSupported() -> BOOL {   // sic: should be Swift `Bool`
    guard centralManager.state == .poweredOn else { return }
    if #available(iOS 27.0, *) {
        return CBCentralManager.supportsFeatures(.channelSounding)
    }
}

func startChannelSounding(_ peripheral: CBPeripheral) {
    guard peripheral.isConnected else { return }
    if #available(iOS 27.0, *) {
        // Step 1: Create a CBChannelSoundingSessionConfiguration
        let config = CBChannelSoundingSessionConfiguration(role: .initiator)
        // Step 2: Start the channel sounding session
        peripheral.startChannelSoundingSession(config)
    }
}
```

- `CBCentralManager.supportsFeatures(.channelSounding)` checks the **local iOS device**
  supports it.
- After starting, **iOS repeatedly performs procedures**. Each completed procedure
  drives a delegate callback carrying the measured distance in **meters**.

```swift
// Receive distance results — called per completed procedure
func peripheral(_ peripheral: CBPeripheral,
                didReceive results: CBChannelSoundingProcedureResults?,
                error: Error?) {
    guard let results = results else { return }
    let distance = results.distance
    // Do something with distance
}

// End the session when you're done
func cancelChannelSounding(_ peripheral: CBPeripheral) {
    guard peripheral.isConnected else { return }
    if #available(iOS 27.0, *) {
        peripheral.cancelChannelSoundingSession(config)
    }
}

// Called once the session has fully ended
func peripheral(_ peripheral: CBPeripheral,
                didCompleteChannelSoundingSession error: Error?) {
    // Session is complete
}
```

## 3. Nearby Interaction API — distance + direction

Same prerequisite (AccessorySetupKit pairing + Core Bluetooth connection). Nearby
Interaction layers direction on top of the raw Channel Sounding measurements.

```swift
import CoreBluetooth
import NearbyInteraction

func startChannelSoundingThroughNearbyInteraction(_ peripheral: CBPeripheral) {
    if #available(iOS 27.0, *) {
        // Step 1: Check local device support
        guard NISession.deviceCapabilities.supportsBluetoothChannelSounding else { return }

        // Step 2: Build the accessory configuration from the CB peripheral identifier
        let config = NINearbyAccessoryConfiguration(
            bluetoothChannelSoundingIdentifier: peripheral.identifier,
            previousChannelSoundingIdentifier: nil)

        // Step 3: Camera assistance is REQUIRED to get direction
        if NISession.deviceCapabilities.supportsCameraAssistance {
            config.isCameraAssistanceEnabled = true
        }
    }
}
```

- Capability check is a **different** API than Core Bluetooth's:
  `NISession.deviceCapabilities.supportsBluetoothChannelSounding`.
- The config is keyed off the Core Bluetooth `peripheral.identifier`, passed as the
  `bluetoothChannelSoundingIdentifier`.
- **Direction requires camera assistance** (`isCameraAssistanceEnabled`), itself gated
  on `supportsCameraAssistance`.

```swift
func runChannelSoundingThroughNearbyInteraction(_ config: NINearbyAccessoryConfiguration) {
    let session = NISession()
    session.delegate = self
    session.run(config)
}

// Motion hint improves direction estimates
func updateAccessoryMotionState(_ isMoving: Bool) {
    let motionState: NIMotionActivityState = isMoving ? .moving : .stationary
    session.updateMotionState(motionState, forObjectWithToken: object.discoveryToken)
}

// Same callback shape as UWB
func session(_ session: NISession, didUpdate nearbyObjects: [NINearbyObject]) {
    guard let object = nearbyObjects.first else { return }
    if let distance = object.distance {
        // Do something with distance
    }
    if let direction = object.horizontalAngle {
        // Do something with horizontal angle
    }
}
```

- **Motion state** (`.moving` / `.stationary`) is a hint that improves direction:
  a wall-mounted tag is `.stationary`; something attached to a moving object is `.moving`.
- The delegate callback is **identical to UWB** — you receive `NINearbyObject` updates.
  Both **distance and direction are optional**; distance may be `nil` if a measurement
  failed.
- iOS **filters outliers and smooths** the results for you.

### Runtime behavior to design around

- **Foreground only in iOS 27.** Moving to the background **pauses** the session.
- iOS may **reduce measurement frequency** when other Bluetooth or Wi-Fi activity
  increases.
- Device requirement: **iPhones with the N1 chip.**

## 4. Hardware tips — building a compatible accessory

For an accessory to work with Channel Sounding on iOS:

- **Bluetooth 6.3** support.
- **Inline PCT** feature required.
- **Phase-based ranging** — chipset must support **mode-0 and mode-2** (per the
  Bluetooth spec).
- **T_FCS ≥ 100µs** — T_FCS is the interspace timing between tones.

## 5. Next steps

- Try the APIs with a compatible accessory; think about where measuring distance would
  improve how people use your app.
- Ask on the **Developer Forums** and file via **Feedback Assistant**.
