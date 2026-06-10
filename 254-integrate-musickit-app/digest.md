---
title: "Integrate MusicKit into your app — Full Digest"
session: WWDC26 · 254
url: https://developer.apple.com/videos/play/wwdc2026/254/
duration: 21m
speakers: Cathy, Alan
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 254 — Integrate MusicKit into your app

## TL;DR

A soup-to-nuts MusicKit walkthrough framed around adding music to a SwiftUI workout
app. Five practical stages:

1. **Setup + authorization** — register a developer token (App ID → MusicKit
   checkbox), add the **Media Library** capability with a usage string, and request
   access with `MusicAuthorization.request()`.
2. **Subscription offers** — surface an in-app Apple Music sign-up with the
   `.musicSubscriptionOffer` view modifier (with Performance Partner Program
   commission potential), and observe subscription state via `MusicSubscription`.
3. **Music items + picker** — understand the music-item model (Attributes /
   Relationships / Associations) and let users browse the catalog *and* library
   through one `.musicPicker` modifier, single- or multi-select.
4. **Players + playback UI** — pick between `SystemMusicPlayer` and
   `ApplicationMusicPlayer`, drive a queue, and build observable SwiftUI controls
   (`ArtworkImage`, play/pause via `playbackStatus`, skip).
5. **Catalog requests** — fetch curated content with `MusicCatalogResourceRequest`,
   handling pagination and region/explicit **content equivalency** via
   `.findEquivalents`.

MusicKit is Swift-concurrency- and SwiftUI-native throughout: async request methods,
`@State` + `.task` observation, and view modifiers instead of delegate plumbing.

---

## 1. Project setup and authorization (2:11)

Two prerequisites before any code:

- **Developer token** — register on the developer portal so tokens are generated
  automatically. Concretely: on the **App ID** page, check the **MusicKit** box under
  the **App Services** tab. Tokens are tied to your developer account, so sign into
  the *same* account in Xcode.
- **Authorization** — call MusicKit's async `MusicAuthorization.request()`, which
  returns whether access was approved and prompts the user. Add the **Media Library**
  capability under **Signing & Capabilities** and fill in a usage-description string;
  that text appears at the bottom of the permissions alert.

### Subscription offers

An Apple Music subscription is **not required** to use MusicKit, but without one the
app can only reach purchased or synced music. To offer sign-up without leaving the
app, use the `.musicSubscriptionOffer` view modifier (takes an `isPresented`
binding). You can earn commissions via the **Apple Services Performance Partner
Program** by passing your info through `MusicSubscriptionOffer.Options`; the
`messageIdentifier` (here `.playMusic`) changes which UI is presented.

```swift
@State var showSubscriptionOffer = false

let options = MusicSubscriptionOffer.Options(
    messageIdentifier: .playMusic
)

@ViewBuilder
var musicSubsriptionButton: some View {
    Button("Subscribe to Apple Music", systemImage: "music.note") {
        showSubscriptionOffer = true
    }
    .musicSubscriptionOffer(isPresented: $showSubscriptionOffer, options: options)
}
```

Show the button only when the user can actually become a subscriber, and keep the
status fresh by grabbing `MusicSubscription.current` and listening to
`subscriptionUpdates` inside a `.task`:

```swift
@State var subscription: MusicSubscription?

var body: some View {
    VStack {
        // ...
        if let subscription, subscription.canBecomeSubscriber {
            musicSubscriptionButton
        }
    }
    .task(id: isAuthorized) {
        self.subscription = try? await MusicSubscription.current
        for await subscription in MusicSubscription.subscriptionUpdates {
            self.subscription = subscription
        }
    }
}
```

## 2. Music items and the music picker (7:10)

**Music items** are the building blocks of MusicKit's APIs — value types in the
model layer. Three flavors of data hang off each one:

- **Attributes** — simple built-in properties (e.g. an Album's `title`,
  `contentRating`).
- **Relationships** — related content with strong ties (e.g. an Album's `tracks`,
  themselves music items).
- **Associations** — related content with weaker ties (e.g. an Album's
  `otherVersions`, a collection of other albums).

Item types include Album, Song, Genre, Station, and Playlist.

The **music picker** surfaces the Apple Music catalog *and* the user's library in one
unified interface, leveraging many MusicKit request types behind a single
`.musicPicker` view modifier. It does **not** require a subscription — but without one
it shows library items only.

```swift
@State var showMusicPicker = false
@State var selectedSong: Song? = nil

@ViewBuilder
var musicPickerButton: some View {
    Button("Pick some Music", systemImage: "music.note.list") {
        showMusicPicker = true
    }
    .musicPicker(isPresented: $showMusicPicker, selection: $selectedSong)
}
```

