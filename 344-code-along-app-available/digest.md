---
title: "Code-along: Make your app available to Siri — Full Digest"
session: WWDC26 · 344
url: https://developer.apple.com/videos/play/wwdc2026/344/
duration: 24m
speakers: Justin Kang
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 344 — Code-along: Make your app available to Siri

## TL;DR

A hands-on code-along that makes an existing SwiftUI calendar app, **CometCal**,
controllable by Siri — no training phrases, no NLP. The whole thing rests on **App
Intents + App Schemas**: you describe your content and actions in vocabulary Siri
already knows (the *calendar domain*), and Siri handles language, clarification,
confirmation, and disambiguation for free.

The build is two halves:

1. **Understand content** — three schematized entities (`CalendarEntity`,
   `AttendeeEntity`, `EventEntity`) built from `calendar_*` Xcode snippets. Pick
   `IndexedEntity` (donated to Spotlight for semantic match) vs `TransientAppEntity`
   (no id/query/index). Add an `OpenIntent` so taps navigate to the right screen, and
   two view modifiers (`.appEntityIdentifier`, `.userActivity`) for **onscreen
   awareness** so Siri understands "this event" / "that third event."
2. **Act on content** — three schematized intents (`createEvent`, `updateEvent`,
   `deleteEvent`). The standout details: `IntentParameter.valueState` to tell
   *change* vs *explicitly clear* vs *not in the request* apart on optional update
   parameters, and `ShowsSnippetView` to replace Siri's default card with your own
   SwiftUI view.

Repeated message: "three structs and a few code snippets" to go from screen-only to
fully voice-piloted.

---

## 1. The plan — App Intents + App Schemas

Siri is powered by Apple Intelligence; apps plug in through the **App Intents**
framework so their content and actions appear in Siri and other system experiences.

**App Schemas** are the key idea: they describe your entities, the parameters of your
actions, and the outputs in terms Siri *already understands* — so there are **no
training phrases and no NLP on your end**. Schemas are grouped into **App Schema
Domains**; this talk lives entirely in the **calendar domain** (events, calendars,
attendees, and the actions on them).

Two companion talks are named up front:
- **"Build intelligent Siri experiences with App Schemas"** — the concepts.
- **"Get to know App Intents"** — fundamentals (intents, entities, queries).

The sample is **CometCal**, a SwiftUI calendar app "with a cosmic twist," downloadable
from the Apple Developer site. The two goals: make Siri (a) understand the content and
answer questions, and (b) take actions like creating/updating/deleting events.

## 2. CalendarEntity — the first schematized entity

CometCal already has a SwiftData `CalendarModel`. The goal is an app entity that
represents it via App Schemas.

Workflow:
- New file `CalendarEntity.swift`, `import AppIntents`.
- Type `calendar_` in the editor — **Xcode autocompletes every schema in the calendar
  domain**. Pick `calendar_calendar`; the snippet drops in the `@AppEntity` macro,
  properties, a `DisplayRepresentation`, and query stubs. It's now a *schematized
  entity* Siri can reason over.
- Set `id` to `UUID` to match the data model.
- Conform to **`IndexedEntity`** to enable matching by *meaning*, not just text. Once an
  entity is **donated** to the Spotlight index, Siri can resolve it by name, property,
  or context — with no custom property query.
- Add conversion helpers: an initializer mapping `CalendarModel → CalendarEntity`, and a
  convenience `.entity` property on `CalendarModel`.
