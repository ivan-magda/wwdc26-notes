# Session 345 — Discover new capabilities in the App Intents framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/345/
- **Duration:** 18m
- **Speaker:** Moe (engineer on the App Intents team)
- **Sample app:** Landmarks Travel Tracking (from "Get to know App Intents", WWDC25)

## Description

The 2027 App Intents updates bring more control, more flexibility, and a smoother
developer experience across Siri, Shortcuts, Spotlight, Widgets, and Apple
Intelligence. The talk covers three areas — entity enhancements, richer parameter
types, and intent execution — all built on the Landmarks Travel Tracking sample app.

## Key topics

- **`ValueRepresentation`** — a new `Transferable` representation that shares structured
  types the system already understands (e.g. export a landmark as a `PlaceDescriptor`
  from GeoToolbox so it flows to Maps for directions). Supports a closure or a key-path.
- **`RelevantEntities`** — proactively suggest entities to the system with a context
  (e.g. running playlists when a workout starts) via `updateEntities`; remove by
  context, by entity, or entirely. Complements Spotlight indexing and interaction
  donation for content that's never been searched or used.
- **`EntityCollection`** — passes only identifiers to `perform()` without resolving full
  entities; a one-line `@Parameter` type change that made tagging 1000 photos nearly
  instant.
- **`SyncableEntity` / `SyncableEntityIdentifier`** — declare a stable ID so Siri can
  continue conversations across devices; pair a local and a stable ID when you only
  have local IDs.
- **Richer native parameter types** — `Duration`, `PersonNameComponents`, and more, each
  with a free native picker, Siri understanding, and localization across Siri,
  Shortcuts, and Widgets.
- **`@UnionValue`** — one `@Parameter` accepts multiple types via a Swift enum; the macro
  generates type info, case metadata, and picker support; works everywhere including
  Shortcuts.
- **`LongRunningIntent`** — run beyond the 30-second limit, manage the background task
  lifecycle, and show progress as a Live Activity; builds on `ProgressReportingIntent`;
  supports background GPU access.
- **`CancellableIntent`** — an `onCancel` handler to clean up gracefully (user cancel,
  timeout, or resource reclaim).
- **`ExecutionTargets`** — override the system's process-selection heuristics to target
  the main app, an App Intents extension, a WidgetKit extension, or any combination.

## Related sessions to fetch (referenced in this talk)

- [ ] Get to know App Intents (WWDC25)
- [ ] Explore advanced App Intents features for Siri and Apple Intelligence
- [ ] Code-along: Make your app available to Siri
- [ ] Validate your App Intents adoption with AppIntentsTesting
- [ ] WWDC25 background tasks video (named but not titled — "running tasks in the background")

## Chapter summary (Summary tab)

- **0:00 Introduction** — The 2027 App Intents updates; three areas: entity
  enhancements, richer parameters, intent execution, built on Landmarks Travel Tracking.
- **2:40 Share entities across apps with `ValueRepresentation`** — beyond Transferable's
  File/Data representations, share structured types the system understands (export a
  landmark as a `PlaceDescriptor` → Maps directions). Use a key-path if the property
  already exists.
- **3:45 Register relevant entities with `RelevantEntities`** — surface never-seen,
  never-used content via `updateEntities` with a context (running playlists at workout
  start); remove by context, by entity, or entirely.
- **7:05 Handle entities efficiently with `EntityCollection`** — pass just identifiers to
  `perform()` without full resolution; one-line change made tagging 1000 photos nearly
  instant.
- **8:55 Use entities across devices with `SyncableEntity`** — Siri continues
  conversations across devices; declare a stable ID (server UUID / CloudKit record ID),
  or pair local + stable with `SyncableEntityIdentifier`.
- **11:01 Richer parameter types** — native `Duration`, `PersonNameComponents`, and more,
  with free pickers across Siri, Shortcuts, Widgets.
- **12:38 Union value parameters** — `@UnionValue` enum lets one parameter accept multiple
  types (a widget showing photos from a landmark collection or a photo album).
- **13:26 Extend execution with `LongRunningIntent`** — run beyond 30s, manage background
  task lifecycle, show progress as a Live Activity; `CancellableIntent.onCancel` for
  graceful cleanup; background GPU access.
- **15:27 Target the right process with `ExecutionTargets`** — override heuristics to
  target the main app, App Intents extension, WidgetKit extension, or any combination.
- **17:14 Next steps** — adopt `ValueRepresentation`, register relevant content,
  `EntityCollection` for large sets, `LongRunningIntent` for >30s work. See the
  Code-along and AppIntentsTesting sessions.

## Code

See `code.md` — 7 snippets extracted from the Code tab.
