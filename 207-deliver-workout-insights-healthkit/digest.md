---
title: "Deliver workout insights with HealthKit workout zones — Full Digest"
session: WWDC26 · 207
url: https://developer.apple.com/videos/play/wwdc2026/207/
duration: 12m
speakers: Seth (HealthKit team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 207 — Deliver workout insights with HealthKit workout zones

## TL;DR

In **iOS 27 and watchOS 27**, **workout zones** — for both **heart rate** and
**cycling power** — are built directly into HealthKit. HealthKit now does the work
of mapping each incoming sample to a zone and tracking time-in-zone automatically,
so your app reads structured zone data instead of bucketing raw samples itself.

Three headline capabilities:

1. **Read zones from a completed workout** — `zoneGroupsByType` on `HKWorkout` /
   `HKWorkoutActivity` returns an `HKWorkoutZoneGroup` (configuration + per-zone
   durations) you can chart or use to classify effort.
2. **React to zones live** — `HKLiveWorkoutBuilderDelegate.didUpdateWorkoutZone`
   fires when the current zone changes, with current/previous zone, cumulative
   totals, and a timestamp — enough to drive live coaching and target-zone alerts.
3. **Control the zone model** — by default HealthKit uses the person's **preferred
   zones** (synced from Health Settings, auto-calculated from age + resting HR), but
   apps with a **proprietary model** can supply a **custom `HKWorkoutZoneConfiguration`**
   per workout.

Heart rate zones are typically 5; cycling power defaults to 6; custom configs allow
3–9. The talk demos heart rate; cycling power follows the same shape (swap the
`HKQuantityType`).

---

## 1. What workout zones are (0:01)

- **Heart rate zones** are personalized from **age** and **resting heart rate**.
  Typically **5 zones**. Each sample (e.g. 135 bpm) falls into one zone (e.g. Zone 3),
  which expresses intensity. Useful for running, cycling, HIIT, rowing.
- **Cycling power zones** measure power output in **watts**, based on a personalized
  **functional threshold power (FTP)**.
- Why they matter: many plans set intensity goals (endurance → stay below a threshold;
  intervals → stay at/above a level for a period). Time spent largely in zones 4–5
  signals a hard effort, which feeds recovery / load-balance guidance.
- **HealthKit calculates time-in-zone automatically** from incoming samples during the
  workout, and people can share zone info directly with your app.

### Authorization

Same flow as other HealthKit types — request authorization for the relevant quantity
types: **workouts, heart rate, and cycling power**.

## 2. Reading zones from a completed workout (2:17)

Access `zoneGroupsByType` on either `HKWorkout` or an individual `HKWorkoutActivity`,
keyed by the `HKQuantityType` you want. For cycling power, just swap the quantity type.

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

If zones are available, you get back an **`HKWorkoutZoneGroup`** with two properties:

- **`configuration`** (`HKWorkoutZoneConfiguration`) — describes the set of zones and
  how they were made:
  - the **`HKQuantityType`** (here, heart rate);
  - a **`source`** enum: **system** (auto-calculated), **manual** (set by the user in
    Settings), or **custom** (supplied by an app at workout time);
  - an array of **zones** ordered by boundary. Each zone has an **index** and a min/max
    `HKQuantity`. The **first zone has no lower bound, the last has no upper bound**, so
    the full value range is always covered. Zones are **contiguous and non-overlapping**.
- **`zoneDurations`** — an array where each element pairs a **zone** with the **time
  spent** in it, ordered by threshold.

Because zones live on both `HKWorkout` and `HKWorkoutActivity`, you can show one chart
for the whole workout or break it out **per activity** in multi-sport workouts. The
demo charts `zoneDurations` into a post-workout time-in-zone bar graph.

## 3. Live zone updates during a workout (6:19)

During a live workout HealthKit processes each incoming heart rate sample, assigns a
zone, and **notifies your app only when the current zone changes** (e.g. Zone 2 → 3).
You receive these via **`HKLiveWorkoutBuilderDelegate`**, the protocol for live-workout
updates, using **`didUpdateWorkoutZone`**.

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

Each `HKLiveWorkoutZoneUpdate` includes:

- **current** and **previous** zone (updates fire only on change);
- the **zone group** — full configuration plus current total time per zone (running totals);
- a **timestamp** for the last processed sample — handy for a live "time in current zone" timer.

Use it to **highlight the active zone** and **alert** the person when they drift out of
a target zone so they can adjust intensity.

## 4. Preferred zones (8:11)

By default HealthKit uses the **preferred** zone thresholds configured in **Health
Settings**, giving people a consistent experience across apps and devices (these
**sync via HealthKit**). Preferred zones are either:

- **system-calculated** — periodically recomputed from user metrics where available
  (heart rate from age + resting HR), or
- **manually configured** in Health Settings.

Before starting a zone-aware workout, confirm a preferred configuration exists. Query
it on **`HKHealthStore`** or **`HKWorkoutBuilder`**:

```swift
if try await builder.zoneConfiguration(for: HKQuantityType(.heartRate)) == nil {
    // No preferred configuration — fall back to a custom one (next section).
}
```

## 5. Custom zones (9:00)

Use a custom configuration when your app has a **proprietary zone model** that differs
from Health Settings (e.g. a training platform). The pattern: if no preferred config is
set, build boundaries → make a configuration → hand it to the builder **before**
`beginCollection`.

```swift
let defaultHeartRateZoneThresholds = [91.0, 114.0, 136.0, 158.0]
let bpmUnit = HKUnit.count().unitDivided(by: HKUnit.minute())
let boundaries = defaultHeartRateZoneThresholds.map(
    { HKQuantity(unit: bpmUnit, doubleValue: $0) }
)

let heartRate = HKQuantityType(.heartRate)
let defaultConfiguration = try HKWorkoutZoneConfiguration(
    quantityType: heartRate,
    zoneBoundaries: boundaries
)

try await builder.setCustomZoneConfiguration(defaultConfiguration, for: heartRate)

// Custom zones must be set BEFORE beginning collection.
let startDate = Date()
try await builder.beginCollection(at: startDate)
```

Rules and gotchas:

- **Units must match** and be compatible with the configuration's quantity type
  (heart rate uses count/minute).
- HealthKit derives zones from the boundaries: **first zone starts at 0, last is
  unbounded**. **Between 3 and 9 zones** are required.
- Custom zone configs are **only saved within the context of that workout** — HealthKit
  does **not** persist or sync them. **Your app** owns saving/syncing if needed.
- `setCustomZoneConfiguration(_:for:)` must be called **before `beginCollection`**.

## 6. Comparing across different zone counts (10:22)

Zone configurations can vary in thresholds and **number of zones** — common for cycling
power (system default **6**, but apps use **5, 7, or 8**). This is a trap when
comparing efforts:

- **Zone 3 in a 5-zone workout ≠ Zone 3 in a 7-zone workout** — each index covers a
  different value range, so time-in-zone is **not** directly comparable by index.
- To compare across workouts, **normalize**: take the original samples and **re-bucket**
  them into your app's chosen number of zones (the talk's example normalizes to 7).

## Wrap-up

Workout zones enable post-workout summary screens, live coaching, and long-term training
dashboards on a simplified HealthKit interface. Apple recommends: adopt the API (use the
sample app as a guide), chart zone data, and handle live zone changes.
