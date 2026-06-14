---
title: "Validate your App Intents adoption with AppIntentsTesting — Full Digest"
session: WWDC26 · 295
url: https://developer.apple.com/videos/play/wwdc2026/295/
duration: 26m
speakers: Venkatesh (App Intents team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 295 — Validate your App Intents adoption with AppIntentsTesting

## TL;DR

**AppIntentsTesting** is a brand-new framework for writing automated tests against
your App Intents — the code that powers Siri, Shortcuts, Spotlight, and Widgets.
Headline themes:

1. **It's an integration test framework, not a unit-mock framework.** Tests live in
   a standard XCUITest bundle in their own process; your app runs in a separate
   process and executes intents on-device through the *full* App Intents stack —
   the same code path people hit. No mocks, no stubs.
2. **No app import — string + bundle id only.** You reach every intent, entity, enum,
   and query via `IntentDefinitions(bundleIdentifier:)` and string subscripts. Your
   test target never compiles your app code, so tests stay stable across releases
   and don't depend on any UI (yours or the system's). CI picks them up automatically.
3. **Five lines to your first test.** Build `IntentDefinitions`, grab an intent,
   `makeIntent(params).run()`, and assert on `result.value` via dynamic member lookup.
4. **It scales up:** entity string queries (`entities(matching:)`), chaining intents
   the way Shortcuts compose them, **test-only intents** (`isDiscoverable = false` +
   `#if DEBUG`) for seeding state, and **system integrations**:
   `spotlightQuery()` for Spotlight indexing and `viewAnnotations()` for what Siri
   sees on screen.
5. **Recommended workflow:** unit-test the fundamental types first, then
   integration-test the deeper system reaches, then *still* test manually with Siri
   and Shortcuts.

The whole talk is demoed against **CometCal**, a SwiftUI sample calendar app that
fully adopts App Intents, with a space-mission framing.

---

## 1. Your first test (CreateCalendarIntent)

All you need to get started is a **UI Testing bundle**. The session creates one from
scratch and flags one hard requirement: **the test runner and the app must use the
same development team for code signing**.

The five-line flow:

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

Key mechanics:

- `IntentDefinitions(bundleIdentifier:)` exposes **every intent, entity, and query the
  app defines — without importing the app.**
- `definitions.intents["CreateCalendarIntent"]` returns an `IntentDefinition`; call
  `makeIntent(...)` to get a populated instance, then `.run()` to execute it **in the
  app, on-device**.
- Parameters are passed by raw value. `color` is an **AppEnum** — you pass its raw
  string (`"red"`) and the framework converts. Because the test doesn't build against
  your app, **there is no autocompletion for parameter names/types** — you fill them
  in by hand from the intent's definition.
- Custom parameter types: see **`IntentValueConvertibleWrapper`** in the docs.
- `result.value` is the intent's perform return value (here a `CalendarEntity`); chain
  `.title` via **dynamic member lookup** to assert.

The on-device proof: before the run the app had three calendars (Deep Space, Mission
Control, Stargazing); after, "Occupy Saturn" (red) appears — a real intent executed
through a test.

## 2. How AppIntentsTesting works

The architecture is the point:

- Tests find intents **by string**, like UI tests, with **no access to app internals**.
- Tests run in a **standard XCUITest bundle in their own process**; the **app runs in a
  separate process** and executes the intents.
- The test runner drives execution **across the process boundary** and receives
  results **without sharing any state**.

What that buys you, per the talk:

- **Full-stack coverage** — the same code path Siri/Shortcuts/Spotlight hit. **No mocks,
  no stubs.**
- **CI-friendly** — create an XCUITest bundle (or reuse an existing one); your CI
  pipeline picks it up automatically.
- **App-structure-agnostic** — the test target never imports app code; you only pass a
  bundle identifier, so you don't compile app code into the test target.
- **Stable across releases** — no dependency on any UI, yours or the system's.

## 3. Testing entity queries (TDD)

Entity queries decide what gets returned whenever your entities are looked up — in
Shortcuts, via Siri, anywhere App Intents surfaces them. AppIntentsTesting covers
**string queries, identifier lookups, and suggested entities.**

The demo is pure TDD. The problem: in Shortcuts, searching for an event shows "No
Options Available" because `EventEntity`'s string query isn't implemented. Write the
test first:

```swift
func testEventStringQuery() async throws {
    let results = try await eventEntityDefinition
        .entities(matching: "Cosmic Ray")

    XCTAssertEqual(results.count, 1)
    XCTAssertEqual(try results[0].title, "Cosmic Ray Calibration")
}
```

- `entities(matching:)` on the **entity definition** executes the string query
  on-device and returns an array of `EventEntity` representations — assert on `count`,
  and use dynamic member lookup for properties (`results[0].title`).
- The test **fails first**, which "tells me exactly what I need to build."

Then implement the conformance on `EventEntityQuery`:

