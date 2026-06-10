# Code samples — Session 220

Extracted from the session's Code tab. Five snippets, all SwiftUI.

## Coffee dispenser — label, value, adjustable trait + action

```swift
// Improve accessibility for coffee dispenser

import SwiftUI

struct CoffeeDispenserView: View {
    @State var coffee: Double = 0.0
    var body: some View {
        CoffeeSlider(value: coffee)
            .accessibilityElement()
            .accessibilityLabel("Coffee Dispenser")
            .accessibilityValue("\(Int(coffee)) ounces")
            .accessibilityAddTraits(.adjustable)
            .accessibilityAdjustableAction { direction in
                switch direction {
                case .increment:
                    increaseCoffeeAmount()
                case .decrement:
                    decreaseCoffeeAmount()
                }
            }
    }
}
```

## Coffee dispenser — set the accessibility activation point

```swift
// Set the accessibility activation point
import SwiftUI

struct CoffeeDispenserView: View {
    @State var coffee: Double = 0.0

    var body: some View {
        CoffeeSlider(value: coffee)
            .accessibilityActivationPoint(
                UnitPoint(x: 0.5, y: 1 - coffee)
            )
    }
}
```

## Coffee dispenser — throttled announcements during passthrough

```swift
// Post accessibility announcements

import SwiftUI

struct CoffeeDispenserView: View {
    @State var coffee: Double = 0.0

    var body: some View {
        CoffeeSlider(value: coffee)
            // ...
            .onChange(of: coffee) { _, newValue in
                if sufficientTimeSinceLastAnnouncement() && valueHasChanged() {
                    cacheLastSpokenValue(newValue)
                    AccessibilityNotification
                        .Announcement(newValue)
                        .post()
                }
            }
    }
}
```

## Equalizer pad — custom actions for a 2D control

```swift
// Add custom actions

import SwiftUI

struct EqualizerView: View {
    var body: some View {
        EqualizerPad()
            .accessibilityActions("Move Up") {
                increaseY(by: 10)
            }
            .accessibilityActions("Move Right") {
                increaseX(by: 10)
            }
            .accessibilityActions("Move Down") {
                decreaseY(by: 10)
            }
            .accessibilityActions("Move Left") {
                decreaseX(by: 10)
            }
    }
}
```

## Virtual cat — Direct Touch API

```swift
// Customize accessibility for the interactive cat surface

import SwiftUI

struct VirtualCat: View {
    var cat: CatModel
    var body: some View {
        InteractiveCatSurface()
            .accessibilityLabel("Virtual Cat")
            .accessibilityValue(cat.currentReaction.description)
            .accessibilityDirectTouch([.requiresActivation])
    }
}
```

---

## API facts surfaced by the code + transcript

- `accessibilityElement()` marks a view as a single accessibility element so the modifiers below apply to it.
- `.adjustable` trait + `accessibilityAdjustableAction { direction in … }` give VoiceOver swipe-up/down adjustment; `direction` is `.increment` or `.decrement`.
- `accessibilityActivationPoint(UnitPoint)` positions where the passthrough gesture (double-tap-and-hold) begins; default is center. Here `y: 1 - coffee` puts it at the current fill level.
- `AccessibilityNotification.Announcement(_).post()` speaks an update; throttle it (transcript: announce only on real change and after ≥0.3s) to avoid noise.
- `accessibilityActions(_:_:)` (called per action) exposes named custom actions — unlike `.adjustable`, not limited to a single axis. (Transcript calls this "the `accessibilityAction` modifier added four times.")
- `accessibilityDirectTouch(_:)` marks a direct-touch region; underlying trait is `allowsDirectInteraction`. Options: `.requiresActivation` (won't respond until a double-tap; lets the finger move without accidental activation; stays active until focus leaves) and `.silentOnTouch` (VoiceOver stays silent so it doesn't talk over the control's own audio).