- Add a **`@Dependency`** for the `CalendarManager` (CometCal's SwiftData data layer).
  `@Dependency` injects a shared resource registered once, rather than new instances.
- `CalendarManager` is main-actor isolated → annotate the query `@MainActor`.
- Implement the `EntityQuery` lookup-by-id method. Then also conform to
  **`EnumerableEntityQuery`** and add `allEntities()` — needed later so Siri can offer
  available calendars as options when creating events.
- `DisplayRepresentation`: title = calendar title, image = a system calendar icon.

The piece "easy to miss": `IndexedEntity` only defines the *shape* — entities still
need to be **donated**. In `CalendarManager`:
- A `CSSearchableIndex` instance is created in the initializer with a unique name.
- `createCalendar` / `updateCalendar` call `indexAppEntities(...)` before returning.
- `deleteCalendar` calls `deleteAppEntities(...)` with the entity's id and type.

```swift
import AppIntents

@AppEntity(schema: .calendar.calendar)
struct CalendarEntity: IndexedEntity {
    let id: UUID
    var title: String

    static var defaultQuery = CalendarEntityQuery()

    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(title: "\(title)", image: .init(systemName: "calendar"))
    }

    init(_ model: CalendarModel) { /* map from data model */ }
}

@MainActor
struct CalendarEntityQuery: EnumerableEntityQuery {
    @Dependency var calendarManager: CalendarManager

    func entities(for ids: [UUID]) async throws -> [CalendarEntity] {
        try calendarManager.calendars(for: ids).map(\.entity)
    }
    func allEntities() async throws -> [CalendarEntity] {
        calendarManager.allCalendars.map(\.entity)
    }
}
```

Demo: create a calendar "Lunar Orbit Log," swipe down to Spotlight, search it — it
appears with the calendar icon and title.

## 3. AttendeeEntity — TransientAppEntity, IntentPerson, schematized enums

Same workflow (`calendar_attendee` snippet), but **conforms to `TransientAppEntity`
instead of `IndexedEntity`** — deliberately. A transient entity is temporary, needs no
unique identifier, and isn't meant to be queried.

Why it fits: in CometCal an **attendee = a person's participation in a specific event**,
not the person themselves. The same person attends many events; indexing each
attendance separately would create duplicate Spotlight results. Attendees are always
reached *through* their event, so there's no independent lookup path. `TransientAppEntity`
makes that explicit — no query, no index to maintain.

New pieces:
- **`IntentPerson`** — the system's standard person type (name + contact info). Useful
  for sharing across apps, e.g. handing an attendee's email to the Mail app to draft a
  message.
- Two **schematized `@AppEnum`s**: `calendar_attendeeStatus` and `calendar_attendeeType`.
  The schema defines all possible cases; your app adopts the ones that apply. CometCal's
  model maps directly to the status cases; for type, it adds just a `person` case (all
  CometCal attendees are people). If an app uses different terminology, you map your
  model onto the schema's cases so Siri recognizes the shape.

Also notes a required schema property like a boolean for whether the attendance is optional.

## 4. EventEntity — the gravitational center

The central entity (`calendar_event` snippet), also an **`IndexedEntity`** with indexing
wired in `CalendarManager`. This is where the **semantic index shines**:
- "When is my crew lunch?" → Siri searches the **title**.
- "What events mention oxygen?" → Siri searches the **note content**.

People ask questions about their data; Siri answers directly. The schema is large, but
the same patterns apply — just more parameters.

Schema mechanics worth noting:
- **Required vs optional** properties are defined by the schema. Essentials (`title`,
  `startDate`) are straightforward. Optional properties the app doesn't use (`travelTime`,
  `virtualLocation`) can stay unset. Non-schema properties that exist on the data model
  (`isFavorite`) can also be added to the entity.
- **Composition**: the event's calendar is a `CalendarEntity`; its attendees are an
  `[AttendeeEntity]`. Siri understands these relationships through App Schemas.
- **Recurrence**: uses Foundation's `Calendar.RecurrenceRule`, converted to/from CometCal's
  simple frequency enum (daily/weekly/monthly/yearly).
- **Union values**: a property that can hold one of several types. Location is either a
  `PlaceDescriptor` (from the **GeoToolbox** framework) or a `String`; alarms are either a
  `Duration` or a `Date`. Implemented via code snippets.
- **Schematized event enums** like `EventEntityStatus` come complete from the snippets.

Demo: from the Meteor Shower Watch Party detail view, mid-conversation with Siri the user
asks "Is the Meteor Shower Party happening anytime soon?", "What's the weather like out
there?", and (typed) "When is the peak viewing time?" — all answered from app content,
"no custom natural language... just entities and schemas."

## 5. OpenIntent — navigate to the right screen

Problem: tapping a Siri/Spotlight result opened CometCal but only to the main screen.

