---
title: "Build intelligent Siri experiences with App Schemas — Full Digest"
session: WWDC26 · 240
url: https://developer.apple.com/videos/play/wwdc2026/240/
duration: 28m
speakers: Dan Niemeyer
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 240 — Build intelligent Siri experiences with App Schemas

## TL;DR

In the "27 releases," Siri gets meaningfully more capable, and the way developers
plug in is still **App Intents** — now extended so Siri can reach all the way into
your app's content and context. Three new powers, all built on App Intents:

1. **Access your entities** — Siri can read the real content in your app and answer
   questions about it ("When and where is my next meeting?").
2. **Take action via your intents** — Siri can execute your app's actions from natural
   language ("Send my latest report to Mary"), without opening the app.
3. **Understand onscreen context** — Siri can resolve "this"/"that" by annotating your
   views with the same entities you already use ("Explain this text", "Forward the last one").

The build path is a four-layer stack: model content as **App Entities** → conform them
to **App Schemas** so Siri knows the *category* → make them resolvable with
**`IndexedEntity`** (semantic search) or **`EntityStringQuery`** → conform intents to
**App Schemas grouped into domains** so actions become executable. Cross-app flows add
**onscreen awareness** (view/activity annotations) + **content transfer** (`Transferable`,
`IntentValueRepresentation`, `IntentValueQuery`). The recurring sample is **UnicornChat**,
a messaging app for chatting with unicorns (Bubbles, Flare, Glow).

The sleeper feature is tooling: Xcode now flags *incomplete schema sets* (e.g. you
adopted `sendMessage` but not `draftMessage`) as **build errors with Fix-Its**, and a
**testing ladder** (AppIntentsTesting → Shortcuts → Spotlight → Siri) lets you validate
each layer in isolation.

---

## 1. What's new in Siri (1:06)

App Intents remains "the foundation for integrating your app with Siri and Apple
Intelligence" — a structured way to describe what your app can do and the content it
manages. Everything in the talk builds on it. The three new capabilities:

- **Access app entities** — the real, meaningful content inside your app. Siri can
  understand *what a meeting is* in your app, *which* meeting is relevant, and *which
  properties* to return (time, location).
- **Take action via intents** — intents describe the actions your app supports, their
  parameters, and when they're safe to run. Siri handles language understanding; your
  app focuses on the action.
- **Understand onscreen context** — annotate views with entities and Siri can reason
  about what's meaningful onscreen, which entities it represents, and what actions apply.

The sample app for the rest of the talk is **UnicornChat**, a messaging app (download
from the Apple Developer website). Its nouns are **Contact, Conversation, Message** —
all modeled as App Entities conforming to App Schemas.

## 2. Contributing content with App Entities (4:06)

An **`AppEntity`** is a structured representation of content your app *already* works
with — a calendar event, a mail message, a photo or album. It is **not a new data
model**; it's a description of existing content so the system can understand it. An
entity describes three things: **what it is, how it's identified, and which properties
matter** (a title, a date, some text).

Modeling alone isn't enough for Siri to find or talk about an entity. The entity must
conform to an **`AppSchema`**, which gives Siri a predefined understanding of common
categories — messages, contacts, documents. With a schema, Siri stops treating your app
as a black box and can reason about what the user means.

```swift
// Contributing message content to Apple Intelligence

@AppEntity(schema: .messages.message)
struct MessageEntity: IndexedEntity {

    // The text content of the message
    @Property(indexingKey: \.textContent)
    var body: AttributedString?
}
```

The `@AppEntity(schema: .messages.message)` macro both declares the entity and binds it
to the Messages-domain `message` schema.

## 3. Entity resolution — IndexedEntity vs EntityStringQuery (6:21)

