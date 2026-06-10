# Code samples — Session 254

Extracted from the Code tab. Labels derived per snippet; minor OCR artifacts from
the extraction (e.g. a smart-bracket and an `isplaying` typo) are noted inline.

## Apple Music subscription offer button

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

## Observing subscription status

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

## Music picker with single selection

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

var body: some View {
    VStack {
        if let subscription, subscription.canBecomeSubscriber {
            musicSubscriptionButton
        }
        musicPickerButton
    }
}
```

## Displaying current artwork with ArtworkImage

```swift
@State var queue = ApplicationMusicPlayer.shared.queue

var body: some View {
    VStack {
        if let artwork = queue.currentEntry?.artwork {
            ArtworkImage(artwork, width: 200, height: 200)
        } else {
            // Placeholder artwork
            RoundedRectangle(cornerRadius: 16)
                .fill(.quaternary)
                .frame(width: 200, height: 200)
        }
    }
}
```

## Showing current song title and subtitle

```swift
@State var queue = ApplicationMusicPlayer.shared.queue

var body: some View {
    VStack {
        // ...
        if let currentSong = queue.currentEntry {
            Text(currentSong.title)
                .font(.title3.bold())

            if let subtitle = currentSong.subtitle {
                Text(subtitle)
                    .font(.subheadline)
                    .foregroundStyle(.secondary)
            }
        }
    }
}
```

## Play / pause control driven by playbackStatus

```swift
let player = ApplicationMusicPlayer.shared
@State var state = ApplicationMusicPlayer.shared.state

var isPlaying: Bool {
    state.playbackStatus == .playing
}

var playPause: some View {
    Button(
        isPlaying ? "Pause" : "Play",
        systemImage: isPlaying ? "pause.fill" : "play.fill"   // extraction had "isplaying"
    ) {
        if isPlaying {
            player.pause()
        } else {
            Task {
                try await player.play()
            }
        }
    }
}
```

## Skip to previous / next entry

```swift
let player = ApplicationMusicPlayer.shared

var controls: some View {
    HStack {
        Button("Back", systemImage: "backward.fill") {
            Task {
                try await player.skipToPreviousEntry()
            }
        }
        // ...
        Button("Next", systemImage: "forward.fill") {
            Task {
                try await player.skipToNextEntry()
            }
        }
    }
}
```

## Catalog resource request with content equivalency

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
