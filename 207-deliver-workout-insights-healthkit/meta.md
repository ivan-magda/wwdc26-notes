# Session 207 — Deliver workout insights with HealthKit workout zones

- **URL:** https://developer.apple.com/videos/play/wwdc2026/207/
- **Duration:** 12m
- **Speakers:** Seth (HealthKit team engineer)

## Description

Workout zones turn biometric data into actionable training guidance, helping people
understand their effort and intensity in a workout. In iOS 27 and watchOS 27, heart
rate and cycling power zone support is built directly into HealthKit. The session
walks through accessing zone data from completed workouts, registering for live zone
change updates during a session, reading people's preferred zone configurations, and
supplying custom zone configurations — using a sample workout-tracking app as a guide.

## Key topics

- **Workout zones in HealthKit (iOS 27 / watchOS 27)** — heart rate *and* cycling power zones; HealthKit auto-calculates time-in-zone from incoming samples
- **Heart rate zones** — personalized from age + resting heart rate; typically 5 zones; each sample (e.g. 135 bpm) maps to a zone (e.g. Zone 3) = intensity level
- **Cycling power zones** — measured in watts, based on personalized functional threshold power (FTP); system default is 6 zones
- **Reading completed-workout zones** — `zoneGroupsByType[HKQuantityType]` on `HKWorkout` or `HKWorkoutActivity` → `HKWorkoutZoneGroup` (configuration + zone durations)
- **`HKWorkoutZoneConfiguration`** — quantity type, source enum (system / manual / custom), and contiguous, non-overlapping zones (first unbounded low, last unbounded high)
- **Live zone updates** — `HKLiveWorkoutBuilderDelegate.didUpdateWorkoutZone`; fires only on zone change, includes current + previous zone, cumulative zone group, last-sample timestamp
- **Preferred zones** — synced via HealthKit across devices; system-calculated or manually set in Health Settings; query on `HKHealthStore` or `HKWorkoutBuilder`
- **Custom zones** — `HKWorkoutZoneConfiguration` set on `HKWorkoutBuilder` before `beginCollection`; scoped to the single workout, not persisted by HealthKit; 3–9 zones required
- **Normalizing across zone counts** — zone 3 in a 5-zone model ≠ zone 3 in a 7-zone model; re-bucket original samples rather than comparing indices directly
- **Authorization** — same flow as other HealthKit types; request workouts + heart rate + cycling power quantity types

## Related sessions to fetch (referenced in this talk)

- [ ] _None explicitly named._ The talk points to "the provided sample app" as a guide but does not reference other WWDC sessions by title.

## Chapter summary (Summary tab)

- **0:01 Introduction** — Workout zones turn biometric data into actionable training guidance. In iOS/watchOS 27, heart rate and cycling power zone support is built into HealthKit. Heart rate zones are personalized (age + resting HR), typically 5 zones; cycling power zones are in watts based on FTP. Demoed via a sample app.
- **2:17 Accessing workout zones** — Use `zoneGroupsByType` on a completed `HKWorkout` / `HKWorkoutActivity` for a given `HKQuantityType` (e.g. heart rate). Explore `HKWorkoutZoneConfiguration` (quantity type, source, contiguous zone boundaries) and the array of zone durations (time spent per zone). Use it to render a post-workout zone chart or classify effort.
- **6:19 Live zone updates** — Adopt `didUpdateWorkoutZone` on `HKLiveWorkoutDelegate` for real-time notifications when heart rate crosses into a new zone. Each update carries current + previous zone, cumulative running totals, and the last-processed sample timestamp. Use it to highlight the active zone and alert on drift from target zones.
- **8:11 Preferred zones** — HealthKit defaults to preferred zone thresholds from Health Settings (auto-calculated from age / resting HR, or manually configured). These sync across devices. Before starting a zone-aware workout, query for a preferred zone configuration on `HKHealthStore` or `HKWorkoutBuilder` to confirm one is set.
- **9:00 Custom zones** — When the app uses a proprietary zone model, configure each workout with a custom `HKWorkoutZoneConfiguration` supplied to `HKWorkoutBuilder` before `beginCollection`. Custom configurations are scoped to a single workout and not persisted by HealthKit.

## Code

See `code.md` — 7 snippets extracted from the Code tab.
