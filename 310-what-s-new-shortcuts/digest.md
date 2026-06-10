---
title: "What's new in Shortcuts — Full Digest"
session: WWDC26 · 310
url: https://developer.apple.com/videos/play/wwdc2026/310/
duration: 11m
speakers: Duraid
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 310 — What's new in Shortcuts

## TL;DR

A short, demo-driven session on three Shortcuts improvements and how to make your
app's actions and content fit them:

1. **Automations moved into the editor** and gained three new trigger types —
   **screenshot**, **keyboard** (external connect/disconnect), and **notification**
   (per-app, with keyword filtering on the notification's content).
2. **The Use Model action got smarter** — newer Apple Intelligence models with
   **web retrieval**, operating over your App Intent entity content — and gained a
   **transcript inspector** so you can see exactly what data the model received.
3. **Storage** persists data between shortcut runs (Get / Set + global values),
   **syncs across devices via iCloud**, and works with any type including App
   Entities — which makes **stable, device-consistent entity identity** a new
   correctness requirement.

The running example is **Soup Chef**, an app to browse and order soups.

---

## 1. Automations in the editor + three new types

Automations make shortcuts run automatically in response to events (the speaker's
example: leaving work → calculate route home → text partner an ETA). New this year:

- **Automations live directly in the Shortcuts editor**, alongside a shortcut's
  actions, in an **"Automation"** section — easier to browse, add, and set up.
- **Three new automation types:**
  - **Screenshot** — runs when a screenshot is saved.
  - **Keyboard** — triggers when an external keyboard is connected or disconnected.
  - **Notification** — runs when a notification arrives from a **specific app**,
    with a **keyword filter** on the notification content.

### Notification automation demo (Soup Chef)

When a soup delivery is close, Soup Chef sends a notification with the driver's name
and ETA. The automation:

- Triggers on a notification **from the Soup Chef app**.
- Filters for the word **"arriving"** so it doesn't fire on every notification.
- Action: turn on the porch lights + have the HomePod announce the soup is arriving.

### Why notification design matters

Keyword-filtered automations only work if the notification is parseable. The talk's
checklist:

- **Distinct** — the driver's name makes it identifiable.
- **Specific** — the verb "arriving" makes it filterable.
- **Actionable** — the time-until-arrival gives the shortcut something to act on.

Follow the **Human Interface Guidelines** for notifications and your users can build
these automations on top of your app.

## 2. The Use Model action

The **Use Model** action runs a large language model inline in a shortcut. This year:

- Access to **newer, more capable Apple Intelligence models**.
- Those models can **go out to the web** for up-to-date information.
- They operate over **content from your app** — e.g. filtering a large list of
  events down to ones related to an upcoming Montreal trip.

### Soup of the Day demo + the transcript inspector

Goal: a "Soup of the Day" shortcut that picks a soup matching the user's spice
preference and lets them order it. Pieces, built from App Intents:

- **Find Soups** — an action backed by an `EntityPropertyQuery`, fetching all soups
  with a filter for the ones available today.
- **Order Soup** — an action taking a `SoupEntity` parameter, confirming, and
  ordering.
- The **Use Model** action strings these together to pick a soup.

First run picks a mild chicken tortilla soup — wrong, the user wanted spicy. To
debug *what the model saw*:

- Add a **Show Content** action right after Use Model.
- Select the **Transcript** property on the Use Model output inside Show Content.
- Running the shortcut now displays the **model transcript** — the exact, raw
  structured representation of each `SoupEntity` passed to the model, expandable
  per entity to see its properties.

The transcript reveals the entity only exposed **name + availability** — not enough
to judge spice level. Fix: add an **ingredients** property listing each ingredient
and quantity per serving. Re-running, the model now picks a Tom Yum with real heat.

```swift
// SoupEntity — give the model enough structured detail to judge correctly.
struct SoupEntity: AppEntity, Identifiable {
    static var typeDisplayRepresentation = TypeDisplayRepresentation(
        name: "Soup",
        numericFormat: "\(placeholder: .int) soups"
    )
    static var defaultQuery = SoupEntityQuery()

    var id: Soup.ID

    @Property var name: String

    @Property(title: "Available Today")
    var isAvailableToday: Bool

    @Property(title: "Ingredients")   // the property added to fix the bad pick
    var ingredients: String

    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(title: "\(name)", subtitle: SoupStore.description(for: id))
    }
}
```

> Building App Intents for the Use Model action is covered in
> **"Develop for Shortcuts and Spotlight with App Intents"** (WWDC25).

## 3. Storage

**Storage** lets a shortcut **save content and persist it between runs**.

- The editor has a **view to create, view, and edit** a shortcut's stored values.
- **Global values** are shared across multiple shortcuts — useful for data needed
  in more than one shortcut, like an **API key**.
- Three actions retrieve and update this data (Get, Set / "setter", and global
  values), enabling everything from simple counters/logs (daily coffees) to
  shortcuts that track richer context across runs.

### Demo A — fresh facts every morning

The Use Model action is **deterministic** (predictable output) — good usually, but
this motor-racing-fact shortcut wants something new daily. Pattern:

1. **Get** a stored value, "Previous Facts" (a list of every past fact).
2. Pass it to the model and instruct it to **avoid** those facts.
3. **Add to List** appends the new fact, outputting the updated list.
4. **Set** the stored value to the new list.

Result: a new fact each run — i.e. giving the model a **memory**.

### Demo B — give Soup of the Day a memory

Same pattern applied to the soup picker so it stops repeating the same soup several
days in a row: store recent selections and tell the model not to repeat past picks.
The storage view then shows a list of previous soups.

### iCloud sync + stable entity identity

- Stored values **sync across devices** — build/run on iPhone, continue on iPad and
  it remembers past soups.
- Storage works with **any data type, including App Entities**.
- **Because values sync, an App Entity needs a consistent identity across every
  device.** An entity saved on iPhone must be recognized by the iPad/Mac app as the
  *same* entity.
- In the demo, "Order Soup" must resolve a stored soup that may have originated on
  another device. The fix: derive the identifier from a source that **produces the
  same value on every device** — here, each soup's **backend database row ID** as
  the stable `SoupEntity.id` — not a per-device identifier.

## Wrap-up / next steps

- Build shortcuts that integrate with your app to learn what users want to automate.
- Refine your notifications so users can build powerful (keyword-filtered) automations.
- Test your App Entities so they play well with the Use Model action and Storage.
