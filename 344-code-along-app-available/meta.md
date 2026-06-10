# Session 344 — Code-along: Make your app available to Siri

- **URL:** https://developer.apple.com/videos/play/wwdc2026/344/
- **Duration:** 24m
- **Speakers:** Justin Kang (engineer, Swift Intelligence Frameworks team)
- **Format:** Code-along (downloadable CometCal sample project)

## Description

A step-by-step code-along that takes an existing SwiftUI calendar app, CometCal,
and makes it available to Siri using App Intents and App Schemas. The talk shows
how to teach Siri to *understand* an app's content (calendars, events, attendees)
and *act* on it (create, update, delete events) — all through conversation, with no
training phrases and no custom NLP. The opening "picnic" scenario demonstrates the
end goal: Siri searching events, updating times with confirmation, texting
attendees, and answering questions about event notes and locations.

## Key topics

- **App Schemas** describe content and actions in terms Siri already understands;
  organized into **domains** (here, the calendar domain). No training phrases, no NLP.
- **Schematized `@AppEntity`** types built from `calendar_*` code snippets in Xcode
  autocomplete: `CalendarEntity`, `AttendeeEntity`, `EventEntity`.
- **`IndexedEntity`** (semantic match via Spotlight donation) vs **`TransientAppEntity`**
  (no id, no query, no index) — choosing the right one per entity.
- **Donation** to Spotlight via `CSSearchableIndex` + `indexAppEntities` / `deleteAppEntities`.
- **`@Dependency`** injection of the shared `CalendarManager` data layer; `@MainActor` queries.
- **`EntityQuery` / `EnumerableEntityQuery`** — lookup by id vs `allEntities()` for options.
- **Composition** — `EventEntity` holds a `CalendarEntity` and `[AttendeeEntity]`.
- **Schema details** — `IntentPerson`, schematized `@AppEnum`s, `Calendar.RecurrenceRule`,
  union values (location: `PlaceDescriptor` from GeoToolbox or `String`; alarms: `Duration` or `Date`).
- **`OpenIntent`** (`system.open` schema) to navigate straight to an event's detail view.
- **Onscreen awareness** — `.appEntityIdentifier` (list) and `.userActivity` (detail) modifiers
  let Siri resolve "this event" / "that third event" without a title.
- **Schematized intents** — `calendar_createEvent`, `calendar_updateEvent`, `calendar_deleteEvent`;
  Siri handles language, clarification, confirmation, and disambiguation.
- **`IntentParameter.valueState`** — `.set(value)` vs `.set(nil)` (clear) vs `.unset` (not in request).
- **Custom snippet views** — `ShowsSnippetView` + a SwiftUI view to replace Siri's default result card.

## Related sessions to fetch (referenced in this talk)

- [ ] Build intelligent Siri experiences with App Schemas (companion — concepts)
- [ ] Get to know App Intents (fundamentals — intents, entities, queries)
- [ ] Explore advanced App Intents features for Siri and Apple Intelligence
- [ ] (Testing CometCal with the new `AppIntentsTesting` framework — title not stated)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Justin Kang previews the goal via a picnic scenario: Siri
  searches events, updates times with confirmation, texts attendees, and answers
  questions, all through conversation.
- **1:43 App Schemas and the plan** — apps integrate with Apple Intelligence through
  App Intents; App Schemas describe content/actions, organized into domains (calendar).
  Introduces CometCal and the two goals: understand content, perform actions.
- **3:44 Build the CalendarEntity** — schematized `@AppEntity` from `calendar_calendar`;
  id = UUID, conform to `IndexedEntity`, `@Dependency` + `@MainActor` query
  (`EnumerableEntityQuery` with `allEntities()`), display representation, donate to
  Spotlight via `indexAppEntities` / `deleteAppEntities`.
- **8:00 Build the AttendeeEntity** — from `calendar_attendee`, conforming to
  `TransientAppEntity` (no id/query/index). Introduces `IntentPerson` and two
  schematized `@AppEnum`s (`calendar_attendeeStatus`, `calendar_attendeeType`).
- **10:30 Build the EventEntity** — the central `IndexedEntity` (`calendar_event`) where
  the semantic index shines for title/note questions. Composes `CalendarEntity` and
  `[AttendeeEntity]`, handles `Calendar.RecurrenceRule`, union values, status/span enums.
- **14:34 Open events with OpenIntent** — small `OpenEventIntent` (`system.open` schema)
  takes an `EventEntity` and tells `NavigationManager` to navigate to it.
- **15:30 Onscreen awareness** — `.appEntityIdentifier` on the list and `.userActivity`
  on the detail view let Siri resolve "this event" / "that third event".
- **17:18 Create events with Siri** — `CreateEventIntent` from `calendar_createEvent`;
  fill parameter types, add `@MainActor @Dependency`, resolve schema parameters in
  `perform()`, return an `EventEntity`. Siri handles language/clarification/confirmation.
- **19:24 Update events** — `UpdateEventIntent` (`calendar_updateEvent`) mirrors create
  but parameters are optional; `IntentParameter.valueState` distinguishes set/clear/unset.
- **21:30 Custom snippet views** — add `ShowsSnippetView` to the return type, return a
  custom SwiftUI `EventSnippetView` (cosmic gradient, star icon).
- **22:30 Delete events** — `DeleteEventIntent`, just the event plus an optional span for
  recurring events; Siri handles confirmation and disambiguation.
- **23:35 Next steps** — download CometCal, browse App Intents docs for all schemas/domains,
  write tests with `AppIntentsTesting`, watch the advanced App Intents session.

## Code

This session has no Code tab. See `code.md` for the concrete APIs named in the
transcript (reconstructed, not copy-paste-ready snippets).
