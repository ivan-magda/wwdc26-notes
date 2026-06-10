# Session 297 — Best practices for integrating visual intelligence in your app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/297/
- **Duration:** 18m
- **Speakers:** David (ML engineer, System Experience)

## Description

A step-by-step guide to integrating your app with Visual Intelligence, built around
a sample music-discovery app. Covers the two integration surfaces: providing results
to Visual Intelligence via Image Search (App entities + an `IntentValueQuery` + an
`OpenIntent`), and receiving data Visual Intelligence writes into system stores
(EventKit, Contacts, HealthKit). New this year: Visual Intelligence on iPadOS and
macOS, plus capturing contacts, multiple calendar events, and medical-device readings.

## Key topics

- Model app content as an `AppEntity` with a concise `DisplayRepresentation` (title, subtitle, thumbnail) — roughly three lines of text plus a thumbnail-sized image
- Implement an `IntentValueQuery` that receives a `SemanticContentDescriptor` (carrying the captured image's `pixelBuffer`)
- On-device image similarity with the Vision framework: `GenerateImageFeaturePrintRequest`, pre-computed `FeaturePrintObservation`s, distance thresholds, ranked + limited results
- Land users with an `OpenIntent`; keep it lightweight (runs as the app foregrounds); reuse an existing one rather than creating a Visual-Intelligence-specific intent
- Cross-platform: same entities/query/OpenIntent on iOS, iPadOS, macOS — mind camera vs screenshot input and much larger Mac pixel buffers (consider resizing)
- `@UnionValue` to return multiple entity types from one query (albums + concerts); derive related content beyond pixel matches
- `semanticContentSearch` schema to continue into the app's full in-app search with pre-populated results
- System store integrations: `EKEventStore` (EventKit), `CNContactStore` (Contacts), `HKHealthStore` (HealthKit); observe store-change notifications so captured data appears automatically

## Related sessions to fetch

- [ ] App Intents sessions from WWDC25 (recommended intro)
- [ ] Vision framework session (image-processing techniques: text, barcodes, faces, classification)
- [ ] App Intents session(s) referenced in the closing "related sessions"

## Chapter summary (Summary tab)

- **0:07 Introduction** — Visual Intelligence integration and what's new in iOS 26, iPadOS, and macOS, demoed through a sample music-discovery app. Agenda: define content, implement a query, cross-platform adoption, system store integrations.
- **2:02 Defining your content** — Model content as an `AppEntity` so Visual Intelligence can show it in search results. `DisplayRepresentation` (title, subtitle, thumbnail); concise identifying text and thumbnail-sized images.
- **5:03 Implementing a query** — `IntentValueQuery` returns results from a `SemanticContentDescriptor`'s pixel buffer using Vision's `GenerateImageFeaturePrintRequest` for on-device similarity, with pre-computed feature prints and distance thresholds for speed.
- **8:18 Opening results** — `OpenIntent` takes people straight to the selected content; keep it lightweight since it runs as the app foregrounds; reuse an existing `OpenIntent`.
- **10:03 Mac and iPad adoption** — Same entities, query, and OpenIntent carry over with minimal changes. Account for camera vs screenshot input and larger Mac pixel buffers that may need resizing.
- **12:27 Returning multiple result types** — `@UnionValue` returns more than one entity type from a single query (albums plus nearby concerts); derive related content rather than only matching pixels.
- **12:56 Continuing search in your app** — The `semanticContentSearch` schema lets people continue into full in-app search, pre-populating results from the captured context.
- **14:27 System store integrations** — Visual Intelligence writes data your app reads back: events via EventKit (`EKEventStore`), contacts via Contacts (`CNContactStore`), medical-device readings via HealthKit (`HKHealthStore`). Observe store-change notifications so data appears automatically.
- **17:16 Next steps** — Recaps the two integration points (Image Search and system stores) across iOS, iPadOS, macOS; points to documentation and related App Intents / Vision sessions.

## Code

See `code.md` — 11 snippets extracted from the Code tab.
