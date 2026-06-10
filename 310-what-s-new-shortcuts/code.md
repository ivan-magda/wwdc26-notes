# Code samples — Session 310

Extracted from the Code tab. (The Code tab listed this snippet twice; deduped here.)

## SoupEntity — App Entity with model-friendly properties

The demo `SoupEntity` exposes `name`, `isAvailableToday`, and `ingredients` so the
Use Model action has enough structured detail to judge a soup (e.g. spice level).
Note `id: Soup.ID` — in the talk the stable identifier comes from the backend
database row ID so the entity resolves to the same thing across synced devices.

```swift
// MARK: - Soup Entity

import AppIntents

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

    @Property(title: "Ingredients")
    var ingredients: String

    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(title: "\(name)", subtitle: SoupStore.description(for: id))
    }
}
```

## Spoken APIs / actions named in the talk

- `EntityPropertyQuery` (App Intents) — backs the "Find Soups" action with a
  filter for soups available today.
- An App Intent ("Order Soup") taking a `SoupEntity` parameter, with confirmation.
- **Use Model** action — runs an Apple Intelligence model inline; supports web
  retrieval; consumes App Intent entity content.
- **Show Content** action + its **Transcript** output property — the model
  transcript inspector.
- **Storage** actions — Get (retrieve a stored value), Set (the "setter" action),
  and global values shared across shortcuts.
- **Add to List** action — appends and returns the new list (used to grow a
  stored "Previous Facts" / previous soups list).
- New automation triggers: **screenshot**, **keyboard** (connect/disconnect),
  **notification** (per-app, with keyword content filter).

> Note: the supplement's Code tab text shows `ingredients` as a `String`, while
> the transcript describes adding ingredients "as a simple array of strings"
> (`[String]`), each entry an ingredient + quantity. See `digest.md` Open Questions.
