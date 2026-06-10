---
title: "Explore advanced App Intents features for Siri and Apple Intelligence — Full Digest"
session: WWDC26 · 343
url: https://developer.apple.com/videos/play/wwdc2026/343/
duration: 24m
speakers: Antonio Cancio
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 343 — Explore advanced App Intents features for Siri and Apple Intelligence

## TL;DR

This is the "you've shipped App Intents, now make them feel native to Siri" talk.
It assumes you already know App Intents and App Schemas, then layers on the polish
APIs. Three big arcs, each demoed with one of three sample apps (CosmoTunes,
UnicornChat, CometCal):

1. **Shape the Siri conversation** — control what Siri *says* (`ProvidesDialog` /
   `IntentDialog` with `full` + `supporting` strings), ask mid-intent clarifying
   questions (`$param.requestValue`), control what Siri *shows* (entity
   `DisplayRepresentation` system-wide + per-action `ShowsSnippetView` SwiftUI
   snippets), teach Apple Intelligence your users' habits (`IntentDonationManager`),
   and get confirmation right on shared/public data (new `OwnershipProvidingEntity`).
2. **Make content discoverable** — three discovery paths: the Spotlight **semantic
   index** (`IndexedEntity` + `IndexedEntityQuery`), **structured search**
   (`IntentValueQuery` with a system-supplied input and multi-type `UnionValue`
   results), and **in-app search** (`.system.searchInApp`). Plus **onscreen
   awareness** so Siri resolves "play the third one."
3. **Reuse existing integrations** — attach `appEntityIdentifier(s)` to
   notifications, Now Playing, and AlarmKit so users can act on your entities
   ("reply…", "play the live version", "snooze it") wherever they meet them.

The throughline: **entity identifiers + display representations are a universal
language** that connects your content to every Siri surface, and you should only
customize where it actually helps.

---

## 1. Shape the Siri conversation

### Custom responses

Siri does the heavy lifting — natural-language understanding, action selection,
response. You shape the rest. Two levels:

- **Let Siri respond.** Return an empty `IntentResult` from `perform()`; Siri writes
  the response itself.
- **Match your app's voice.** Adopt `ProvidesDialog` and return an `IntentDialog`
  with two strings. `supporting` shows alongside UI; `full` is read aloud on
  voice-only devices (AirPods), so **`full` must stand on its own**.

```swift
@AppIntent(schema: .audio.addToPlaylist)
struct AddToPlaylistIntent {
    func perform() async throws -> some IntentResult & ProvidesDialog {
        return .result(
            dialog: IntentDialog(
                full: """
                      Added \(song.title) to the \
                      \(playlist.title) mix tape.
                      """,
                supporting: "Added"
            )
        )
    }
}
```

(CosmoTunes calls songs "tracks" and playlists "mix tapes" — the dialog carries that
voice.)

### Mid-intent clarifying questions