Fix: a small **`OpenEventIntent`** conforming to the **`system.open`** schema. It takes an
`EventEntity` as its target and tells the `NavigationManager` to navigate to that event.
The system calls it whenever someone taps an event result in Spotlight/Siri or asks Siri
to open one. After this, taps land directly on the event detail view.

## 6. Onscreen awareness — two modifiers

So users can say "email the people in **this event**" without naming it. It takes exactly
two SwiftUI view modifiers:

- In `CalendarListView` (the events list): **`.appEntityIdentifier`**, passing an
  `EntityIdentifier` for each event entity. The system now knows which events are
  on screen while browsing — enabling "open **that third event**."
- In the event detail view: **`.userActivity`** with an `EntityIdentifier`. This tells the
  system one specific event is front and center, so Siri resolves "this event" to exactly
  the one being viewed.

Demos: "open that third event" (from the list) and "email the people in this event and ask
someone to bring chocolate and marshmallows" (from the detail view → hands attendees to Mail).

## 7. CreateEventIntent — Siri acts

Intents also use code snippets. Pick `calendar_createEvent`: it scaffolds the `@AppIntent`
macro, the schema, all required parameters, and a `perform` stub.

Steps:
- Fill in the parameter types (from `title` to `note`).
- Add a `@Dependency` for `CalendarManager`.
- Mark `perform()` `@MainActor`, set `EventEntity` as the return type.
- General pattern: **resolve parameters → perform the action → return an entity.** For
  create: extract the location from its union value, convert recurrence if provided, call
  `calendarManager.createEvent(...)`, return an `EventEntity`.

The payoff: because it conforms to an App Schema, **Siri handles interpreting language,
asking for clarification, and confirming details** — the developer writes none of that.

Demo: "create a new event in the Lunar Orbit Log" / "Call it Zero Gravity Yoga for June
15th, 8am." Siri resolves title, date, and time and adds the event.

## 8. UpdateEventIntent — the valueState subtlety

`calendar_updateEvent` mirrors create, but **most parameters are optional** (you might
change only one thing). The `event` parameter is what Siri resolves; everything else is
optional. Perform logic resolves each provided parameter, calls
`calendarManager.updateEvent(...)`, returns the updated event.

The important nuance: when `recurrence` is `nil`, does that mean *don't change it* or
*remove it*? A plain nil check can't tell. The `@AppIntent` macro wraps each property in an
**`IntentParameter`** exposing a **`valueState`**:

- **`.set`** with an actual value → a new value is provided (change it).
- **`.set`** with a `nil` value → explicitly cleared.
- **`.unset`** → the parameter isn't part of the request (leave it alone).

This applies to any optional parameter where clearing is a meaningful action.

```swift
switch $recurrence.valueState {
case .set(let rule):   // value present → change; rule == nil → explicit clear
    break
case .unset:           // not in the request → leave unchanged
    break
}
```

Demo: "move this to 10 in the evening" → confirm; "change this to repeat weekly and move it
to my Deep Space calendar" → confirm; "actually, do not repeat this event." The detail view
reflects every change.

## 9. Custom snippet views

By default Siri builds the result card from the `DisplayRepresentation`. **Snippet views**
replace that with a custom SwiftUI view:
- Add **`ShowsSnippetView`** to the perform method's return type.
- Return a custom view (`EventSnippetView`) taking the `EventEntity`.
- Same approach works for any intent that returns a result (e.g. create).

Demo: "push the crew lunch out by an hour" now renders CometCal's cosmic gradient accent,
dark blue background, event details, and a star icon — "the app's personality shining
through within Siri." Apple's caveat: keep it simple and lightweight.

## 10. DeleteEventIntent — the simplest

`DeleteEventIntent` is just the **event** plus an optional **span** for recurring events.
Perform finds and deletes it. **Siri automatically handles the confirmation dialog** before
removing anything, and **disambiguates** when more than one event matches.

Demo: "delete that party" → confirm; "delete the event happening June 9th" → "actually,
never mind." Siri confirms before deleting and disambiguates matches.

## Next steps (from the talk)

- Download the **CometCal** sample and explore the full implementation.
- Browse the **App Intents documentation** for all available schemas and domains.
- Write tests using the new **`AppIntentsTesting`** framework (separate video).
- Watch **"Explore advanced App Intents features for Siri and Apple Intelligence."**
