# Session 343 — Explore advanced App Intents features for Siri and Apple Intelligence

- **URL:** https://developer.apple.com/videos/play/wwdc2026/343/
- **Duration:** 24m
- **Speakers:** Antonio Cancio (App Intents team)

## Description

Advanced App Intents techniques to make your app's Siri and Apple Intelligence
experience feel polished, personal, and unmistakably yours. The talk covers three
areas: shaping the Siri conversation (custom responses, dialog requests, visuals,
interaction donations, confirmations), making content more widely available
(semantic index, structured search, in-app search, onscreen awareness), and
leveraging existing system integrations (notifications, Now Playing, alarms).
Demoed with three sample apps — CosmoTunes (music), UnicornChat (messaging), and
CometCal (calendar).

## Key topics

- Customize Siri responses: empty `IntentResult` (Siri speaks) vs. `ProvidesDialog` returning an `IntentDialog` with `full` + `supporting` strings (full string read on voice-only devices)
- Mid-intent clarifying questions via `$parameter.requestValue(...)` dialog requests inside `perform`
- Visual identity: entity `DisplayRepresentation` (title/subtitle/image) reused across responses, disambiguation, Spotlight, Shortcuts; per-action custom SwiftUI snippets via `ShowsSnippetView`
- Interaction donations via `IntentDonationManager` — donate UI interactions (system already knows Siri/Shortcuts ones) using schema-conforming intents so Apple Intelligence learns app preferences and tracks ongoing activities (Maps `NavigationSession`, Clock stopwatches). Donate accurately; excessive donations are ignored
- Confirmations: Siri auto-confirms meaningful side effects; new `OwnershipProvidingEntity` protocol tells Siri an entity is `.shared`/`.public`/`.unknown` so it confirms on shared/public content
- Semantic index: adopt `IndexedEntity`, index via `CSSearchableIndex.indexAppEntities`, keep fresh (add/update/delete), support reindexing with the new `IndexedEntityQuery`
- Structured search: `IntentValueQuery` receives a system-provided structured input (e.g. `AudioSearch` with `.searchQuery`/`.unspecified`/`.url` criteria) and can return multiple entity types via a `UnionValue`
- In-app search: adopt `.system.searchInApp` schema (the iOS 17 `.system` search, renamed) so Siri re-runs the search inside your own search UI
- Onscreen awareness: `NSUserActivity` (single primary item), View Entity annotation `.appEntityIdentifier` (one of many), collection annotation `.appEntityIdentifier(forSelectionType:)`, custom canvas annotation; all supported in SwiftUI, UIKit, AppKit
- Display-representation queries (`displayRepresentations(for:requestedComponents:)`) let Siri resolve onscreen entities from just the text rep, skipping a full DB fetch
- Existing integrations: `appEntityIdentifiers` on `UNMutableNotificationContent` and on Now Playing `MediaSessionRepresentable`/`MusicContent`; `appEntityIdentifier` on AlarmKit's `AlarmConfiguration`. Persistent entities only — no `TransientAppEntity`

## Related sessions to fetch (referenced in this talk)

- [ ] Secure your app: Mitigate risks to agentic features
- [ ] Modernize your UIKit app
- [ ] Code-Along: Make your app available to Siri
- [ ] (Fundamentals) App Intents / App Schemas intro sessions referenced at the start

## Chapter summary (Summary tab)

- **0:00 Introduction** — Antonio Cancio. Advanced techniques to make Siri / Apple Intelligence feel polished and personal. Agenda: shape the Siri conversation, improve content discovery, leverage existing integrations. Demoed with CosmoTunes, UnicornChat, CometCal.
- **1:59 Customize how Siri responds** — return an empty result to let Siri respond, or adopt `ProvidesDialog` and return an `IntentDialog` with `full` and `supporting` strings. Ask clarifying questions mid-intent with a dialog request (e.g. an optional timer label).
- **4:20 Visual responses** — an entity's `DisplayRepresentation` (title, subtitle, image) is used across responses, disambiguation, Spotlight, and Shortcuts; a custom SwiftUI snippet view (`ShowsSnippetView`) styles specific actions. Customize only where it helps; account for voice-only devices.
- **6:22 Interaction donations** — system interactions are known automatically, but UI interactions aren't, so donate them via `IntentDonationManager` (schema-conforming intents) so Apple Intelligence learns preferences and stays aware of ongoing activities (Maps navigation, Clock stopwatches). Donate accurately; excessive donations are ignored.
- **9:46 Confirmations and entity ownership** — Siri auto-confirms intents with meaningful side effects, especially on shared/public content. Conform shareable entities to the new `OwnershipProvidingEntity` protocol and keep ownership current; display representations are used as confirmation visuals.
- **11:59 Semantic index with IndexedEntity** — adopt `IndexedEntity` and index entities in Spotlight via `indexAppEntities` for meaning-based search. Keep the index fresh; support re-indexing with the new `IndexedEntityQuery`.
- **13:32 Structured search with IntentValueQuery** — for content too large, server-side, or fast-changing to index, use `IntentValueQuery`: the system passes a structured search input and you can return multiple entity types. CosmoTunes maps an `AudioSearch` to a `UnionValue` of songs and playlists.
- **15:27 In-app search** — adopt the `.system.searchInApp` schema (formerly `.system` search) so "Show me running playlists in CosmoTunes" re-runs Siri's search inside your own search UI, regardless of domains adopted or indexing.
- **16:22 Onscreen awareness** — connect what's visible to entities so Siri resolves "play the third one." Start with `NSUserActivity` (single primary item) and View Entity annotations (`appEntityIdentifier`, one of many); scale up with collection annotations (`forSelectionType:`) and custom canvas annotations; all supported in UIKit and AppKit. Enable display-representation queries so Siri resolves onscreen entities fast.
- **20:51 Leverage existing integrations** — attach entities to integrations you already use: `appEntityIdentifiers` on `UNMutableNotificationContent` (reply to announced notifications), on Now Playing via `MediaSessionRepresentable` ("play the live version"), and `appEntityIdentifier` on AlarmKit's `AlarmConfiguration` ("snooze it"). Persistent entities only, no transient entities.
- **23:30 Next steps** — start with display representations, then index entities and keep them current, add `IntentValueQuery` and in-app search, annotate views and existing integrations, and finally donate UI interactions. See the sample projects and "Code-Along: Make your app available to Siri."

## Code

See `code.md` — 13 snippets extracted from the Code tab.
