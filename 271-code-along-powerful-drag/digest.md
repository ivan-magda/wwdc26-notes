---
title: "Code-along: Build powerful drag and drop in SwiftUI — Full Digest"
session: WWDC26 · 271
url: https://developer.apple.com/videos/play/wwdc2026/271/
duration: 15m
speakers: Jack (UI Frameworks)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 271 — Code-along: Build powerful drag and drop in SwiftUI

## TL;DR

SwiftUI has had drag and drop since iOS 16 via `draggable`, `dropDestination`, and the
`Transferable` protocol. This code-along adds three new layers on top, built into a
working game of Solitaire:

1. **Reordering** — `reorderable` on a `ForEach` plus `reorderContainer` on its host lets
   people rearrange content with drag and drop. A view lifts out, a placeholder holds its
   spot, neighbors part to show where it'll land, and the drop applies a `difference` to
   your model. One container can span many collections (the seven piles), and you exclude
   items (face-down cards) just by not marking them reorderable.
2. **Multi-item drags** — `dragContainer(for:)` lifts several items at once; map a dragged
   item's ID to the set of IDs that should come with it. `dragPreviewsFormation` and
   `dropPreviewsFormation` control how the lifted group looks at the source and over a drop
   target (pile / list / stack).
3. **Drag configuration** — `dragConfiguration` on the source expresses *intent* (move vs.
   the default copy), and `dropConfiguration` on the destination has the *final say*: pick
   the target collection, restrict the operation, and validate against your rules
   (return `.forbidden` to reject).

The recurring framing: start with just `reorderable` + `reorderContainer`, then compose the
other modifiers on top to fully customize behavior.

---

## 0. Recap — what already existed (since iOS 16)

- `draggable` moves content (photos, text, your types) through the system via a drag gesture.
- Conform your data to **`Transferable`** with a transfer representation so it can be dragged
  out of and accepted by other apps. The card type conforms, then an instance goes to `draggable`.
- `dropDestination` accepts content; you declare which `Transferable` type the view handles.
- Deep-dive on making content transferable: **"Meet Transferable" (WWDC 2022).**

## 1. Reordering — `reorderable` + `reorderContainer`

The mental model: when you drag a reorderable view it's **lifted from the hierarchy**, an
**empty placeholder** takes its spot, neighbors **make space** as you move over them, the
placeholder tracks **where it'll land**, and the drop **moves** it there.

Start in a Preview with four cards:

```swift
HStack {
    ForEach(cards) { card in
        CardFaceView(card: card)
    }
    .reorderable()
}
.reorderContainer(for: CardValue.self) { difference in
    cards.apply(difference: difference)
}
```

- `reorderable()` marks the `ForEach`; `reorderContainer(for:)` goes on the host (the `HStack`).
- The container's item type (`CardValue`) must match the `ForEach`'s.
- The closure hands you a **`difference`** at the end of an operation, which you apply to your
  array (`cards.apply(difference:)`).

### Spanning multiple collections (all seven piles, one container)

To let cards move *between* piles, put one `reorderContainer` on the `HStack` of piles and use
the `in:` overload with a group type to identify each pile:

```swift
.reorderContainer(for: CardValue.self, in: Card.Group.self) { difference in
    game.moveCards(difference: difference)
}
```

Then each pile's `ForEach` is marked reorderable. Because several reorderable views live in the
**same** container, each needs a unique `collectionID`:

```swift
ForEach(cards[index...], id: \.value) { card in
    CardView(card: card)
}
.reorderable(collectionID: Card.Group.pile(index))
```

### Excluding items without "advanced" API

Face-down cards shouldn't reorder. Rather than reach for low-level drag/drop control, **split
the array into two `ForEach` views** — face-down cards in a plain `ForEach`, face-up cards in
a `reorderable` one:

