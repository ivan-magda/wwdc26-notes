---
title: "Meet the Now Playing framework — Full Digest"
session: WWDC26 · 312
url: https://developer.apple.com/videos/play/wwdc2026/312/
duration: 13m
speakers: Leo Formaggio
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 312 — Meet the Now Playing framework

## TL;DR

A new **NowPlaying** framework replaces the old now-playing plumbing (`MPNowPlayingInfoCenter` / `MPRemoteCommandCenter`) with a modern, **`@Observable`-driven, declarative** API. Three layers, escalating in scope:

1. **Media sessions** — conform an `@Observable` model to **`MediaSessionRepresentable`** (id, content, playback snapshot, commands, artwork), then wrap it in **`MediaSession(model)`**. The system observes the model and keeps the Lock Screen, Control Center, Dynamic Island, StandBy, and CarPlay in sync automatically. No manual "info center" pushes.
2. **Remote media sessions** — surface media playing on *other* devices (smart speakers, TVs your app controls). A **`RemoteMediaSessionExtension`** app extension is woken by **APNs** with the new state; it returns a model conforming to **`RemoteMediaSessionRepresentable`**, which adds a **`devices`** list (`MediaDevice` with volume capabilities) and an **`update(_:)`** hook.
3. **Media Sharing Extensions** — route media from iPhone to third-party speakers/TVs through the **system device picker**, with the protocol implementations living *outside* the app and managed by the system — so apps no longer embed each casting SDK.

The single demo throughout is the speaker's own ambient-sounds focus/relax app.

The headline for app developers: the integration is declarative and observation-driven. You describe *what* is playing; the framework handles *where* it shows up and *when* it refreshes.

---

## 1. The system now-playing experience

The system now-playing experience is the media UI that lives outside any single app: the **Lock Screen, Control Center, Dynamic Island**, **StandBy** (when the iPhone is charging and set down), and **CarPlay**. It exists on **all Apple platforms** — iPhone, **Apple Watch, Apple Vision Pro, Apple TV**. NowPlaying is the framework that bridges an app's playback into that experience.

## 2. Media sessions (local playback)

The model is a plain `@Observable` class that knows what's playing:

```swift
import Observation

@Observable
final class PlayerModel {
    let player: SoundPlayer
    var sound: Sound { player.currentSound }

    init(player: SoundPlayer) {
        self.player = player
    }
}
```

You make it legible to the system by conforming to **`MediaSessionRepresentable`** — described in the talk as "a contract between my app and the system." Five members carry the weight:

```swift
import NowPlaying

extension PlayerModel: MediaSessionRepresentable {
    var id: String { "ambient-sound-session" }

    var content: (any MediaContentRepresentable)? {
        GenericContent(
            id: sound.id,
            title: sound.name,
            subtitle: sound.description,
            type: .audio,
            duration: .live,
            artwork: Artwork(id: sound.id) { size in
                let data = try await self.artworkData(size: size)
                return try ArtworkRepresentation(data: data)
            }
        )
    }

    var playbackSnapshot: MediaPlaybackSnapshot? {
        MediaPlaybackSnapshot(state: player.isPlaying ? .playing() : .paused)
    }

    var commands: [MediaCommand] {[
        .play  { self.player.play() },
        .pause { self.player.pause() },
        .previous { self.player.previous() },
        .next  { self.player.next() }
    ]}
}
```

Member by member:

- **`id`** — a unique identifier for the session representation.
- **`content`** — describes what's playing. NowPlaying ships content-specific types — **`Music`, `Podcast`, `MovieContent`** — and a catch-all **`GenericContent`** (used here). `type` is **`.audio`** or **`.video`**. `duration` is **continuous/`.live`** for indefinite content (ambient sounds); for content with a defined length you also pass an **`elapsedTime`** in the snapshot.
- **`artwork`** — an `Artwork` built with an **async closure** that the system calls "whenever it needs an image at a specific size." Lazy and size-driven, so you only render the artwork the surface actually needs.
- **`playbackSnapshot`** — the current playback state (`.playing()` / `.paused`).
- **`commands`** — an array of `MediaCommand`s, each a closure the system invokes when the user taps the corresponding control on a system surface. Tap pause on the Lock Screen → your `.pause` closure runs → the button flips to the paused state. Same for play / next.

Conforming is only half of it. To connect to the system, construct a **`MediaSession`** with the model, in the same place you set up your audio engine:

```swift
import NowPlaying

struct PlayerController {
    let player: SoundPlayer
    let model: PlayerModel
    let session: MediaSession<PlayerModel>

    init() {
        self.player = SoundPlayer()
        self.model = PlayerModel(player: player)
        self.session = MediaSession(model)
    }
}
```

