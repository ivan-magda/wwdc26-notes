# Session 295 — Validate your App Intents adoption with AppIntentsTesting

- **URL:** https://developer.apple.com/videos/play/wwdc2026/295/
- **Duration:** 26m
- **Speakers:** Venkatesh (Software Engineer, App Intents team); colleague Justin (CometCal / Siri code-along).

## Description

AppIntentsTesting is a brand-new framework for testing App Intents — the code that
powers Siri, Shortcuts, Spotlight, and Widgets. It is an integration testing
framework: your tests live in a standard XCUITest bundle running in their own
process, and execute your intents, entities, enums, and queries on-device through
the full App Intents stack, with no mocks, no stubs, and no imports of your app
code (you reference everything by bundle identifier and string). The session walks
through writing a first test against a sample calendar app (CometCal), then levels
up to entity queries, chained intents, test-only intents, and system integrations
(Spotlight indexing and view annotations).

## Key topics

- `IntentDefinitions(bundleIdentifier:)` — string/bundle-id entry point that exposes every intent, entity, and query without importing the app.
- `makeIntent(...).run()` — populate an intent with parameters (raw values, incl. AppEnum raw strings) and execute it on-device; `result.value` + dynamic member lookup to assert on returned entities.
- Out-of-process architecture: tests in an XCUITest bundle, app in a separate process; CI picks it up automatically; stable across releases, no UI dependency. Test runner and app must share a development team for code signing.
- Testing entity queries: `entities(matching:)` for `EntityStringQuery`; identifier lookups; suggested entities. Demonstrated via TDD (write failing test → implement `entities(matching:)` → green).
- Chaining intents: pass the `EventEntity` returned by one intent straight into the next, mirroring Shortcuts composition; the runtime resolves a `CalendarEntity` from a passed string via its `EntityStringQuery`.
- Test-only intents: `isDiscoverable = false` + `#if DEBUG` to seed state, jump to a view, or wrap not-yet-adopted functionality. Example: `SeedSampleEventsIntent`.
- System integrations: `spotlightQuery(_:)` to assert Spotlight indexing; `viewAnnotations()` to assert which entity Siri sees on screen (`ViewAnnotation.entity`).
- Custom parameter types: `IntentValueConvertibleWrapper` (see docs).
- Recommended workflow: unit-test fundamental types first, then integration-test deeper system reaches, then still test manually with Siri + Shortcuts.

## Related sessions to fetch

- [ ] Get started with App Intents (WWDC25 — "if you are new to AppIntents")
- [ ] New advances in App Intents (WWDC26 — "new advances")
- [ ] Justin's CometCal Siri code-along ("how we made CometCal available to Siri")
- [ ] Build Siri experiences with App Intents (closing pointer)

## Chapter summary (Summary tab)

- **0:16 Introduction** — AppIntentsTesting, a new framework for testing App Intents (Siri, Shortcuts, Spotlight, Widgets). Agenda: getting started, framework overview, testing intents and entities, system integrations.
- **2:01 Meet CometCal: your first test** — sample calendar app; first test for `CreateCalendarIntent`: create a UI testing bundle, build `IntentDefinitions` from the bundle id, `makeIntent` with parameters, `run()` on-device, assert on the returned entity via dynamic member lookup.
- **2:29 How AppIntentsTesting works** — integration test framework; tests run in a standard XCUITest bundle while the app runs in a separate process, executing intents through the full App Intents stack; no mocks, no app-code imports (just a bundle id); stable, CI-friendly.
- **9:39 Testing entity queries** — TDD an `EntityStringQuery`: call `entities(matching:)`, watch it fail, implement the conformance on `EventEntityQuery`, rerun to green, verify against Shortcuts on device.
- **13:49 Combining multiple intents** — chain intents as people compose Shortcuts: run `CreateEventIntent` (runtime resolves `CalendarEntity` from a string), pass the returned `EventEntity` into `UpdateEventIntent`, assert the updated title.
- **16:27 Test-only intents** — focused intents that exist only for tests: seed known state, jump to a view, or wrap not-yet-adopted functionality. `isDiscoverable: false` + `#if DEBUG`.
- **18:22 Testing Spotlight indexing** — regression test: `spotlightQuery()` returns nothing before the event exists, create it with `CreateEventIntent`, then assert exactly one indexed result — catches a real bug where indexing was commented out.
- **20:56 Testing view annotations** — verify the entity Siri sees on screen: open an event via `OpenEventIntent`, use XCUI to confirm the page, call `viewAnnotations()`, assert the single `ViewAnnotation`'s entity — surfaces a bug where the wrong `EntityIdentifier` (calendar id) was used.
- **24:00 The App Intents testing workflow** — unit-test the fundamental types (actions, data, queries) first, then integration-test deeper system reaches (view annotations, Spotlight, cross-app), and still test manually with Siri and Shortcuts.
- **25:19 Next steps** — download the CometCal sample project and its full test suite, consult the AppIntentsTesting documentation, and explore the talk on building Siri experiences with App Intents.

## Code

See `code.md` — 7 snippets extracted from the Code tab.
