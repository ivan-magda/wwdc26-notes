---
title: "Live Activities essentials — Full Digest"
session: WWDC26 · 223
url: https://developer.apple.com/videos/play/wwdc2026/223/
duration: 15m
speakers: Adi (System Experience Engineer)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 223 — Live Activities essentials

## TL;DR

A from-scratch walkthrough of building a Live Activity, built around a coffee-ordering
demo app. Three movements:

1. **Model your data efficiently** — split everything into static (`ActivityAttributes`)
   and dynamic (`ContentState`). Only the dynamic half can update over the activity's
   lifetime, which is what keeps updates cheap.
2. **Build the presentations and drive the lifecycle** — WidgetKit `ActivityConfiguration`
   supplies a Lock Screen view plus the Dynamic Island's compact / minimal / expanded
   regions. Start and update locally with ActivityKit, or remotely with push (targeted
   tokens, or broadcast channels for mass scale).
3. **Optimize for every screen** — adapt to the landscape Dynamic Island's limited width
   (`isDynamicIslandLimitedInWidth`), fill StandBy edge-to-edge
   (`showsWidgetContainerBackground` + `activityBackgroundTint`), support Apple Watch /
   CarPlay via the `.small` activity family, and add tap actions with `LiveActivityIntent`.

Headline platform change: **in iOS 27, the Dynamic Island compact and minimal views are
visible in both portrait and landscape** — which is the reason the limited-width handling
matters this year.

---

## Where Live Activities show up

Live Activities deliver timely, glanceable updates for something happening right now (the
example shown is the MLB app tracking a live game). They surface across the system:

- **Lock Screen** — the primary canvas.
- **Dynamic Island** — compact and minimal when idle; **expanded** on an alerting update
  or a long-press.
- **StandBy** — when iPhone is charging in landscape.
- **Apple Watch** Smart Stack, **macOS menu bar**, **CarPlay Dashboard** — a Live Activity
  running on iPhone is automatically forwarded to these devices.

iOS 27 makes the Dynamic Island compact/minimal views visible in **portrait and landscape**.

## 1. Plan the data model

Design first — the talk points to the Human Interface Guidelines and the session
"Design dynamic Live Activities." Then split your data:

- **Static** → a struct conforming to `ActivityAttributes`. Never changes for the life of
  the activity.
- **Dynamic** → a nested `ContentState` struct. The only data you can update over time.

This split is what enables efficient updates. For the coffee order: shop name, drink, and
a server-side order ID are static; the order phase, estimated ready time, and rating are
dynamic.

```swift
import ActivityKit
import Foundation

public struct DrinkOrderAttributes: ActivityAttributes {
    let shopName: String
    let drink: Drink
    let orderID: UUID

    public struct ContentState: Codable, Hashable {
        var phase: DrinkOrder.Phase = .waiting
        var estimatedReadyDate: Date
        var rating: DrinkOrder.Rating?
    }
}
```

## 2. Build the views

The UI is WidgetKit. Add a widget extension if you don't have one, then provide an
`ActivityConfiguration` keyed on your attributes type. Each presentation is a SwiftUI view
that reads from `context` (attributes + most recent content state).

The Dynamic Island has four slots:

- **compactLeading / compactTrailing / minimal** — small, shown when nobody is actively
  interacting. The minimal view appears when multiple Live Activities are running, so it
  should carry the single most essential signal (here: a circular gauge of time remaining).
- **expanded** — built from `DynamicIslandExpandedRegion` blocks (leading / center /
  trailing / bottom) that surround the device sensors; roughly as roomy as the Lock Screen.

```swift
import ActivityKit
import SwiftUI
import WidgetKit

struct DrinkOrderLiveActivity: Widget {
    var body: some WidgetConfiguration {
        ActivityConfiguration(for: DrinkOrderAttributes.self) { context in
            ActivityView(context: context)
        } dynamicIsland: { context in
            DynamicIsland {
                DynamicIslandExpandedRegion(.leading)  { ExpandedLeadingView(context: context) }
                DynamicIslandExpandedRegion(.center)   { ExpandedCenterView(context: context) }
                DynamicIslandExpandedRegion(.trailing) { ExpandedTrailingView(context: context) }
                DynamicIslandExpandedRegion(.bottom)   { ExpandedBottomView(context: context) }
            } compactLeading: {
                CompactLeadingView(context: context)
            } compactTrailing: {
                CompactTrailingView(context: context)
            } minimal: {
                MinimalView(context: context)
            }
        }
    }
}
```

## 3. Start and update

Three ways to start a Live Activity:

- **ActivityKit, foreground** — the simplest; start directly while the app runs.
- **Scheduled** — ask ActivityKit to start one at a specific future time.
- **Push notification** — start it from the server.

Starting checks authorization, fills in the static attributes, builds the initial
`ContentState`, wraps it in `ActivityContent` (with an optional `staleDate`), and calls
`Activity.request`. A **`staleDate`** marks when the content should be treated as
out-of-date so your views can show a stale indicator; the coffee demo leaves it `nil`.