**Entity resolution** is how Siri turns what the user says into real entities. For exact
names ("Open UnicornChat with Glow") Siri matches the contact and fills in the entity
plus its property values. But people speak in *concepts*, not exact strings ("the best
windsurfing in Carmel") — that needs **semantic search**, not string matching.

**`IndexedEntity`** is the primary path. Conforming puts your entities into the **system
semantic index**, letting Siri:

- match on **meaning**, not just text,
- understand **relationships** between entities,
- and **answer questions** over your content ("Show the messages with Flare about movies"
  finds messages that reference movie titles — a semantic query, not a string match).

The **`indexingKey`** tells Spotlight which properties (e.g. the message body) are
searchable. Once indexed, Siri can search, reason, and answer — not just retrieve. Net
effect: fewer follow-up questions and more natural language understanding.

When data *can't* be indexed ahead of time — large datasets, server-side data, or data
that changes too frequently — use **`EntityStringQuery`**. Siri hands you the user's raw
input; your app finds and returns matches. You lose semantic understanding but gain full
control over matching.

```swift
// An interface that locates entities using arbitrary string input

struct ContactQuery: EntityStringQuery {
    func entities(matching string: String) async throws -> [ContactEntity] {
        let predicate = #Predicate<Person> { person in
            person.name.localizedStandardContains(string)
        }
        let descriptor = FetchDescriptor<Person>(predicate: predicate)
        let matches = try modelContext.fetch(descriptor)
        return matches.map(\.entity)
    }
}
```

**The recipe:** model content as App Entities → conform to an App Schema → adopt
`IndexedEntity` when indexable, else `EntityStringQuery`.

## 4. Making actions available (9:49)

App Intents expose actions to the system. Not all actions are treated equally — two tiers:

- **Plain App Intents** surface across **Shortcuts, Spotlight, Widgets, and more**.
  People can discover and trigger them *even without Siri*. You describe the action,
  define parameters, implement behavior; the system surfaces, suggests, and wires it in.
- **Schematized actions** become **executable by Siri**. Just as entities use schemas to
  be *understood*, actions use schemas to be *executed*. Think of a schema as a
  *specialization of an App Intent* — still an App Intent, but shaped so Siri can process
  it. Schemas define the kinds of actions Siri understands, the structure it expects, and
  how those actions map to natural language (e.g. "Send a message to Mary", "Play my focus
  Playlist").

Individual schemas define individual actions, but apps usually need a complete set — so
schemas are grouped into **App Schema domains** (mail, photos, messages, …). Integrating
with a domain means implementing a set of predefined schemas and mapping them to your
app's functionality. Domains are "categories of contracts between your app and Siri."
(More on how schemas work: Dan's WWDC24 video.)

## 5. Adopting a schema domain in UnicornChat (12:03)

Everything starts with entities. UnicornChat already models the two things needed to send
a message: a **Contact** (recipient — name, identifier) and a **Message** (body, author).
Adopting the **`sendMessage`** schema from the Messages domain:

1. In Xcode, start typing the schema name — Xcode knows all available schemas grouped by
   domain, and autocomplete picks the right one. Select `sendMessage` from `messages`.
2. The schema declares what the action does, the parameters it expects (recipient, content),
   and how to guide the user if something is missing.
3. Map schema parameters onto UnicornChat's flow: **process the parameters → pass them into
   UnicornChat's interface so the message sends → return the newly sent message as an App
   Entity**.

Because the action is an App Intent, it's available system-wide; because it conforms to a
Messages-domain schema, Siri executes it directly with no NL handling on your part.

**Demo:** "Send a message to Glow in UnicornChat, saying 'What movies do you recommend?'"
→ Siri resolves Glow via the AppEntity query, invokes the intent, sends it — **without
opening the app**.

Recap: App Intents expose actions → App Schemas make them understandable → App Schema
domains package them into end-to-end experiences. Adopt a domain and Siri speaks your
app's language.

## 6. Working across apps — onscreen awareness + content transfer (15:39)

Many real requests span apps: "Hey Siri, email my wife this reply from Bubbles." Two
capabilities combine:

### Onscreen awareness (16:00)

Connect what's visible onscreen to structured entities so Siri resolves "this message" /
"that conversation" without the user naming them. Two annotation APIs:

- **`NSUserActivity`** — when there's **one primary thing** onscreen (viewing a document,
  composing a message).
- **View annotations** — when **multiple meaningful items** are visible (messages in a
  conversation, items in a list).

```swift
// Working across apps - View annotations

List {
    ForEach(messages) { message in
        MessageRow(message: message)
            .appEntityIdentifier(
                EntityIdentifier(
                    for: MessageEntity.self,
                    identifier: message.id
                )
            )
    }
}
```

Each row is annotated with its message entity — the *same* entities used for intents — so
"Edit this message" or "Forward the last one" resolve directly from the view.

### Content transfer (17:55)

Lets *other* apps act on your entities. Export via **`Transferable`** + an
**`IntentValueRepresentation`**, which tells the system how to represent your content in a
form other apps understand. UnicornChat exports a `ContactEntity` as an `IntentPerson`,
enabling "Call this contact" / "Text my wife this conversation" / "Summarize this message".
Your app needn't know what happens next — it just describes its content accurately.

```swift
// Working across apps - Exporting content to another app

extension ContactEntity: Transferable {

    static var transferRepresentation: some TransferRepresentation {
        IntentValueRepresentation(
            exporting: \.person
        )
    }
}
```

**On import**, content either refers to something that already exists or is brand new —
your call which path:

- **Match existing** → `IntentValueQuery`. Conceptually like an entity query but scoped to
  intent parameters. "Given this incoming value, which of my entities does it refer to?"

```swift
// Working across apps - IntentValueQuery

struct ContactEntityQuery: IntentValueQuery {

    func values(for input: [IntentPerson]) async throws -> [ContactEntity] {
        let names = input.map(\.displayName)
        let descriptor = FetchDescriptor<Contact>()
        let contacts = try model.mainContext.fetch(descriptor)
        let matches = contacts.filter { contact in
            names.contains(where: { name in
                contact.name.localizedStandardContains(name)
            })
        }
        return matches.map(\.entity)
    }
}
```

- **Create new** → `IntentValueRepresentation(importing:)`. Convert the incoming value into
  a fresh entity. UnicornChat creates a new unicorn from an `IntentPerson` when needed.

```swift
// Working across apps - IntentValueRepresentation

extension ContactEntity: Transferable {

    static var transferRepresentation: some TransferRepresentation {
        IntentValueRepresentation(exporting: \.person, importing: { intentPerson in
            let contact = Contact(importing: intentPerson)
            ContactManager.shared.contacts.append(contact)
            return contact.entity
        })
    }
}
```

Rule of thumb: **if the content already exists, resolve it; if not, import it.** Many apps
do both. Onscreen awareness plus content transfer together enable multi-step, cross-app
experiences — all built on entities, intents, and schemas.

## 7. Best practices — complete schema sets, enforced by Xcode (21:09)

Individual schemas and APIs can be adopted independently, but great Siri experiences
depend on the pieces working *together* — and the tools help. In the demo, after adopting
`sendMessage`, **building fails**: Xcode reports that the related **`draftMessage`** schema
hasn't been adopted. Some Siri scenarios need more than one schema for a complete
experience (you can send messages, so you also need to draft them — especially when
confirmation is required).

This is "not just a compiler error, it's a design hint." Instead of failing silently at
runtime, the build system surfaces the gap early. Clicking the error offers a **Fix-It**
that generates a sample `draftMessage` adoption — intent definition, required parameters,
stub implementation, all wired. You then fill in app-specific pieces: connect the intent
to entities → inject dependencies → process input → open the message-creation view (on the
**main actor**, since it mutates UI state). Build again → success, and Siri can guide the
full messaging flow.

## 8. Testing your integration (24:18)

A progressive ladder, narrow to broad:

1. **AppIntentsTesting** — a new framework to exercise intents **entirely in isolation, no
   Siri**. Invoke the intent, pass parameters, validate the result like any integration
   test. Fastest, most reliable way to validate business logic early. (See "Validate your
   App Intents adoption with AppIntentsTesting.")
2. **Shortcuts app** — structured UI for your intents; inspect parameters, tweak inputs,
   see how the action is presented. Validates the *shape* of the intent.
3. **Spotlight** — validates content integration: entities indexed correctly, discoverable,
   linkable. Confirms Siri can *find* the right data before acting on it.
4. **Siri** — the full end-to-end: natural language, entity resolution, onscreen context,
   cross-app workflows.

## 9. Next steps (26:21)

- Model and index entities to Spotlight so Siri can find your content.
- Adopt the App Schema domains matching your app's core experiences.
- Adopt `Transferable` for content import/export.
- Test early and often: AppIntentsTesting → Shortcuts → Spotlight → Siri.

All APIs are available today and designed to scale as Siri evolves. Bringing your app to
Siri makes it faster, more accessible, and easier to use across the system, beyond voice
control.
