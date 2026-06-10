# Session 223 — Live Activities essentials

- **URL:** https://developer.apple.com/videos/play/wwdc2026/223/
- **Duration:** 15m
- **Speakers:** Adi (System Experience Engineer)

## Description

Live Activities keep people up-to-date about ongoing tasks or events with progressing
information over time, appearing on the Lock Screen, in the Dynamic Island, on Apple
Watch, in the macOS menu bar, and on the CarPlay Dashboard. This session covers the
essentials: designing an efficient data model with `ActivityAttributes` and
`ContentState`, building the SwiftUI presentations, starting and updating activities via
ActivityKit or push notifications, and optimizing for each screen — Dynamic Island
landscape, StandBy, the small activity family, and App Intent interactivity.

## Key topics

- Where Live Activities surface: Lock Screen, Dynamic Island (compact / minimal /
  expanded), StandBy, Apple Watch Smart Stack, macOS menu bar, CarPlay Dashboard
- iOS 27: Dynamic Island compact + minimal views now show in both portrait and landscape
- Data model split: static data in an `ActivityAttributes` struct, dynamic data in a
  nested `ContentState` struct — only dynamic data updates over the activity's lifetime
- Building views with WidgetKit / `ActivityConfiguration` and `DynamicIsland` regions
- Lifecycle: `ActivityAuthorizationInfo`, `Activity.request`, `activity.update`,
  `staleDate`
- Remote updates: broadcast channels (mass scale) vs. targeted push notifications (push token)
- Optimization: `isDynamicIslandLimitedInWidth` (landscape), `showsWidgetContainerBackground`
  + `activityBackgroundTint` (StandBy), `supplementalActivityFamilies([.small])` +
  `activityFamily` (Watch / CarPlay)
- Interactivity: `LiveActivityIntent` wired to SwiftUI `Button(intent:)`

## Related sessions to fetch (referenced in this talk)

- [ ] Design dynamic Live Activities
- [ ] SwiftUI essentials
- [ ] Bring your Live Activity to Apple Watch

## Chapter summary (Summary tab)

- **0:01 Introduction** — Overview of the Live Activities experience: glanceable,
  timely updates on Lock Screen, Dynamic Island, Apple Watch, CarPlay Dashboard; iOS 27
  adds landscape Dynamic Island visibility and StandBy.
- **1:53 Create and update** — Define an efficient data model using `ActivityAttributes`
  (static) and `ContentState` (dynamic); build tailored SwiftUI views for each
  presentation; manage lifecycle locally via ActivityKit or remotely via push.
- **9:51 Optimize** — Adapt layouts to constraints (limited width in landscape Dynamic
  Island, StandBy background, `.small` activity family for Apple Watch / CarPlay) and
  integrate App Intents for quick contextual actions.

## Code

See `code.md` — 9 snippets extracted from the Code tab.
