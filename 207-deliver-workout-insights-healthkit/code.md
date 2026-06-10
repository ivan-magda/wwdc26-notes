# Code samples — Session 207

Extracted from the Code tab. Timestamps map to the Summary-tab chapters; the
on-screen `// Copy Code` placeholders have been stripped.

## 2:17 — Read heart rate zones from a completed workout

```swift
// Read heart rate zones from the completed workout
if let heartRateZoneGroup = workout.zoneGroupsByType?[HKQuantityType(.heartRate)] {
    let zones = ZoneDisplayData(
        zoneCount: heartRateZoneGroup.configuration.zones.count,
        currentZoneIndex: nil,
        durations: heartRateZoneGroup.zoneDurations.map(\.duration)
    )
}
```

## 6:19 — Live zone-change updates via the delegate

```swift
func workoutBuilder(_ workoutBuilder: HKLiveWorkoutBuilder,
                    didUpdateWorkoutZone zoneUpdate: HKLiveWorkoutZoneUpdate) {
    guard let zoneGroup = zoneUpdate.zoneGroup else {
        return
    }
    if let currentIndex = zoneUpdate.currentZoneDuration?.zone.index {
        let data = ZoneDisplayData(
            zoneCount: zoneGroup.configuration.zones.count,
            currentZoneIndex: currentIndex,
            durations: zoneGroup.zoneDurations.map(\.duration)
        )
        Task { @MainActor in
            self.heartRateZones = data
        }
    }
}
```

## 8:11 — Check for a preferred zone configuration

```swift
if try await builder.zoneConfiguration(for: HKQuantityType(.heartRate)) == nil {
    // No preferred configuration set — fall back to a custom one (below).
}
```

## 9:00 — Build zone boundaries from default thresholds

```swift
let defaultHeartRateZoneThresholds = [91.0, 114.0, 136.0, 158.0]
let bpmUnit = HKUnit.count().unitDivided(by: HKUnit.minute())
let boundaries = defaultHeartRateZoneThresholds.map(
    { HKQuantity(unit: bpmUnit, doubleValue: $0) }
)
```

## 9:00 — Create a custom HKWorkoutZoneConfiguration

```swift
let heartRate = HKQuantityType(.heartRate)
let defaultConfiguration = try HKWorkoutZoneConfiguration(
    quantityType: heartRate,
    zoneBoundaries: boundaries
)
```

## 9:00 — Supply the custom configuration to the builder

```swift
try await builder.setCustomZoneConfiguration(defaultConfiguration,
                                             for: heartRate)
```

## 9:00 — Begin data collection (must come after setting custom zones)

```swift
// Begin data collection
let startDate = Date()
try await builder.beginCollection(at: startDate)
```

---

## API facts surfaced by the code

- **Completed-workout read:** `workout.zoneGroupsByType?[HKQuantityType(.heartRate)]` → `HKWorkoutZoneGroup` with `.configuration` and `.zoneDurations`.
- `HKWorkoutZoneGroup.configuration.zones.count` gives the zone count; `zoneDurations.map(\.duration)` gives per-zone times.
- **Live delegate:** `HKLiveWorkoutBuilder` (delegate method `didUpdateWorkoutZone`) passes an `HKLiveWorkoutZoneUpdate` exposing `.zoneGroup`, `.currentZoneDuration?.zone.index`, previous/current zone, and a last-sample timestamp.
- **Preferred-zone query:** `builder.zoneConfiguration(for:)` (and the equivalent on `HKHealthStore`) returns `nil` when no preferred configuration exists.
- **Custom zones:** build `HKQuantity` boundaries (units must match the quantity type), construct `HKWorkoutZoneConfiguration(quantityType:zoneBoundaries:)`, then `builder.setCustomZoneConfiguration(_:for:)` **before** `builder.beginCollection(at:)`.
- Heart rate boundary unit: `HKUnit.count().unitDivided(by: .minute())` (bpm). Threshold example `[91, 114, 136, 158]` → 5 zones.
- Zone-count rule (from transcript): **3–9 zones**; first zone starts at 0, last is unbounded.