To ask a question *while the intent runs* (not before it's called), use a dialog
request inside `perform`. Here a timer intent asks for a label only when another
timer is already running, by requesting a value for its optional `label` parameter:

```swift
@AppIntent(schema: .clock.createTimer)
struct CreateTimerIntent {
    var duration: Duration
    var label: String?
    var isSleepTimer: Bool

    func perform() async throws -> some ReturnsValue<TimerEntity> {
        label = try await $label.requestValue(
            """
            You already have a timer running. \
            What should we call this one?
            """
        )
        return .result(value: timerEntity)
    }
}
```

There are other dialog-request kinds (choose from a list, ask for confirmation) — see
the sample app + docs. Guidance: ask clarifying questions **sparingly** to avoid
friction.

### Visual responses

Two distinct mechanisms — don't conflate them:

- **Entity `DisplayRepresentation`** (title, optional subtitle, optional image)
  defines how an entity looks/reads **everywhere**: responses, disambiguation,
  question-answering, Spotlight, Shortcuts. This is the foundational one.

```swift
@AppEntity(schema: .audio.song)
struct SongEntity {
    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(
            title: "\(title)",
            subtitle: "\(artistName)",
            image: artworkImage
        )
    }
}
```

- **`ShowsSnippetView`** styles a **specific action's** response with a custom SwiftUI
  view (e.g. `PlaylistSnippetView` in app colors). Add it to the return type and pass
  the view alongside the dialog.

```swift
func perform() async throws -> some IntentResult & ProvidesDialog & ShowsSnippetView {
    let view = PlaylistSnippetView(playlist: updatedEntity, tracks: updated.tracks)
    return .result(dialog: dialog, view: view)
}
```

Guidance: customize only where it helps, keep responses accurate and natural across
**all** platforms incl. voice-only, and keep snippets scaling across the ecosystem.

### Interaction donations

The premise: when users interact with your app through **Siri or Shortcuts**, the
system already knows. It **cannot** learn from actions taken in your app's **own UI**
unless you donate them. Each donation is a hint that a person did a specific action;
the system stores them as schema-conforming App Intents in a **temporary transcript**
that gives Siri context.

Pattern shown in UnicornChat: the `ConversationView` and the `SendMessageIntent` share
a `sendMessage` helper. Add a `donateIntent` flag so the helper donates **only when
called from the UI** (Siri paths are already known), then build the intent + result
and donate:

```swift
@ModelActor
actor ModelManager {
    func sendMessage(_ /* ... */, donateIntent: Bool = false) async throws -> [Message.ID] {
        if donateIntent {
            let intent = SendMessageIntent()
            intent.destination = .recipients(conversation.recipients.map(\.entity))
            let result = messages.map(\.entity)
            Task {
                try await IntentDonationManager.shared.donate(
                    intent: intent,
                    result: .result(value: result)
                )
            }
        }
    }
}
```

Two payoffs:

- **Preference learning** — after frequent in-app messaging to a contact, "Send a
  message to <contact> from the Home Screen" can infer UnicornChat.
- **Ongoing-activity awareness** — donations keep Siri aware of activities users might
  start/stop by voice. Applies to **Maps** (`NavigationSession` start/stop — start
  navigation in the app, later say "add a stop") and **Clock** (stopwatch
  start/stop/pause/lap).

Rule: donations must reflect **real** behavior; **excessive donations get ignored**.

### Confirmations and entity ownership

Before calling your intent, Siri may confirm. Confirmation **protects against
unintended side effects — a known LLM risk** — and matters most for intents with
meaningful side effects on your data or the outside world. Siri **auto-confirms**
those.

It matters even more for **shared/public** content. By default Siri assumes entities
are **private** and may skip confirmation. Conform shareable entities to the new
`OwnershipProvidingEntity` protocol and report ownership state so Siri confirms
appropriately:

```swift
@AppEntity(schema: .calendar.event)
struct EventEntity: OwnershipProvidingEntity {
    var ownership: EntityOwnership {
        attendees.isEmpty ? .unknown : .shared   // also .public
    }
}
```

- Only add the protocol to entities that can actually be shared/made public.
- **Keep ownership current** — it's read whenever the system requests the entity.
- Your **display representations** double as the confirmation visuals.
- Deeper trust/risk material: **"Secure your app: Mitigate risks to agentic features."**

---

## 2. Make content discoverable

Three discovery paths: semantic index, structured search, in-app search — then
onscreen awareness on top.

### Semantic index (IndexedEntity)

For content available **locally** (CosmoTunes playlists). Adopt `IndexedEntity` and
push entities into Spotlight's semantic index:

```swift
func indexPlaylist(_ playlist: Playlist) async throws {
    let entity = PlaylistEntity(playlist: playlist)
    try await CSSearchableIndex(name: indexName).indexAppEntities([entity])
}
```

- Enables "Play my WWDC playlist in CosmoTunes" and surfaces entries in the Spotlight
  search UI.
- Depending on the App Intents **domain**, indexing yields **semantic** search —
  meaning, not just exact keywords.
- **Keep it fresh:** index on add, update when key props change (especially those used
  in the display representation), delete on removal.
- Support reindexing by adopting the new **`IndexedEntityQuery`** (skip it if you
  already reindex via Core Spotlight–level APIs).

### Structured search (IntentValueQuery)

When content is **large, server-side, or fast-changing** to index ahead of time. (In
the demo: playlists are indexed, songs are not.) `IntentValueQuery` is like
`EntityQuery`, but (a) the system passes a **structured search input**, and (b) you can
**return more than one entity type**.

```swift
struct AudioIntentValueQuery: IntentValueQuery {
    func values(for input: AudioSearch) async throws -> [AudioEntity] {
        switch input.criteria {
        case .searchQuery(let query):
            return try await searchResults(for: query)
        case .unspecified:
            return try await likedSongResults()
        // ... also a .url case
        }
    }
}
```

- `AudioEntity` is a **`UnionValue`** covering both songs and playlists.
- `AudioSearch.criteria` cases: **`.searchQuery`** (relevant part of the utterance),
  **`.unspecified`** ("Play CosmoTunes" → jump into previously liked songs), and
  **`.url`** ("Play that playlist Glow sent me" → a link from your app).
- `IntentPerson` and other system types may also be supported as inputs.

### In-app search (.system.searchInApp)

Sometimes users want to **find**, not act. Default Siri behavior shows an entity result
list, but you can route the query into **your own** search UI by adopting the
`.system.searchInApp` schema. (This is the iOS 17 `.system` search, **renamed**; part
of the System App Schema domain; works regardless of which domains you adopt or whether
you index.)

```swift
@AppIntent(schema: .system.searchInApp)
struct SearchAudioLibraryIntent {
    var criteria: StringSearchCriteria
    func perform() async throws -> some IntentResult {
        navigation.searchText = criteria.term
        navigation.selectedTab = .library
        return .result()
    }
}
```

### Onscreen awareness

Spotlight + structured search let Siri reason about content **by name**. Onscreen
awareness lets it resolve **deixis** — "play the third one," "that conversation" —
without naming. Out of the box Siri only sees the **pixels' text**, so it can't act on
tracks shown or describe an artist not currently on screen. These APIs tell Siri
**what entities are on screen and where**.

Start with two; scale to four:

```swift
// (a) Single primary entity — NSUserActivity
.userActivity("cosmotunes.nowPlaying", isActive: playback.currentTrack) { activity in
    activity.title = playback.currentTrack?.title
    activity.appEntityIdentifier = EntityIdentifier(
        for: SongEntity.self, identifier: playback.currentTrack.id)
}

// (b) One among many — View Entity annotation
.appEntityIdentifier(
    EntityIdentifier(for: AlbumEntity.self, identifier: session.id.uuidString))

// (c) Lists/collections — Collection annotation
.appEntityIdentifier(forSelectionType: GeneratedTrack.ID.self) { trackID in
    EntityIdentifier(for: SongEntity.self, identifier: trackID)
}
```

- **`NSUserActivity`** for a screen dedicated to one item (NowPlayingView).
- **View Entity annotation** (`.appEntityIdentifier`) when an entity is one of several
  (AlbumView, where album + tracks are both visible).
- **Collection annotation** (`.appEntityIdentifier(forSelectionType:)`) for lists:
  avoids per-row annotations, lets the system fetch IDs **lazily**, and (key) lets Siri
  discover entities that were **selected then scrolled off** — per-row annotations
  vanish when the view leaves the hierarchy.
- **Custom canvas annotation** for non-standard subviews (the retro PianoRollView).
- **UIKit/AppKit** support all of it: `AppEntityAnnotatable`,
  `UICollectionViewAppIntentsDataSource`, `appEntityUIElementProvider`. These also power
  contextual menu items — see **"Modernize your UIKit app."**

**Performance angle:** when many entities are on screen, Siri must resolve them fast or
it'll clarify / play the wrong thing / get abandoned. Implement a
**display-representation query** so Siri can pull just the **text** rep and skip a full
DB fetch:

```swift
func displayRepresentations(
    for identifiers: [PlaylistEntity.ID],
    requestedComponents: DisplayRepresentation.Components = .text
) async throws -> [PlaylistEntity.ID: DisplayRepresentation] { /* ... */ }
```

---

## 3. Leverage existing integrations

Your app already plugs into system surfaces — attach entities there so they become a
**universal language** linking notifications and time-sensitive events to your content.
Three integrations, **one pattern** (entity annotations), enabling "play the live
version," "reply…", and "snooze it":

```swift
// (a) Notifications — reply to an announced notification on AirPods
content.appEntityIdentifiers = [
    EntityIdentifier(for: MessageEntity.self, identifier: message.id)
]

// (b) Now Playing — most specific → least specific
content.appEntityIdentifiers = [
    EntityIdentifier(for: SongEntity.self, identifier: track.id),
    EntityIdentifier(for: ArtistEntity.self, identifier: track.session.artistName),
    EntityIdentifier(for: PlaylistEntity.self, identifier: currentPlaylist.id),
]

// (c) AlarmKit — act on a firing alarm/timer
AlarmManager.AlarmConfiguration<CosmoTunesAlarmMetadata>.alarm(
    schedule: schedule, attributes: attributes,
    appEntityIdentifier: EntityIdentifier(for: AlarmEntity.self, identifier: alarm.id),
    stopIntent: DismissAlarmIntent(), secondaryIntent: SnoozeAlarmIntent(), sound: sound)
```

- Notifications: set `appEntityIdentifiers` on `UNMutableNotificationContent` (note the
  plural — an array). Now Playing: set it on the `MusicContent` inside the app's
  `MediaSessionRepresentable`. AlarmKit: single `appEntityIdentifier` on
  `AlarmConfiguration`.
- **Hard constraint:** these three annotation APIs require **persistent** entities —
  **`TransientAppEntity` is not allowed**, because transient entities have no persistent
  identifier.

---

## Next steps (recommended adoption order)

1. Customize entity **display representations** (used everywhere).
2. Add entities to the **semantic index** and keep it current.
3. Add **`IntentValueQuery`** + **in-app search**.
4. **Annotate** views, activities, and existing system integrations.
5. Finally, **donate UI interactions** for personalization.

See the three sample projects and **"Code-Along: Make your app available to Siri."**
