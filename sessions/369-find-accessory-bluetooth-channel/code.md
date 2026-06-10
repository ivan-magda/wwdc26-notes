# Code samples — Session 369

Extracted from the Code tab. Timestamps map to the transcript. Snippets are kept
close to as-extracted; a few obvious OCR/extraction artifacts are flagged inline and
in `digest.md` → Open Questions (e.g. `BOOL` should be Swift `Bool`,
`[NINearbyObjects]` should be `[NINearbyObject]`).

## 3:43 — Core Bluetooth: check support & start a session

```swift
import CoreBluetooth

func isChannelSoundingSupported() -> BOOL {   // sic: should be Swift `Bool`
    guard centralManager.state == .poweredOn else { return }
    if #available(iOS 27.0, *) {
        // Check current device supports Bluetooth Channel Sounding
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

## 4:09 — Core Bluetooth: receive distance, cancel, completion

```swift
import CoreBluetooth

// Receive distance results
func peripheral(_ peripheral: CBPeripheral,
                didReceive results: CBChannelSoundingProcedureResults?,
                error: Error?) {
    guard let results = results else { return }

    let distance = results.distance

    // Do something with distance
}

// Cancel a Channel Sounding session
func cancelChannelSounding(_ peripheral: CBPeripheral) {
    guard peripheral.isConnected else { return }
    if #available(iOS 27.0, *) {
        // Cancel the channel sounding session
        peripheral.cancelChannelSoundingSession(config)
    }
}

func peripheral(_ peripheral: CBPeripheral,
                didCompleteChannelSoundingSession error: Error?) {
    // Session is complete
}
```

## 5:00 — Nearby Interaction: configure a session

```swift
import CoreBluetooth
import NearbyInteraction

// Configure a Nearby Interaction Channel Sounding session
func startChannelSoundingThroughNearbyInteraction(_ peripheral: CBPeripheral) {
    if #available(iOS 27.0, *) {
        // Step 1: Check current device supports Bluetooth Channel Sounding
        guard NISession.deviceCapabilities.supportsBluetoothChannelSounding else { return }

        // Step 2: Create an NINearbyAccessoryConfiguration
        let config = NINearbyAccessoryConfiguration(
            bluetoothChannelSoundingIdentifier: peripheral.identifier,
            previousChannelSoundingIdentifier: nil)

        // Step 3: Enable camera assistance for direction support
        if NISession.deviceCapabilities.supportsCameraAssistance {
            config.isCameraAssistanceEnabled = true
        }
    }
}
```

## 5:20 — Nearby Interaction: run a session, motion state, updates

```swift
import CoreBluetooth
import NearbyInteraction

// Run a Nearby Interaction Channel Sounding session
func runChannelSoundingThroughNearbyInteraction(_ config: NINearbyAccessoryConfiguration) {
    // Create an NISession
    let session = NISession()
    session.delegate = self
    // Run the NISession with the accessory configuration
    session.run(config)
}

// Improve Nearby Interaction direction outputs
func updateAccessoryMotionState(_ isMoving: Bool) {
    let motionState: NIMotionActivityState = isMoving ? .moving : .stationary

    // Tell NISession about the accessory's motion state
    session.updateMotionState(motionState, forObjectWithToken: object.discoveryToken)
}

// Receive NISession updates
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

---

## Useful API facts surfaced by the code

- **Gating** — `CBCentralManager.supportsFeatures(.channelSounding)` (Core Bluetooth) and `NISession.deviceCapabilities.supportsBluetoothChannelSounding` (Nearby Interaction); both behind `#available(iOS 27.0, *)`.
- **Core Bluetooth session lifecycle** — `CBChannelSoundingSessionConfiguration(role: .initiator)` → `peripheral.startChannelSoundingSession(_)` → `peripheral(_:didReceive:error:)` delivers `CBChannelSoundingProcedureResults.distance` → `peripheral.cancelChannelSoundingSession(_)` → `peripheral(_:didCompleteChannelSoundingSession:)`.
- **Nearby Interaction** — `NINearbyAccessoryConfiguration(bluetoothChannelSoundingIdentifier:previousChannelSoundingIdentifier:)`, `isCameraAssistanceEnabled` (gated on `supportsCameraAssistance`), `NISession.run(_)`, `updateMotionState(_:forObjectWithToken:)`, `NINearbyObject.distance` / `.horizontalAngle`.
- **Motion** — `NIMotionActivityState` has `.moving` and `.stationary`.
