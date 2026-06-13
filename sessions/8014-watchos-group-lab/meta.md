# Session 8014 — watchOS Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8014/
- **Duration:** 01:00:06 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + engineering panel answering upvoted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization.

## Panel

Roles are taken from the on-air self-introductions (00:00–01:36). First names that appear only mid-sentence elsewhere in the audio are not used to build the roster, and the host's routing ("Dan, do you want to kick this off") is how most answers are attributed below.

- **Leah Wommelsdorf** — host; Worldwide Developer Relations team at Apple.
- **Dan Keene** — manages the workout team on watchOS.
- **Anne Hitchcock** — watchOS frameworks engineer; system spaces / system UI, connectivity work this year, and collaborations with the Foundation Models team and the App Intents team.
- **Devin** — widgets and live activities, and how they integrate with system spaces like the Smart Stack and watch faces. (Introduced by first name only; surname not stated.)
- **Matthew Koontz** — manages the watchOS system spaces team: how apps launch, how notifications arrive, and the underlying look and feel of the platform.

## Description

A roughly one-hour live, on-demand watchOS group lab. There is no separate warm-up round: the host moves straight from introductions into developer questions. The panel answers a set of upvoted developer questions spanning Foundation Models on the watch, background and concurrency constraints, the iOS-to-watchOS transition, on-device debugging and tooling, favorite watchOS 27 features, 3D rendering after SceneKit's deprecation, Liquid Glass refinements, the widgets/live activities/controls trio and when to use each, widget update budgets, the new workout zones API, and standalone first-launch asset fetching. The recurring throughline is to **file feedback with concrete use cases** and to treat the watch's constraints as a design opportunity rather than a limitation.

## Key topics

- **Foundation Models on watchOS 27** — requires a network call; uses Private Cloud Compute or any `LanguageModel`-conforming provider, never the iPhone's on-device model. Check availability, plan token/quota fallbacks, mind network latency. Official `LanguageModel` support for Claude and Gemini "coming soon" from those vendors.
- **New frameworks on watchOS 27** — Foundation Models, Vision framework, and a "Core AI framework" (transcript-unclear name) all arrive on the watch this year.
- **Background / concurrency constraints** — familiar APIs (NSURLSession, CloudKit, Swift async) work, but watchdog timeouts are strict by design to protect all-day battery; lower core count means being deliberate about kicked-off work.
- **Tooling and debugging** — Xcode 27 plus Device Hub gives a direct Mac-to-watch connection (no longer proxied through the phone); 5 GHz Wi-Fi on recent hardware helps throughput; peer-to-peer must be enabled on the network.
- **3D rendering after SceneKit** — SceneKit is deprecated but not gone; recommended path is SwiftUI Canvas (GPU-accelerated). RealityKit is not on watchOS.
- **Liquid Glass on watchOS 27** — darker speculars, better content separation while scrolling, performance gains; mostly automatic for apps already on the watchOS 10 design language; no per-device clear/tinted slider on watch.
- **Widgets, live activities, controls** — when to reach for each; widget update budget (~15–20 min when actively used); watch face vs Smart Stack tiers; APNS-based refresh (watchOS 26) and watch-connectivity-based widget refresh (new this year).
- **Workout zones API** — heart rate zones, cycling power zones, time-in-zone; usable beyond workout apps; a long-requested "hidden gem."
- **HealthKit additions** — menopause / perimenopause API, workout zones, rich watch-sourced data (heart rate, calories, Core Motion).
- **Standalone first-launch asset fetching** — bundle what you instantly need, use background URL sessions, design a graceful fully-offline experience.

## Related sessions (referenced on-air)

Titles were spoken in the lab; **session numbers are not stated on air** and are not asserted here. The "designing great watch experiences" video the panel reaches for is from the watchOS 10 era (WWDC23), with the title approximated live as "Design and build apps with watchOS."

- "Bring your live activity to Apple Watch" — referenced as a WWDC25 session (the produce / "mango cart" delivery live-activity demo).
- Live activities fundamentals / "Meet Live Activities" — referenced as a session to dive into live activities (title approximated on-air).
- "Widget Kit Foundations" / WidgetKit foundations — WWDC26, referenced as a foundations video for building and keeping widgets up to date (title approximated on-air).
- "What's new in SwiftUI" — WWDC26, referenced for new tools to customize Liquid Glass on the watch and for the new reorderable API (also said to be mentioned in the State of the Union).
- A watchOS 10-era design session ("Design and build apps with watchOS," approximate title) — referenced for designing for the wrist instead of porting an iOS layout.
- Developer-website article "Keeping Your Widget Up To Date" — referenced as the canonical resource on widget update budgets (article, not a session).

## Chapter summary

No published chapters (group lab). Rough arc with transcript timestamps:

- **00:00** — Intros and self-introductions; host frames the year's big watchOS 27 themes.
- **02:28** — Foundation Models on the watch (PCC vs on-device, network requirement) — questions from Emcro and Durkio, plus design fundamentals for the wrist.
- **10:09** — iOS-to-watchOS transition: background constraints, off-main-thread work, CloudKit (LazyVar).
- **15:52** — Having a watch presence without a full app: live activities, controls (LazyVar follow-up).
- **18:48** — Speeding up physical watchOS debug loops; Xcode 27 + Device Hub direct connection (LazyVar).
- **23:22** — Favorite watchOS 27 features round (PearlJam66): workout buddy insights, Foundation Models, widget refresh via watch connectivity, Smart Stack suggestions, Siri AI, health/zones.
- **27:04** — 3D rendering after SceneKit deprecation; SwiftUI Canvas (Zev Eisenberg, NYT).
- **29:04** — Lesser-known watch opportunities; Core Motion as a peripheral; live activities and Smart Stack relevance; sports/noise alerting design (Prechaya).
- **37:18** — Liquid Glass on watchOS 27 (Simon, Helix): refinements, automatic adoption, no tint slider on watch.
- **42:32** — Hidden gems: workout zones, the reorderable API.
- **45:40** — Widget update budgets; timeline vs relevance vs live activities vs controls (Medium Fidelity).
- **53:35** — Standalone first-launch asset fetching with limited connectivity (LazyVar).
- **57:09** — Watch-band show-and-tell, thanks, and wrap-up (forums + Feedback Assistant).

## Code

n/a — no code was shown on screen; this lab is verbal Q&A. API and feature names are captured in `digest.md`.