Once created, **`MediaSession` observes the model** and keeps the now-playing surfaces up to date automatically — no explicit refresh calls. (Reference article: *"Publishing Media Sessions."*)

## 3. Remote media sessions (content on other devices)

The second scenario: the app controls a **smart speaker** over a web server. The user picks "Living Room Speaker" from the app's device picker; the app talks to the speaker through the server to read state and send commands. To surface *that* content in the system now-playing experience, use the **remote media sessions** API, built on an **app extension + push notifications**.

### The two interaction directions

- **Speaker → iPhone (state updates):** speaker changes → server is notified → server uses **APNs** to push the new state to the iPhone → the system **launches the app extension** with the payload → the extension returns an updated session representation. (Reference: *"Setting up a remote notification server."*)
- **iPhone → speaker (commands):** user acts on iPhone system UI → system calls a **command handler in the app extension** → extension forwards to the server → server tells the speaker, which reacts.

### The app extension

```swift
import ExtensionFoundation
import NowPlaying

@main
final class SampleAppExtension: @MainActor RemoteMediaSessionExtension {
    var configuration: some AppExtensionConfiguration {
        RemoteMediaSessionExtensionConfiguration(extension: self)
    }

    var extensionPoint: AppExtensionPoint {
        AppExtensionPoint.Identifier(host: "com.apple.nowplaying", name: "remote-media")
    }

    func session(_ state: RemotePlayerState) async throws -> RemotePlayerModel {
        RemotePlayerModel(state: state)
    }
}
```

The extension conforms to **`RemoteMediaSessionExtension`**, configured with **`RemoteMediaSessionExtensionConfiguration`** and the **`remote-media`** extension point. The system calls **`session(_:)`** whenever it needs to interact with a remote representation (refresh UI or handle an interaction); you build your model from the incoming `RemotePlayerState` and return it.

### The remote model

```swift
import Observation

@Observable
@MainActor
final class RemotePlayerModel {
    let client: ServerClient
    var state: RemotePlayerState

    init(state: RemotePlayerState) {
        self.client = ServerClient(sessionID: state.sessionID)
        self.state = state
    }
}
```

It conforms to **`RemoteMediaSessionRepresentable`**, which the talk notes "feels very similar" to the local case — same `id`, `content`, `playbackSnapshot`, `commands` shape. Two differences:

1. **Commands are server-backed and async/throwing** — each closure sends a request to the server rather than poking a local player:

```swift
var commands: [MediaCommand] {[
    .play  { try await self.client.send(.play) },
    .pause { try await self.client.send(.pause) },
    .previous { try await self.client.send(.previous) },
    .next  { try await self.client.send(.next) }
]}
```

2. **Remote-only members — `devices` and `update(_:)`:**

```swift
var devices: [MediaDevice] {
    state.devices.map { device in
        MediaDevice(
            id: device.id,
            name: device.name,
            type: .speaker,
            capabilities: [
                .absoluteVolume(device.volume) { volume in
                    // send volume change to server
                }
            ]
        )
    }
}

func update(_ state: RemotePlayerState) {
    self.state = state
}
```

- **`devices`** tells the system which devices are playing in the session. Each **`MediaDevice`** needs a unique id **stable across sessions**, a `name`, a `type` (e.g. `.speaker`), and **`capabilities`** — here `.absoluteVolume`, which surfaces a volume slider in Control Center and fires its closure with the new level when the user drags it.
- **`update(_:)`** is called when a push notification arrives with new state (e.g. the content on the speaker changed). It writes the new `state`; because the model is `@Observable`, **NowPlaying detects the change and updates the system automatically** — same observation mechanism as the local path.
- **`RemotePlayerState`** is a developer-defined struct conforming to **`RemoteMediaSessionAttributes`**; it doubles as the server state *and* the APNs push payload.

(Reference: *"Publishing remote media sessions."*)

## 4. Media Sharing Extensions

The third piece is broader than now-playing display: it's about **playing media from iPhone to other speakers and TVs through a unified system interface**.

- Lets your app use the **system device picker** for **all the media protocols your app supports**, and the selection is reflected on system surfaces like Control Center.
- **The shift:** traditionally, supporting a casting/media protocol meant **embedding its SDK into your app bundle**. With Media Sharing Extensions, the **protocol implementations live outside your app and are managed by the system**. Your app focuses on the media content, not the playback technology.
- **Forward-compatible:** as more protocols become available, apps built on Media Sharing Extensions can use them **without adopting another SDK**.
- It has its own dedicated session ("Learn more about Media Sharing Extensions") and reference article ("Routing media to third-party devices").
