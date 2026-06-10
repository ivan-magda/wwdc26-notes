# Session 310 — What's new in Shortcuts

- **URL:** https://developer.apple.com/videos/play/wwdc2026/310/
- **Duration:** 11m
- **Speaker:** Duraid (Shortcuts team)

## Description

Shortcuts lets people combine everyday app actions and surface them across the
system — through Siri, Control Center, the Action Button, and more. This session
covers three major enhancements: redesigned automation setup with three new
automation types, a more capable Use Model action with a transcript inspector for
debugging, and Storage for persisting data between shortcut runs (synced across
devices via iCloud). The throughline is how to expose your app's actions and
content so they fit naturally into these features.

## Key topics

- **Automations in the editor** — automations now live in the Shortcuts editor
  alongside actions; an "Automation" section to browse and add them.
- **Three new automation types** — screenshot (runs when a screenshot is saved),
  keyboard (external keyboard connect/disconnect), and notification (runs on a
  notification from a specific app, with keyword filtering on content).
- **Notification design for automations** — concise, distinct, specific,
  actionable notifications (per the HIG) make keyword-filtered triggers reliable.
- **Use Model action** — access to newer, more capable Apple Intelligence models
  with web retrieval; works over App Intent entity content from your app.
- **Model transcript inspector** — Show Content + the Transcript property reveals
  exactly what entity data was passed to the model, for debugging unexpected output.
- **App Entity design for the model** — richer `@Property` values (e.g. an
  `ingredients` array) give the model what it needs to judge correctly.
- **Storage** — Get / Set actions persist data between runs; global values shared
  across shortcuts (e.g. an API key); works with any data type including App Entities.
- **iCloud sync + stable identity** — stored values sync across devices, so App
  Entities need a device-consistent `id` (e.g. a backend database row ID), not a
  per-device identifier.

## Related sessions to fetch

- [ ] Develop for Shortcuts and Spotlight with App Intents (WWDC25)

## Chapter summary (Summary tab)

- **0:01 Introduction** — Duraid; Shortcuts combines everyday app actions and
  surfaces them via Siri, Control Center, Action Button. Three enhancements ahead.
- **0:57 Automations** — automations now in the editor and easier to discover;
  three new types (screenshot, keyboard, notification). The notification automation
  supports fine-grained, keyword-filtered triggers on notification content. Build
  notifications that integrate well (Soup Chef "arriving" → porch lights + HomePod).
- **3:25 Use Model** — newer Apple Intelligence models with web retrieval; works
  with app content. The transcript inspector (Show Content → Transcript) shows the
  exact data passed to the model from App Intent entities (Soup of the Day demo).
- **6:58 Storage** — persist data between runs with Get / Set and global values;
  syncs across devices via iCloud. Use it to give the Use Model action a "memory."
  App Entities need a stable, device-consistent identifier for correct recognition.

See `code.md` for the one code sample (the `SoupEntity` definition).
