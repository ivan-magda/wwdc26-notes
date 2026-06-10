# Code samples — Session 271

Extracted from the Code tab. The session is a code-along; snippets build up
incrementally, so later ones repeat earlier modifiers with new lines appended.

## Reordering in a Preview (`reorderable` + `reorderContainer`)

```swift
#Preview {
    @Previewable @State var cards = [
        CardValue(rank: .ace, suit: .clubs),
        CardValue(rank: .ace, suit: .diamonds),
        CardValue(rank: .ace, suit: .hearts),
        CardValue(rank: .ace, suit: .spades)
    ]

    HStack {
        ForEach(cards) { card in
            CardFaceView(card: card)
        }
        .reorderable()
    }
    .frame(maxWidth: .infinity, maxHeight: .infinity)
    .reorderContainer(for: CardValue.self) { difference in
        cards.apply(difference: difference)
    }
    .padding()
    .background(.green.gradient)
}
```

## reorderContainer across multiple piles (keyed by `Card.Group`)

```swift
struct GameView: View {
    var game: Game

    var body: some View {
        GeometryReader { proxy in
            let spacing: CGFloat = 10
            let cardWidth = (proxy.size.width - 6 * spacing) / 7
            VStack {
                HStack(alignment: .top, spacing: spacing) {
                    Group {
                        RemainderView(game: game)
                        CardBackView()
                            .hidden()
                        ForEach(CardValue.Suit.allCases) { suit in
                            DestinationView(game: game, suit: suit)
                        }
                    }
                    .frame(width: cardWidth)
                }
                .padding(.bottom, 20)
                HStack(alignment: .top, spacing: spacing) {
                    ForEach(0..<7) { index in
                        PileView(game: game, index: index)
                            .frame(width: cardWidth)
                    }
                }
                .frame(maxHeight: .infinity, alignment: .top)
                // Add the reorder container modifier.
                .reorderContainer(for: CardValue.self, in: Card.Group.self) { difference in
                    game.moveCards(difference: difference)
                }
            }
        }
        .padding()
    }
}
```

## Excluding face-down cards (split the ForEach, unique collectionID)

```swift
struct PileView: View {
    var game: Game
    var index: Int
    @Query var cards: [Card]

    var body: some View {
        ZStack(alignment: .topLeading) {
            CardPlaceholderView()
            PileLayout {
                let index = firstFaceUpIndex
                // Iterates over the face down cards.
                ForEach(cards[..<index]) { card in
                    CardView(card: card)
                }
                // Iterates over the face up cards.
                ForEach(cards[index...], id: \.value) { card in
                    CardView(card: card)
                }
                .reorderable(collectionID: Card.Group.pile(index))
            }
        }
    }

    var firstFaceUpIndex: Int {
        cards.firstIndex { !$0.isFaceDown } ?? cards.endIndex
    }
}
```

## Custom dragContainer for multi-item drags

```swift
// (appended to GameView's pile HStack, below reorderContainer)
.reorderContainer(for: CardValue.self, in: Card.Group.self) { difference in
    game.moveCards(difference: difference)
}
// Add dragContainer to customize reorderContainer.
.dragContainer(for: CardValue.self) { cardID in
    game.cardStack(startingAt: cardID)
}
```

## dragPreviewsFormation — lifted items appear as a stack

```swift
.dragContainer(for: CardValue.self) { cardID in
    game.cardStack(startingAt: cardID)
}
// Have dragged cards appear as a stack.
.dragPreviewsFormation(.stack)
```

## dropPreviewsFormation — consistent look over drop destinations

```swift
// Declared on the root layout (the GeometryReader content), so it applies
// across every drop destination in the playing area.
.dropPreviewsFormation(.stack)
```

## dragConfiguration — express move on the source card

```swift
struct RemainderView: View {
    @Query var cards: [Card]
    var game: Game

    var body: some View {
        Button {
            incrementCardIndex()
        } label: {
            ZStack {
                CardPlaceholderView()
                CardBackView()
                    .opacity(cards.isEmpty ? 0 : 1)
            }
        }
        .buttonStyle(.plain)
        .disabled(cards.isEmpty)
        ZStack {
            CardPlaceholderView()
            if let currentCard {
                CardFaceView(card: currentCard.value)
                    .draggable(containerItemID: currentCard.value)
                    .opacity(currentCard.value == hiddenCard ? 0 : 1)
            }
        }
        .dragContainer(for: CardValue.self) { cardID in
            [cardID]
        }
        // Add the drag configuration to allow move.
        .dragConfiguration(DragConfiguration(allowMove: true))
    }
}
```

## dropDestination + dropConfiguration — accept moved cards, validate the rules

```swift
.reorderContainer(for: CardValue.self, in: Card.Group.self) { difference in
    game.moveCards(difference: difference)
}
.dragContainer(for: CardValue.self) { cardID in
    game.cardStack(startingAt: cardID)
}
.dragPreviewsFormation(.stack)
.dragConfiguration(DragConfiguration(allowMove: true))
// Add a drop destination to accept inserts.
.dropDestination(for: CardValue.self) { newCards, session in
    if let destination = session.reorderDestination(
        for: CardValue.self, in: Card.Group.self) {
        game.insertCards(newCards, to: destination)
    }
}
// Configure where cards will go when reordering, and accept them by move.
.dropConfiguration { session in
    // Calculate which pile is being dragged over.
    let alignedX = session.location.x - 0.5 * spacing
    let pile = Int(alignedX / (cardWidth + spacing))
    let destination = ReorderDifference<CardValue, Card.Group>
        .Destination(position: .end, collectionID: .pile(pile))
    // Check if the move is allowed.
    let allowed = session.suggestedOperations.contains(.move)
        && game.validateMove(session: session, destination: destination)
    let operation: DropOperation = allowed ? .move : .forbidden
    return DropConfiguration(operation: operation, destination: destination)
}
```

---

## API facts surfaced by the code

- `reorderable()` / `reorderable(collectionID:)` — mark a `ForEach` as reorderable; multiple in one container need a unique `collectionID`.
- `reorderContainer(for:)` and `reorderContainer(for:in:)` — the `in:` form spans multiple collections keyed by a group type (`Card.Group`). Closure receives a `difference` to apply to the model.
- `dragContainer(for:)` — closure maps a dragged item's ID to the array of item IDs to transfer (the stack above it).
- `dragPreviewsFormation(_:)` / `dropPreviewsFormation(_:)` — formation options seen: `.pile`, `.list`, `.stack` (pile is default).
- `DragConfiguration(allowMove:)` + `.dragConfiguration(_:)` — source intent (move vs. default copy).
- `dropDestination(for:)` closure signature here is `(newCards, session)`.
- `session.reorderDestination(for:in:)` — where inserted/reordered items will land.
- `dropConfiguration { session in ... }` returns `DropConfiguration(operation:destination:)`.
- `session.location` (CGPoint), `session.suggestedOperations` (contains `.move`).
- `ReorderDifference<CardValue, Card.Group>.Destination(position: .end, collectionID:)`.
- `DropOperation` cases used: `.move`, `.forbidden`.