```swift
func launchLiveActivity(order: DrinkOrder) throws {
    guard ActivityAuthorizationInfo().areActivitiesEnabled else { return }
    let attributes = DrinkOrderAttributes(shopName: "Coffee Shop", drink: order.drink, orderID: order.id)
    let estimatedReadyDate = Date.now + (15 * 60)
    let contentState = DrinkOrderAttributes.ContentState(phase: .waiting, estimatedReadyDate: estimatedReadyDate)
    let activityContent = ActivityContent(state: contentState, staleDate: nil)
    let activity = try Activity.request(attributes: attributes, content: activityContent)
}
```

Updating is a single call with a fresh `ContentState` (and optionally a new `staleDate`):

```swift
await activity.update(
    ActivityContent(
        state: DrinkOrderAttributes.ContentState(phase: .preparing, estimatedReadyDate: estimatedReadyDate),
        staleDate: nil
    )
)
```

### Remote updates: broadcast vs. targeted push

- **Broadcast channels** — for hundreds/thousands+ people running the *same* activity at
  once (e.g. a live game). The server pushes to a channel; the activity subscribes to it.
- **Targeted push notifications** — for everything else. Obtain a per-activity push token
  and send each update to specific devices.

The ActivityKit push-notifications documentation guide covers the details.

## 4. Optimize the presentations

### Landscape Dynamic Island — limited width

In portrait, compact views can grow in width; in landscape they cannot. Read
`isDynamicIslandLimitedInWidth` and swap to a narrower rendering (here, a progress icon
instead of a timer/label).

```swift
struct CompactTrailingView: View {
    @Environment(\.isDynamicIslandLimitedInWidth) var isDynamicIslandLimitedInWidth
    var context: ActivityViewContext<DrinkOrderAttributes>
    var body: some View {
        if isDynamicIslandLimitedInWidth {
            StepProgressIconView(context: context)
        } else if context.state.phase.showsTimer {
            EstimatedReadyView(context: context, font: .system(.body).monospacedDigit())
                .multilineTextAlignment(.trailing)
                .frame(maxWidth: maximumTimerLabelWidth)
        } else {
            OrderPhaseLabelView(context: context, font: .caption2.bold(), color: .brown)
                .multilineTextAlignment(.trailing)
        }
    }
}
```

### StandBy — fill the screen

StandBy reuses the Lock Screen view scaled to **200%**. A gradient that looks right on the
Lock Screen leaves blank space here. Apply the gradient only when
`showsWidgetContainerBackground` is true (Lock Screen), and otherwise set an edge-to-edge
`activityBackgroundTint`.

```swift
struct ActivityView: View {
    @Environment(\.showsWidgetContainerBackground) var showsWidgetContainerBackground
    var context: ActivityViewContext<DrinkOrderAttributes>

    var body: some View {
        DetailView(context: context)
            .background { if showsWidgetContainerBackground { LinearGradient.barista } }
            .activityBackgroundTint(.espresso)
    }
}
```

### Apple Watch & CarPlay — the small activity family

Activities forward to CarPlay (and the Watch Smart Stack) automatically, defaulting to the
Lock Screen `ActivityView`, which doesn't fit the smaller space. Opt in to the small family
and branch on `activityFamily`:

```swift
// 1. Declare support
ActivityConfiguration(for: DrinkOrderAttributes.self) { /* ... */ } dynamicIsland: { /* ... */ }
    .supplementalActivityFamilies([.small])

// 2. Provide a tailored view
struct ActivityView: View {
    @Environment(\.activityFamily) var activityFamily
    var context: ActivityViewContext<DrinkOrderAttributes>

    @ViewBuilder var contentView: some View {
        if activityFamily == .small { SmallView(context: context) }
        else { DetailView(context: context) }
    }
}
```

Deep-dive: "Bring your Live Activity to Apple Watch."

### Interactivity — App Intents

Wire buttons to a `LiveActivityIntent`. Tapping a button runs the intent's `perform()` on
your app's behalf (sync rating to server, update DB, etc.). The same button view is reused
in the Lock Screen `ActivityView` and the expanded Dynamic Island.

```swift
struct RateDrinkIntent: LiveActivityIntent {
    static var title: LocalizedStringResource = "Rate Drink"
    @Parameter(title: "Order ID")  var orderID: String
    @Parameter(title: "Positive")  var isPositive: Bool

    func perform() async throws -> some IntentResult {
        await updateLocalDatastore(rating: isPositive ? .great : .poor, dismissPolicy: .after(.now + 15))
        return .result()
    }
}

Button(intent: RateDrinkIntent(orderID: context.attributes.orderID.uuidString, isPositive: true)) {
    Label("Great", systemImage: "hand.thumbsup.fill")
}
```

Note the `perform()` here also returns a **`dismissPolicy: .after(...)`** — a way to
auto-dismiss the activity a short time after the action.
