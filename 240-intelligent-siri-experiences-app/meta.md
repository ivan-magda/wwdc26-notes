# Session 240 — Build intelligent Siri experiences with App Schemas

- **URL:** https://developer.apple.com/videos/play/wwdc2026/240/
- **Duration:** 28m
- **Speakers:** Dan Niemeyer (Swift Intelligence Frameworks team)

## Description

How App Intents bring your app to Siri, made more capable, contextual, and personal
by Apple Intelligence in the 27 releases. Siri gains three capabilities built on App
Intents: accessing your app's entities, taking action through your intents, and
understanding onscreen context. The session walks through modeling content as App
Entities, conforming them to App Schemas, adopting App Schema domains for executable
actions, moving content across apps, and the best practices and testing flow that make
a Siri integration complete. Grounded throughout in a sample app, UnicornChat.

## Key topics

- Three new Siri capabilities on App Intents: access app entities, take action via intents, understand onscreen context
- `AppEntity` — structured representation of existing app content (what it is, how it's identified, which properties matter)
- `AppSchema` — gives Siri a predefined understanding of common content categories (messages, contacts, documents)
- Entity resolution: `IndexedEntity` (semantic search + content Q&A via the system semantic index, `indexingKey`) vs `EntityStringQuery` (full control, no semantic understanding) when data can't be indexed ahead of time
- App Intents surface across Shortcuts, Spotlight, Widgets; conforming to schemas + grouping into App Schema *domains* makes them executable by Siri
- End-to-end `sendMessage` schema adoption from the Messages domain
- Moving content across apps: onscreen awareness (`NSUserActivity` for one primary item, view annotations / `appEntityIdentifier` for multiple), and content transfer (`Transferable` + `IntentValueRepresentation` for export; `IntentValueQuery` to match existing, `IntentValueRepresentation(importing:)` to create new)
- Best practices: adopt full schema *sets*; Xcode surfaces missing related schemas (e.g. `draftMessage` alongside `sendMessage`) at build time with Fix-Its
- Testing ladder: AppIntentsTesting → Shortcuts → Spotlight → Siri

## Related sessions to fetch (referenced in this talk)

- [ ] App Intents fundamentals videos (named generically as "these videos" early on)
- [ ] "Bring your app's core features to Siri with App Intents" / app schemas video from WWDC24
- [ ] Validate your App Intents adoption with AppIntentsTesting

## Chapter summary (Summary tab)

- **0:00 Introduction** — Dan Niemeyer; how App Intents bring your app to Siri, made more capable/contextual/personal by Apple Intelligence in the 27 releases. Agenda: what's new, contributing content, making actions available, working across apps, best practices.
- **1:06 What's new in Siri** — Siri gains three capabilities built on App Intents: accessing entities, taking action through intents, understanding onscreen context. Introduces the UnicornChat sample app.
- **4:06 Contributing content with App Entities** — model content as App Entities (what a thing is, how it's identified, which properties matter), then conform to an App Schema so Siri understands the category of content.
- **6:21 Entity resolution and IndexedEntity** — how Siri resolves spoken references to real entities. Adopt `IndexedEntity` for semantic search and content Q&A, using `indexingKey` to mark searchable properties, or `EntityStringQuery` when data can't be indexed ahead of time.
- **9:49 Making actions available** — App Intents expose actions across Shortcuts, Spotlight, Widgets. Conforming intents to App Schemas and grouping them into App Schema domains makes those actions executable by Siri through natural language.
- **12:03 Adopting a schema domain in UnicornChat** — end-to-end walkthrough of adopting the Messages domain's `sendMessage` schema: mapping schema parameters onto UnicornChat's messaging flow and returning the sent message as an entity, so Siri can send messages without opening the app.
- **15:39 Moving content across apps** — export entities with `Transferable` and `IntentValueRepresentation` so other apps can act on them. On import, use `IntentValueQuery` to match existing content or `IntentValueRepresentation(importing:)` to create something new.
- **16:00 Working across apps: onscreen awareness** — requests that span apps rely on onscreen awareness. Connect views to App Entities via `NSUserActivity` (single primary item) or view annotations (multiple visible items), so Siri can resolve references like "this" and "that."
- **21:09 Best practices** — design for complete Siri conversations by adopting full schema sets. Xcode surfaces missing related schemas (e.g. `draftMessage` alongside `sendMessage`) at build time with Fix-Its.
- **24:18 Testing your integration** — validate progressively: AppIntentsTesting for business logic in isolation, then Shortcuts for intent shape, Spotlight for content indexing, finally Siri for the full end-to-end experience.
- **26:21 Next steps** — model and index entities, adopt the App Schema domains matching your app, enable content transfer with `Transferable`, and test early with Shortcuts, Spotlight, and Siri.

## Code

See `code.md` — 6 snippets extracted from the Code tab.