```swift
struct EventEntityQuery: EntityStringQuery {
    func entities(for identifiers: [EventEntity.ID]) async throws -> [EventEntity] { }
    func suggestedEntities() async throws -> [EventEntity] { }

    func entities(matching string: String) async throws -> [EventEntity] {
        try calendarManager.fetchEvents()
            .filter { $0.title.localizedCaseInsensitiveContains(string) }
            .map(\.entity)
    }
}
```

Re-run → green, then verify in Shortcuts on device (search now finds the event).

**Setup hygiene note:** the test runs `SeedSampleEventsIntent` to guarantee a known set
of events (it resets app data and adds events). The recommendation is to lift the
entity/intent definitions out of the test function for reuse and move data seeding to
`setUp()`.

## 4. Combining multiple intents

People build complex Shortcuts by feeding one intent's result into the next.
AppIntentsTesting mirrors that in a single test:

```swift
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

Two things worth calling out:

- The `calendar` parameter takes a `CalendarEntity`, but the test **passes a plain
  string** (`"Deep Space"`). The runtime automatically calls the `CalendarEntity`'s
  `EntityStringQuery` and **fills in the first matching value** — exactly how relationship
  parameters resolve in Shortcuts.
- The update step passes `createResult.value` (the returned `EventEntity`) **directly**
  into `UpdateEventIntent`. Entity creation → chaining → update → assertion, all
  on-device in one test.

## 5. Test-only intents

For reliable, self-contained tests, **test-only intents** are focused intents that
exist only to support tests. Use them to:

- Seed exactly the data a test needs (no leftover data → no flaky results).
- Jump directly to any view without UI navigation — so the test survives a screen
  redesign.
- **Wrap functionality you haven't adopted App Intents for yet** — internal navigation,
  data management, state manipulation — and test it through AppIntentsTesting.

`SeedSampleEventsIntent` (used earlier) is one example. Make any intent test-only:

```swift
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

- `isDiscoverable = false` keeps the system from exposing it anywhere.
- `#if DEBUG` ensures only tests can reach it.

## 6. System integration: Spotlight indexing

When you index entities, they show up in system-wide Spotlight search so people find
your content without opening the app. The bug story: indexing code was **commented out
during development and never re-enabled** — app behavior was unchanged, so it went
undetected, and a Spotlight search for "Dark Matter Symposium" returned nothing.

The fix is one uncommented line; the durable answer is a **regression test**:

```swift
func testNewEventIndexedInSpotlight() async throws {
    let before = try await eventEntityDefinition.spotlightQuery("Supernova Viewing Party")
    XCTAssertTrue(before.isEmpty, "Event should not exist in Spotlight yet")

    // ... Create "Supernova Viewing Party" Event with CreateEventIntent

    let after = try await eventEntityDefinition.spotlightQuery("Supernova Viewing Party")
    XCTAssertEqual(after.count, 1)
    XCTAssertEqual(try after[0].title, "Supernova Viewing Party")
}
```

- `spotlightQuery(_:)` takes a string and returns the **Spotlight-indexed** `EventEntity`
  representations matching it.
- Assert empty before creation, exactly one after — runs on every commit, catches any
  future break in Spotlight integration immediately.

## 7. System integration: view annotations (what Siri sees)

View annotations tell the system which entity a view is currently showing, so Siri can
understand what's on screen and act on it ("Siri, when is this event?" / "Where is
it?"). The bug: a broken annotation means Siri has no idea what's on screen.

```swift
func testEventViewAnnotation() async throws {
    try await openEventDefinition.makeIntent(target: "Morning Launch Briefing").run()

    // Because this lives in an XCUITest bundle, use XCUI to confirm the page
    let app = XCUIApplication()
    let title = app.staticTexts["Morning Launch Briefing"]
    XCTAssertTrue(title.waitForExistence(timeout: 5))

    let annotations = try await eventEntityDefinition.viewAnnotations()

    XCTAssertEqual(annotations.count, 1, "Expected exactly one view annotation")
    XCTAssertEqual(try annotations[0].entity.title, "Morning Launch Briefing")
}
```

- `OpenEventIntent` navigates to the event page; because the test is an XCUITest, you
  can use **XCUI Automation** to confirm the right page rendered.
- `viewAnnotations()` returns the list of view annotations the system reports as
  currently on screen. Assert exactly one `ViewAnnotation`, then assert on its `.entity`
  (dynamic member lookup → `.title`).
- The real bug it surfaced: the wrong `EntityIdentifier` was passed — the event's
  **calendar id** instead of the **event's id**. Test fails, fix the id, test passes,
  Siri now answers correctly.

## 8. The recommended workflow

Where AppIntentsTesting fits in App Intents development:

1. **Implement the fundamental types** — actions, data, queries — and use
   AppIntentsTesting to verify their foundational behavior. These act as **unit tests**
   for your App Intents integration.
2. **Integrate deeper with the system** — annotate views with entities, donate to
   Spotlight, pass data between apps — and cover those with AppIntentsTesting too. These
   are your **integration tests**.
3. **Still test manually** with Siri and the Shortcuts app to experience your intents the
   way people do.

One framework tests intents, entities, enums, queries, and system integrations — all
out-of-process, all automated.
