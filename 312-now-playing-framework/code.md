# Code samples — Session 312

Extracted from the Code tab. Six snippets, mapped to the talk's three sections.

## 1:56 — PlayerModel (the @Observable model)

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

## 2:06 — Conforming to MediaSessionRepresentable

```swift
import NowPlaying

extension PlayerModel: MediaSessionRepresentable {
    var id: String { "ambient-sound-session" }

    var content: (any MediaContentRepresentable)? {
        return GenericContent(
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
        MediaPlaybackSnapshot(
            state: player.isPlaying ? .playing() : .paused
        )
    }

    var commands: [MediaCommand] {[
        .play { self.player.play() },
        .pause { self.player.pause() },
        .previous { self.player.previous() },
        .next { self.player.next() }
    ]}
}
```

## 4:31 — Connecting to the system with MediaSession

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

## 6:42 — RemoteMediaSessionExtension (app extension)

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

## 7:23 — RemotePlayerModel (the @Observable remote model)

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

## 8:49 — Conforming to RemoteMediaSessionRepresentable (devices, update, server-backed commands)

```swift
import NowPlaying

extension RemotePlayerModel: @MainActor RemoteMediaSessionRepresentable {
    var id: String { state.sessionID }

    var content: (any MediaContentRepresentable)? {
        GenericContent(
            id: state.sound.id,
            title: state.sound.name,
            subtitle: state.sound.description,
            type: .audio,
            duration: .live,
            artwork: Artwork(id: state.sound.id) { size in
                let data = try await self.artworkData(size: size)
                return try ArtworkRepresentation(data: data)
            }
        )
    }

    var playbackSnapshot: MediaPlaybackSnapshot? {
        MediaPlaybackSnapshot(
            state: state.isPlaying ? .playing() : .paused
        )
    }

    var commands: [MediaCommand] {[
        .play { try await self.client.send(.play) },
        .pause { try await self.client.send(.pause) },
        .previous { try await self.client.send(.previous) },
        .next { try await self.client.send(.next) }
    ]}

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
}
```

---

## Useful API facts surfaced by the code

- **Local path:** `@Observable` model → conform to `MediaSessionRepresentable` → wrap in `MediaSession(model)`. `MediaSession` observes the model and keeps now-playing surfaces in sync automatically.
- **`MediaSessionRepresentable` surface:** `id: String`, `content: (any MediaContentRepresentable)?`, `playbackSnapshot: MediaPlaybackSnapshot?`, `commands: [MediaCommand]`.
- **Content:** `GenericContent(id:title:subtitle:type:duration:artwork:)`; content-specific alternatives `Music`, `Podcast`, `MovieContent`. `type` is `.audio` / `.video`.
- **Playback state:** `MediaPlaybackSnapshot(state:)` with `.playing()` / `.paused`; defined-duration content also passes `elapsedTime`.
- **Commands:** `[MediaCommand]` built from `.play`, `.pause`, `.previous`, `.next`, each with a closure (sync locally; `async`/throwing for remote server calls).
- **Artwork:** `Artwork(id:)` with an async closure `(size) -> ArtworkRepresentation`, called by the system on demand for each requested size.
- **Remote path:** an `@main RemoteMediaSessionExtension` (with `RemoteMediaSessionExtensionConfiguration` + the `remote-media` extension point) returns a model conforming to `RemoteMediaSessionRepresentable`. Adds `devices: [MediaDevice]` and `update(_:)`.
- **`MediaDevice`:** `id` (stable across sessions), `name`, `type` (e.g. `.speaker`), `capabilities` (e.g. `.absoluteVolume(_:){ … }`).
- **State/push:** `RemotePlayerState` conforms to `RemoteMediaSessionAttributes`; it is both the server state and the APNs push payload. `update(_:)` runs on push receipt; observation propagates the change to the system.
- **Duration mismatch:** the Code tab uses `duration: .live`; the spoken talk says `.continuous`. See `digest.md` Open Questions.