For multiple songs, change the `selection` binding from a single `Song?` to an
**array** — multi-selection lets the user add whole albums/playlists from their
detail pages via the plus button at the top.

## 3. Music players and playback (10:54)

MusicKit ships two players, both subclasses of `MusicPlayer`:

| | `SystemMusicPlayer` | `ApplicationMusicPlayer` |
|---|---|---|
| Controls | the system Music app | playback within your app |
| Queue access | write-only (can only see currently playing item) | full read/write |
| Background | keeps playing when app backgrounds/quits | needs **Audio Background Mode** capability |

Both let you toggle whether a queue appears in the Music app's **Recently Played**
and set playback state like Repeat and Shuffle.

**Playback mechanics:**

- A **queue** is a collection of playable items (songs) or container types (album,
  playlist), set on a player.
- `play()` to start, `pause()` to stop. The player first loads the queue, then loads
  audio assets before output — which takes time.
- `prepareToPlay()` buffers ahead so `play()` outputs sound faster.
- Container-type queue initializers **lazily load** the container's items to cut load
  time further.
- `affectsListeningHistory` (default `true`, but respects the Music app's *Use
  Listening History* setting) controls whether the queue shows in Recently Played.
- Both players expose **observable** `state` and `queue` you can read directly in a
  SwiftUI view (see *Discover Observation in SwiftUI*, WWDC2023).

**Playback UI.** Artwork front-and-center via `ArtworkImage`:

```swift
@State var queue = ApplicationMusicPlayer.shared.queue

var body: some View {
    VStack {
        if let artwork = queue.currentEntry?.artwork {
            ArtworkImage(artwork, width: 200, height: 200)
        } else {
            RoundedRectangle(cornerRadius: 16)
                .fill(.quaternary)
                .frame(width: 200, height: 200)
        }
    }
}
```

Title and subtitle from the current entry:

```swift
if let currentSong = queue.currentEntry {
    Text(currentSong.title)
        .font(.title3.bold())

    if let subtitle = currentSong.subtitle {
        Text(subtitle)
            .font(.subheadline)
            .foregroundStyle(.secondary)
    }
}
```

Play/pause derived from `state.playbackStatus`:

```swift
let player = ApplicationMusicPlayer.shared
@State var state = ApplicationMusicPlayer.shared.state

var isPlaying: Bool {
    state.playbackStatus == .playing
}

var playPause: some View {
    Button(
        isPlaying ? "Pause" : "Play",
        systemImage: isPlaying ? "pause.fill" : "play.fill"
    ) {
        if isPlaying {
            player.pause()
        } else {
            Task { try await player.play() }
        }
    }
}
```

Skip controls via `skipToPreviousEntry()` / `skipToNextEntry()`:

```swift
Button("Back", systemImage: "backward.fill") {
    Task { try await player.skipToPreviousEntry() }
}
Button("Next", systemImage: "forward.fill") {
    Task { try await player.skipToNextEntry() }
}
```

## 4. Catalog requests (16:26)

Catalog requests query Apple Music **independent of the user's library** — useful for
shipping curated content (e.g. a suggested-songs shelf the workout app shows so users
can tap to start instantly). MusicKit offers several structured request types:
filter-by-criteria, search, and curated/personalized content — see the MusicKit docs
for the full list.

`MusicCatalogResourceRequest` fetches a specific resource. On the request you can set
**options**, request additional **relationships/associations** (e.g. the song's
Artists), and a **limit**. Calling the async `response()` returns a
`MusicCatalogResourceResponse` whose results are a strongly-typed
`MusicItemCollection`. That collection paginates: when `hasNextBatch` is `true`, fetch
the next page with the async `nextBatch()`.

**Content equivalency.** Resource availability depends on account settings and
storefront/region, so a resource requested in one region may have an **equivalent
resource with a different ID** in another, and explicit content may have an equivalent
**clean** version when the account disallows explicit content. The `.findEquivalents`
option enables this resolution. The request isn't guaranteed to return everything (a
resource may be unavailable), so `item(for:)` is used per ID.

```swift
func fetchSongs(songIDs: [MusicItemID]) async throws -> (featured: Song?, other: [Song]) {
    var request = MusicCatalogResourceRequest<Song>(matching: \.id, memberOf: songIDs)
    request.options = [.findEquivalents]

    let response = try await request.response()

    let featuredSongID = songIDs[0]
    let featuredSong = response.item(for: featuredSongID)

    let others: [Song] = songIDs[1...].compactMap { songID in
        return response.item(for: songID)
    }

    return (featuredSong, others)
}
```

## 5. Next steps (20:11)

Recap: adopt the music picker for a familiar unified picking experience, and lean on
the catalog (e.g. background music to enrich the app). Pointers to more MusicKit APIs
for browsing/modifying library content, and to cross-platform integration.
