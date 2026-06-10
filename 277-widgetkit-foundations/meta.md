# Session 277 — WidgetKit foundations

- **URL:** https://developer.apple.com/videos/play/wwdc2026/277/
- **Duration:** 21m
- **Speaker:** Jonathan Long (System Experience engineer)

## Description

Widgets highlight your app's most important content across the system. The best
widgets are glanceable, relevant, and personalizable. This session covers the
foundations of WidgetKit: how to build your first widget with a widget extension
and a timeline provider, how to keep it up to date with reload policies, how to
integrate it with your app via deep links, configuration, and interactive
elements, and how to make it adapt to system appearance modes (full color,
tinted, clear) across iOS, iPadOS, watchOS, visionOS, and macOS.

## Key topics

- Widget fundamentals — glanceable, relevant, personalizable; widget extension runs as a separate process; shared container in an app group to pass data
- Timeline model — `TimelineProvider` supplies snapshot, placeholder, and timeline entries; each `TimelineEntry` carries data to render a view at a point in time; views are archived and rendered by the system
- Configurations — `StaticConfiguration` (auto-configured) vs `AppIntentConfiguration` (user-configurable); `containerBackground(for: .widget)` for tint-adaptive backgrounds
- Reload policies — `.atEnd`, `.afterDate(_)`, `.never`; explicit reloads via `WidgetCenter` reload APIs or push notification; budgeted reloads tied to viewing habits
- Widget families — recommend supporting as many sizes as you can; new `.systemExtraLargePortrait` family in macOS/iOS/iPadOS 27 (originally visionOS 26); `.supportedFamilies` modifier
- Integration — `widgetURL` deep links; configurable widgets via App Intents; interactive buttons/toggles backed by App Intents
- Appearance adaptation — tinted/clear glass material rendering; `widgetAccentedRenderingMode(.fullColor)` to keep images (e.g., book covers) in full color
- Reach — iOS widgets also appear on CarPlay and as remote widgets on macOS
- Testing — SwiftUI previews (families, color schemes, rendering modes); WidgetKit developer mode to lift reload budgets

## Related sessions to fetch (referenced in this talk)

- [ ] Principles of great widgets (WWDC21)
- [ ] Live Activities essentials (WWDC26)
- [ ] Explore enhancements to App Intents (WWDC23)
- [ ] Bring widgets to life (WWDC23)
- [ ] What's new in widgets (WWDC25)

## Chapter summary (Summary tab)

- **0:01 Introduction** — Widgets highlight your app's most important content across the system; the best widgets are glanceable, relevant, and personalizable. Agenda: fundamentals, app integration, system adaptation.
- **1:03 Fundamentals** — Widgets are built in a widget extension exposing a timeline of `TimelineEntry` values; each entry renders a SwiftUI view at a moment in time. Define the widget with `StaticConfiguration` or `AppIntentConfiguration`, build a quality `TimelineProvider`, and pick a reload policy. New `.systemExtraLargePortrait` family on macOS/iOS/iPadOS 27.
- **13:15 Integrate with your app** — Three integration points: deep links (route taps to specific content), configurable widgets (personalize content), and interactive elements (App Intent-backed buttons/toggles).
- **17:04 Adapt with the system** — Widgets adapt to full color, tinted, and clear appearance modes. SwiftUI handles most adaptation; customize specific views with `widgetAccentedRenderingMode(.fullColor)`. Test for appearance modes and budgeted reloads.

## Code

See `code.md` — 4 snippets extracted from the Code tab (the evolving `DailyReadingGoalWidget` plus `BookCoverImage`).
