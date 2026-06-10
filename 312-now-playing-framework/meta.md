# Session 312 — Meet the Now Playing framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/312/
- **Duration:** 13m
- **Speakers:** Leo Formaggio (engineer, Media Frameworks team)
- **Platforms:** iPhone, Apple Watch, Apple Vision Pro, Apple TV, CarPlay, StandBy — system now-playing experience is available on all Apple platforms.

## Description

Discover the NowPlaying framework, which lets apps surface currently-playing media
info on system surfaces like the Lock Screen, Control Center, Dynamic Island,
StandBy, and CarPlay. The talk covers three layers: the media sessions API
(`MediaSessionRepresentable` + `MediaSession`) for local playback; remote media
sessions (`RemoteMediaSessionRepresentable` via an app extension + APNs) for content
playing on other devices like smart speakers; and Media Sharing Extensions, which let
apps route media to third-party speakers/TVs through the system device picker without
embedding each protocol's SDK.

## Key topics

- **Media sessions API** — conform an `@Observable` model to `MediaSessionRepresentable`; describe content via `GenericContent` (or `Music` / `Podcast` / `MovieContent`); expose `playbackSnapshot`, `commands`, and `artwork`. Connect to the system with `MediaSession(model)`.
- **Content types** — `GenericContent`, plus content-specific `Music`, `Podcast`, `MovieContent`. Media `type` is `.audio` or `.video`. Duration can be continuous/`.live` or a defined length with `elapsedTime`.
- **Commands** — `.play`, `.pause`, `.previous`, `.next`, each backed by a closure the system invokes when the user acts on a system surface.
- **Remote media sessions** — `RemoteMediaSessionExtension` app extension + `RemoteMediaSessionRepresentable`; state arrives via APNs push, the extension is launched with the payload and returns an updated session representation. Adds `devices` (`MediaDevice` with `.speaker` type + volume `capabilities`) and an `update(_:)` method.
- **Media Sharing Extensions** — system-managed media protocol implementations; the app uses the system device picker for all supported protocols without bundling each SDK.

## Related sessions to fetch (referenced in this talk)

- [ ] Learn more about Media Sharing Extensions (its own session — title referenced, ID TBD)

## Referenced documentation (articles, not sessions)

- "Publishing Media Sessions" — Apple Developer Documentation
- "Setting up a remote notification server" — developer.apple.com
- "Publishing remote media sessions"
- "Routing media to third-party devices"

## Chapter summary (Summary tab)

- **0:00 Introduction** — the system now-playing experience (Lock Screen, Control Center, Dynamic Island, StandBy, CarPlay), available across all Apple platforms; NowPlaying makes it easy to surface app media to the system.
- **1:08 Media sessions** — adopt `MediaSessionRepresentable` to bring audio/video into the system now-playing experience; demo uses an ambient-sounds app.
- **5:03 Remote media sessions** — adopt `RemoteMediaSessionRepresentable` to extend playback control to devices like smart speakers, using an app extension and APNs.
- **10:31 Media sharing extensions** — route media from iPhone to other devices via the system device picker without embedding additional SDKs.

## Code

See `code.md` — 6 snippets extracted from the Code tab.
