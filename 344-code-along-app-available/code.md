# Code samples — Session 344

_No Code tab for this session._ This is a code-along driven by Xcode code snippets
(typed via autocomplete on screen), so Apple shipped no separate Code tab. The
items below are the concrete APIs and patterns named in the transcript, with
illustrative (reconstructed) Swift — verify exact signatures against the downloadable
**CometCal** sample and the App Intents documentation before quoting.

## Spoken APIs

App Schemas / domains
- `calendar` domain schemas surfaced in Xcode autocomplete by typing `calendar_`
- Entity schemas: `calendar_calendar`, `calendar_attendee`, `calendar_event`
- Enum schemas: `calendar_attendeeStatus`, `calendar_attendeeType`, event status enum (`EventEntityStatus`)
- Intent schemas: `calendar_createEvent`, `calendar_updateEvent`, `calendar_deleteEvent`
- Open schema: `system.open`

Entities & queries
- `@AppEntity` macro
- `IndexedEntity` (semantic index via Spotlight donation)
- `TransientAppEntity` (no id, no query, no index — for the attendee)
- `EntityQuery` (`entities(for: ids)` style lookup by id)
- `EnumerableEntityQuery` + `allEntities()`
- `DisplayRepresentation` (title + image)
- `@Dependency` (inject shared `CalendarManager`)
- `@MainActor` on the query / perform method

Donation / Spotlight
- `CSSearchableIndex` (unique name per app)
- `indexAppEntities(...)` to donate; `deleteAppEntities(identifiers:entityType:)` to remove

Schema value types
- `IntentPerson` (system person type: name + contact info, e.g. hand off to Mail)
- `@AppEnum` (schematized enums)
- `Calendar.RecurrenceRule` (Foundation) ↔ app's frequency enum (daily/weekly/monthly/yearly)
- Union value — location: `PlaceDescriptor` (GeoToolbox) or `String`; alarms: `Duration` or `Date`

Intents & actions
- `@AppIntent` macro; `perform()` returning an `EventEntity`
- `IntentParameter` and its `valueState`: `.set(value)` / `.set(nil)` (clear) / `.unset`
- `ShowsSnippetView` on the return type + a custom SwiftUI snippet view (`EventSnippetView`)
- `DeleteEventIntent` with an optional recurrence `span`

Onscreen awareness (SwiftUI view modifiers)
- `.appEntityIdentifier(...)` on the list, passing an `EntityIdentifier` per event
- `.userActivity(...)` with an `EntityIdentifier` on the detail view

## Sketch — schematized entity (CalendarEntity)

```swift
import AppIntents

// Built from the calendar_calendar snippet, then customized:
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

## Sketch — donating to Spotlight (CalendarManager)

```swift
let searchableIndex = CSSearchableIndex(name: "dev.cometcal.index")

func createCalendar(...) throws -> CalendarModel {
    // ...create...
    try await searchableIndex.indexAppEntities([calendar.entity])
    return calendar
}

func deleteCalendar(_ calendar: CalendarModel) throws {
    // ...delete...
    try await searchableIndex.deleteAppEntities(
        identifiers: [calendar.id], entityType: CalendarEntity.self)
}
```

## Sketch — update intent's valueState subtlety

```swift
// In UpdateEventIntent.perform(), distinguishing the three cases:
switch $recurrence.valueState {
case .set(let rule):          // .set with a value  → change it
    // rule may itself be nil → .set(nil) means explicitly clear
case .unset:                  // not part of the request → leave unchanged
    break
}
```

## Sketch — custom Siri result card

```swift
func perform() async throws -> some IntentResult & ReturnsValue<EventEntity> & ShowsSnippetView {
    let event = try await calendarManager.updateEvent(...)
    return .result(value: event.entity) {
        EventSnippetView(event: event.entity)   // cosmic gradient, star icon
    }
}
```

> These sketches are reconstructed from the spoken walkthrough to capture the shape
> of each step; the exact macro arguments and protocol signatures should be confirmed
> against the CometCal sample download.
