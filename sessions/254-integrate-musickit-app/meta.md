# Session 254 — Integrate MusicKit into your app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/254/
- **Duration:** 21m
- **Speakers:** Cathy, Alan (MusicKit team)

## Description

An introduction to MusicKit and a walkthrough of building a music-enhanced workout
app using Swift concurrency and SwiftUI. Covers Xcode project setup and music
authorization, the MusicKit music item model, the unified music picker, the two
music players (SystemMusicPlayer and ApplicationMusicPlayer) and playback UI, and
structured catalog requests for fetching curated Apple Music content with
localization/content-equivalency handling.

## Key topics

- Project setup: register a developer token (MusicKit checkbox in App Services on the App ID), sign into the same Apple Developer account in Xcode
- Music authorization via `MusicAuthorization.request()`; Media Library capability + usage-description string drives the permissions alert
- Apple Music subscription offer in-app via `.musicSubscriptionOffer` view modifier; `MusicSubscriptionOffer.Options` (message identifier `.playMusic`, Performance Partner Program info); observing `MusicSubscription.current` + `subscriptionUpdates`
- Music items: value types with Attributes (e.g. `title`, `contentRating`), Relationships (e.g. Album `tracks`), and Associations (weaker ties, e.g. `otherVersions`); types include Album, Song, Genre, Station, Playlist
- `.musicPicker` SwiftUI view modifier — unified catalog + library browse/search; single or multi-selection (array binding); library-only without a subscription
- Players: `SystemMusicPlayer` (controls system Music app, write-only queue, keeps playing when app backgrounds/quits) vs `ApplicationMusicPlayer` (in-app, full read/write queue, needs Audio Background Mode capability to keep playing in background); both subclass `MusicPlayer`
- Playback: queue of playable items; `play()`/`pause()`; `prepareToPlay()` buffering; lazy container-queue initializers; `affectsListeningHistory`; observable `state` and `queue`; `ArtworkImage`; `skipToNextEntry`/`skipToPreviousEntry`; `playbackStatus`
- Catalog requests: `MusicCatalogResourceRequest`, `.response()` → `MusicCatalogResourceResponse` with strongly-typed `MusicItemCollection`; pagination (`hasNextBatch`/`nextBatch()`); relationships/associations/limit on the request; `.findEquivalents` option for region/storefront + explicit→clean equivalency; `item(for:)`

## Related sessions to fetch (referenced in this talk)

- [ ] Discover Observation in SwiftUI (WWDC2023)
- [ ] Explore more content with MusicKit (WWDC2022)
- [ ] Meet Apple Music API and MusicKit (Android / web)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Cathy & Alan; overview of building a music-enhanced workout app with Swift concurrency and SwiftUI.
- **2:11 Project setup and authorization** — configure Xcode capabilities, request music library authorization, present Apple Music subscription offers.
- **7:10 Music items and music picker** — properties and relationships of MusicKit music items; use the music picker view modifier to browse/select from catalog or library.
- **10:54 Music players and playback** — `SystemMusicPlayer` vs `ApplicationMusicPlayer`; set up playback queues, observe playback state, build SwiftUI playback controls.
- **16:26 Catalog requests** — structured requests like `MusicCatalogResourceRequest` to fetch curated Apple Music content; localization and content equivalency.
- **20:11 Next steps** — recap of MusicKit capabilities and pointers to related sessions.

## Code

See `code.md` — 8 snippets extracted from the Code tab.
