# Session 226 — Create live communication experiences

- **URL:** https://developer.apple.com/videos/play/wwdc2026/226/
- **Duration:** 17m
- **Speakers:** Yaseen (Software Engineer, Apple)

## Description

LiveCommunicationKit is the modern way to build live conversation experiences that
integrate with the system. This session shows how to deliver a rich, native
conversation UI — a full-screen Lock Screen presentation, the Dynamic Island, Phone
app Recents, and Siri — for any real-time audio or video conversation app. It covers
the core concepts (handles, capabilities, the conversation lifecycle, and a single
`ConversationManager` plus its delegate), then walks through receiving incoming
conversations via PushKit, placing outgoing conversations, and managing group
conversations including merging and unmerging.

## Key topics

- **Conversation model** — a single real-time interaction that lives only while someone is in it; built from `Handle`s (kind, value, display name) and capabilities (`.video`, `.pausing`, `.merging`, `.unmerging`).
- **Lifecycle states** — idle → joining → joined → (pause/resume) → leaving → left, driven by your app reporting state and events.
- **Architecture** — `ConversationManager` (you report up to the system) + `ConversationManagerDelegate` (the system sends actions down); every interaction, system-UI or in-app, flows through one `perform(action:)` callback.
- **Incoming conversations** — VoIP push via PushKit wakes the app; you must `reportNewIncomingConversation` before the delegate method returns or the system kills the app.
- **Outgoing conversations** — build a `StartConversationAction` and call `manager.perform([...])`; same delegate path as a join.
- **Recents & Siri** — support the start-call intent (delivered as `NSUserActivity`); donate your own intent at conversation end; use stable handles so people can redial.
- **Group conversations** — track `members` (everyone invited) vs `activeRemoteMembers` (media actively flowing); merge/unmerge two parallel conversations via delegate actions.
- **Migration** — modern replacement for CallKit's `CXProvider`; richer, more flexible API.
- **Background modes** — register Audio + Voice over IP capabilities to keep conversations alive when backgrounded or locked.

## Related sessions to fetch (referenced in this talk)

- [ ] Get to know App Intents
- [ ] PushKit documentation (Voice over IP push handling) — doc, not a session

## Chapter summary (Summary tab)

- **0:01 Introduction** — LiveCommunicationKit is the modern way to build live conversation experiences. Conversations are built from handles, display names, and capabilities that configure the interface, plus a single `ConversationManager` object to manage the full lifecycle. Same Lock Screen / Recents / contact-details presentation as the system phone UI.
- **7:56 Incoming conversations** — `ConversationManager` configures ringtones, group limits, supported handles. Use PushKit to deliver an incoming conversation to a device, then report it to the `ConversationManager`. Join handled via `JoinConversationAction`; end via `EndConversationAction`.
- **11:29 Outgoing conversations** — start in-app conversations by performing a `StartConversationAction`. The `ConversationManager` delegate handles the whole process, using the same unified action-handling logic for system-UI and in-app actions. Redial via the start-call intent / donated intents.
- **13:18 Groups** — track the full invited `members` list plus the currently `activeRemoteMembers` so the interface stays in sync as people join or drop. Advanced call management via delegate actions supports merging and unmerging.

## Code

See `code.md` — 13 snippets extracted from the Code tab.