```swift
let index = firstFaceUpIndex
ForEach(cards[..<index]) { card in CardView(card: card) }      // face down — not reorderable
ForEach(cards[index...], id: \.value) { card in CardView(card: card) }
    .reorderable(collectionID: Card.Group.pile(index))         // face up — reorderable
```

Availability: the reordering APIs are **newly available on all Apple platforms that support
drag and drop.**

## 2. Multi-item drags — Drag Container + preview formations

In Solitaire, dragging a card mid-pile should bring the cards stacked on top of it. More
generally, apps often use a **selection** model (the demo uses tap-to-select, then a drag on any
selected card lifts all of them).

`reorderContainer` **implicitly provides its own `dragContainer` and `dropDestination`**, but you
can declare your own to customize. Add `dragContainer` below `reorderContainer` with the **same
type**:

```swift
.dragContainer(for: CardValue.self) { cardID in
    game.cardStack(startingAt: cardID)   // the dragged card + everything above it
}
```

The closure gets the dragged item's ID and returns the IDs to move together.

### Formations

By default a multi-item drag **collapses into a pile** (first card on top). Options: **pile,
list, stack.** Configure the **source** look with `dragPreviewsFormation`:

```swift
.dragPreviewsFormation(.stack)
```

But over a drop destination the items revert to that destination's formation. To keep it
consistent, set `dropPreviewsFormation` on the **root layout** so it applies everywhere:

```swift
.dropPreviewsFormation(.stack)
```

Availability: `dragContainer` is **new on iOS, iPadOS, visionOS 27**; all three formation/
container modifiers are on **macOS 26 and newer.**

## 3. Drag configuration — move vs. copy, with a final say at the drop

By default SwiftUI **suggests copy** — right for moving data between apps or inserting something
new. But pulling a card from the deck into a pile should **move** it, not duplicate it. The deck
card and the piles are a **separate drag source and drop destination** (unlike the
reorderContainer, which already handles moves internally), so this needs the Drag Configuration API.

### Source: express intent to move

On the source card view (`RemainderView`), which already has `draggable(containerItemID:)` and a
`dragContainer`:

```swift
.dragConfiguration(DragConfiguration(allowMove: true))
```

### Destination: accept inserts, then configure the operation

The reorderContainer only accepts moves **within** itself, so to accept **new** items you add your
own `dropDestination`:

```swift
.dropDestination(for: CardValue.self) { newCards, session in
    if let destination = session.reorderDestination(
        for: CardValue.self, in: Card.Group.self) {
        game.insertCards(newCards, to: destination)
    }
}
```

Moves *between* piles are still handled by the `reorderContainer` closure; this handles *inserts*.

Then `dropConfiguration` has the **final say** about how data transfers. Three jobs in the closure:

```swift
.dropConfiguration { session in
    // 1. Which pile is under the drag?
    let alignedX = session.location.x - 0.5 * spacing
    let pile = Int(alignedX / (cardWidth + spacing))
    let destination = ReorderDifference<CardValue, Card.Group>
        .Destination(position: .end, collectionID: .pile(pile))
    // 2. Only allow move (copy makes no sense in a card game)...
    // 3. ...and validate against the game rules.
    let allowed = session.suggestedOperations.contains(.move)
        && game.validateMove(session: session, destination: destination)
    let operation: DropOperation = allowed ? .move : .forbidden
    return DropConfiguration(operation: operation, destination: destination)
}
```

- Pick the destination collection from `session.location`.
- Restrict to `.move` (normally you'd fall back to copy when move isn't available).
- Return **`.forbidden`** for an illegal play, and SwiftUI prevents the drop — the card snaps
  back. Valid plays move the card from the deck into the pile.

## 4. Composition takeaway

The session's through-line: you can start with *just* `reorderable` + `reorderContainer` and get
working drag-to-reorder, then **layer** `dragContainer`, the formation modifiers, and the
configuration modifiers on top to fully customize. Even outside a card game the prompts are:
let people reorder content, let them drag multiple items, and fine-tune transfer intent.
