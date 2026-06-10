# Code samples — Session 295

Extracted from the session's Code tab. Timecodes map to the Summary-tab
chapters.

## 2:01 — First test: create a calendar and assert on the returned entity

```swift
import AppIntentsTesting

func testCreateCalendar() async throws {
    let definitions = IntentDefinitions(bundleIdentifier: "com.example.apple-samplecode.CometCal")
    let createCalendar = definitions.intents["CreateCalendarIntent"]
    let result = try await createCalendar.makeIntent(
        name: "Occupy Saturn",
        color: "red"
    ).run()
    XCTAssertEqual(try result.value.title, "Occupy Saturn")
}
```

## 9:39 — Testing an entity string query (the failing test)

```swift
// Testing Entity string queries
func testEventStringQuery() async throws {
    let results = try await eventEntityDefinition
        .entities(matching: "Cosmic Ray")

    XCTAssertEqual(results.count, 1)
    XCTAssertEqual(try results[0].title, "Cosmic Ray Calibration")
}
```

## 9:39 — Implementing `entities(matching:)` to make it pass

```swift
// Updated query implementation
struct EventEntityQuery: EntityStringQuery {
    func entities(for identifiers: [EventEntity.ID]) async throws -> [EventEntity] {

    }

    func suggestedEntities() async throws -> [EventEntity] {

    }

    func entities(matching string: String) async throws -> [EventEntity] {
        try calendarManager.fetchEvents()
            .filter { $0.title.localizedCaseInsensitiveContains(string) }
            .map(\.entity)
    }
}
```

## 13:49 — Chaining two intents (create, then update)

```swift
// Test event creation followed by update
func testCreateAndUpdateEvent() async throws {
    let createResult = try await createEventDefinition.makeIntent(
        title: "Asteroid Dodgeball Practice",
        startDate: Date(),
        isAllDay: false,
        calendar: "Deep Space"
    ).run()

    XCTAssertEqual(try createResult.value.title, "Asteroid Dodgeball Practice")

    let updateResult = try await updateEventDefinition.makeIntent(
        title: "Asteroid Dodgeball Rules Overview",
        event: createResult.value
    ).run()

    XCTAssertEqual(try updateResult.value.title, "Asteroid Dodgeball Rules Overview")
}
```

## 16:27 — Test-only intent

```swift
// Test-only intent: SeedSampleEventsIntent
#if DEBUG
struct SeedSampleEventsIntent: AppIntent {
    static let isDiscoverable = false

    func perform() async throws -> some IntentResult {
        // Create known list of events
        return .result()
    }
}
#endif
```

## 18:22 — Testing Spotlight indexing (regression test)

```swift
// Testing Spotlight indexing
func testNewEventIndexedInSpotlight() async throws {

    let before = try await eventEntityDefinition.spotlightQuery("Supernova Viewing Party")
    XCTAssertTrue(before.isEmpty, "Event should not exist in Spotlight yet")

    // ... Create "Supernova Viewing Party" Event

    let after = try await eventEntityDefinition.spotlightQuery("Supernova Viewing Party")
    XCTAssertEqual(after.count, 1)
    XCTAssertEqual(try after[0].title, "Supernova Viewing Party")
}
```

## 20:56 — Testing view annotations (what Siri sees on screen)

```swift
// Testing view annotations
func testEventViewAnnotation() async throws {
    try await openEventDefinition.makeIntent(target: "Morning Launch Briefing").run()

    // Confirm correct event page
    let app = XCUIApplication()
    let title = app.staticTexts["Morning Launch Briefing"]
    XCTAssertTrue(title.waitForExistence(timeout: 5))

    let annotations = try await eventEntityDefinition.viewAnnotations()

    XCTAssertEqual(annotations.count, 1, "Expected exactly one view annotation")
    XCTAssertEqual(try annotations[0].entity.title, "Morning Launch Briefing")
}
```

---

## Useful API facts surfaced by the code

- Entry point: `IntentDefinitions(bundleIdentifier:)` → `.intents["IntentName"]` and an entity definition (e.g. `eventEntityDefinition`). No app import.
- Build + execute: `.makeIntent(param: ...).run()`; capture `result.value` for the perform return value; dynamic member lookup chains entity properties (`result.value.title`).
- AppEnum parameters are passed as raw string values; the framework converts. Custom types → `IntentValueConvertibleWrapper`.
- Entity-definition query methods on the test side: `entities(matching:)`, plus identifier lookups and suggested entities. The string passed for a relationship parameter (e.g. `calendar: "Deep Space"`) is resolved by the runtime via that entity's `EntityStringQuery`.
- System-integration assertions: `spotlightQuery(_:)` returns indexed entity representations; `viewAnnotations()` returns `[ViewAnnotation]`, each with an `.entity`.
- Test-only intents: `static let isDiscoverable = false` + wrap in `#if DEBUG`.
- No `// Copy Code` placeholders were present; one snippet had a stray `/` typo on its comment line in extraction, corrected here to `//`.
