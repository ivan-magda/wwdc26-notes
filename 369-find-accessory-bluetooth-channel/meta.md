# Session 369 — Find your accessory with Bluetooth Channel Sounding

- **URL:** https://developer.apple.com/videos/play/wwdc2026/369/
- **Duration:** 8m
- **Speakers:** Gretchen (Core Bluetooth)

## Description

Discover how to find nearby Bluetooth accessories using Bluetooth Channel Sounding.
Learn how the initiator/reflector ranging procedure works across the 2.4GHz band,
how to measure distance to a paired accessory with the Core Bluetooth API, and how to
add direction with the Nearby Interaction API (plus camera assistance). The session
closes with the hardware requirements an accessory must meet to support Channel
Sounding on iOS.

## Key topics

- Channel Sounding vs. RSSI — actually *measure* distance instead of estimating it; alternative to Ultra Wideband (UWB) when an accessory only has a Bluetooth chipset
- How ranging works — iPhone (initiator) sends a tone, accessory (reflector) reflects it back; phase change measured across 2.4GHz channels = a "procedure"
- Core Bluetooth path (distance only) — `CBCentralManager.supportsFeatures(.channelSounding)`, `startChannelSoundingSession`, `didReceive results` delegate, `cancelChannelSoundingSession`
- Nearby Interaction path (distance + direction) — `NISession.deviceCapabilities.supportsBluetoothChannelSounding`, `NINearbyAccessoryConfiguration(bluetoothChannelSoundingIdentifier:)`, camera assistance for direction
- Motion state hint — `updateMotionState(.moving / .stationary)` improves direction estimates
- Runtime behavior — foreground-only in iOS 27, paused in background; iOS smooths/filters outliers; measurement frequency may drop under Bluetooth/Wi-Fi contention
- Device requirement — iPhones with the **N1 chip**
- Accessory hardware requirements — Bluetooth 6.3, inline PCT, mode-0 + mode-2 phase-based ranging, T_FCS ≥ 100µs

## Related sessions to fetch (referenced in this talk)

- [ ] Explore Nearby Interaction with third-party accessories

## Chapter summary (Summary tab)

- **0:00 Introduction** — Gretchen (Core Bluetooth); three parts: ideas + how it works, implementation (Core Bluetooth and Nearby Interaction), hardware tips, next steps.
- **0:50 Overview** — Party/cooking scenario: two Bluetooth thermometers, app reports "8 meters to my right." Channel Sounding measures distance (vs. RSSI estimates). UWB + Nearby Interaction is best accuracy; Channel Sounding is the option for Bluetooth-only accessories. Initiator (iPhone) sends tone, reflector (accessory) reflects; phase change across 2.4GHz channels yields distance — a "procedure."
- **3:17 Core Bluetooth API** — Pair/set up via AccessorySetupKit, connect via Core Bluetooth. Check `supportsFeatures`, call `startChannelSoundingSession` on a connected `CBPeripheral`, receive distance (meters) in `didReceive results`, end with `cancelChannelSoundingSession`.
- **4:34 Nearby Interaction API** — Check `supportsBluetoothChannelSounding`, build `NINearbyAccessoryConfiguration` from `peripheral.identifier`, enable camera assistance for direction, run an `NISession`. `updateMotionState` improves direction; `NINearbyObjects` updates carry distance + direction (both optional). Foreground-only in iOS 27; N1 chip required.
- **7:05 Hardware tips** — Accessory needs Bluetooth 6.3, inline PCT, mode-0 + mode-2 (phase-based ranging), T_FCS ≥ 100µs. Next steps: try the APIs, file feedback.

## Code

See `code.md` — 4 snippets extracted from the Code tab.
