# Code samples — Session 212

Three snippets from the Code tab. Timestamps map to the transcript section where each
API is discussed.

## ~6:42 — Opt out of the now-playing MiniPlayer

```swift
// Disable the MiniPlayer
CPNowPlayingTemplate.shared.allowsMiniPlayer = false
```

When set to `false`, the now-playing icon appears in the navigation bar instead of the
MiniPlayer. The MiniPlayer is the recommended default and shows automatically for every
app that presents now playing.

## ~15:08 — Opt in to Route sharing (Map template delegate)

```swift
// Enable route sharing
func mapTemplateShouldProvideRouteSharing(_ mapTemplate: CPMapTemplate) -> Bool { true }
```

Per-vehicle, the driver must first approve route sharing at pairing; this delegate
opt-in is the app's side of enabling it.

## ~15:11 — Disable Route sharing for a single trip

```swift
// Disable route sharing for this trip
trip.routeSegmentsAvailableForRegion = false
```

Use when the app determines a specific trip is not eligible for sharing, even though
route sharing is otherwise enabled for the vehicle.

---

## Spoken APIs (named in the transcript, no Code-tab snippet)

- `CPSessionConfiguration` — check whether the connected car supports video before adding a videos tab.
- `CPPlaybackConfiguration` — supplies playback metadata (elapsed time, duration, playback action: play / pause / replay) and `preferredPresentation` (`.video` / `.audio`) for thumbnails and the details header. Update it on every playback state change.
- Thumbnail / card overlays — title badges, custom image badges, and a sports overlay (left team, right team, event status).
- Details header — single prominent thumbnail + title + body + playback configuration + action buttons; first action button auto-combines with playback progress.
- Voice Control template — prompt + animated state icon (both optional), up to two action buttons + leading/trailing nav-bar buttons; full-screen or overlay.
- `CPInterfaceController` — present the Voice Control template as an overlay on top of another template (e.g. the Map template).
- `CPTemplateApplicationScene` — open a URL to perform an action (start navigation, place a call) from a voice interaction.
- `AVAudioSession` — configure with the play-and-record category, default mode, mixing disabled, for voice-conversation audio feedback.
- `CPMapTemplate` panels — composed from trips, grids, route choices, route details, waypoints, and list items, with a button configuration ("Go" / "End") to control the map's primary interface area.
- Route segments — array of geographic coordinates sent to the vehicle whenever the trip changes; the vehicle can propose a charging waypoint back via the Map template.
