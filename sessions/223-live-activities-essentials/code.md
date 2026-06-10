# Code samples — Session 223

Extracted from the Code tab. Timestamps map roughly to the transcript chapters.

## Define the data model (ActivityAttributes + ContentState)

```swift
// Define initial Live Activity.

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

## Create each Live Activity view (ActivityConfiguration + DynamicIsland)

```swift
// Create each Live Activity view

import ActivityKit
import SwiftUI
import WidgetKit

struct DrinkOrderLiveActivity: Widget {
    var body: some WidgetConfiguration {
        ActivityConfiguration(for: DrinkOrderAttributes.self) { context in
            ActivityView(context: context)
        } dynamicIsland: { context in
            DynamicIsland {
                DynamicIslandExpandedRegion(.leading) {
                    ExpandedLeadingView(context: context)
                }
                DynamicIslandExpandedRegion(.center) {
                    ExpandedCenterView(context: context)
                }
                DynamicIslandExpandedRegion(.trailing) {
                    ExpandedTrailingView(context: context)
                }
                DynamicIslandExpandedRegion(.bottom) {
                    ExpandedBottomView(context: context)
                }
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

## Start and update a Live Activity (ActivityKit)

```swift
// Start a Live Activity

func launchLiveActivity(order: DrinkOrder) throws {
    guard ActivityAuthorizationInfo().areActivitiesEnabled else { return }
    let attributes = DrinkOrderAttributes(shopName: "Coffee Shop", drink: order.drink, orderID: order.id)
    let estimatedReadyDate = Date.now + (15 * 60)
    let contentState = DrinkOrderAttributes.ContentState(phase: .waiting, estimatedReadyDate: estimatedReadyDate)
    let activityContent = ActivityContent(state: contentState, staleDate: nil)
    let activity = try Activity.request(attributes: attributes, content: activityContent)
}

// Update a Live Activity

await activity.update(
    ActivityContent(
        state: DrinkOrderAttributes.ContentState(
            phase: .preparing,
            estimatedReadyDate: estimatedReadyDate
        ),
        staleDate: nil
    )
)
```

## Optimize for limited width in the Dynamic Island (landscape)

```swift
// Optimize for limited width in the Dynamic Island

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

## Extend background color in StandBy

```swift
// Extend background color in StandBy

struct ActivityView: View {

    @Environment(\.showsWidgetContainerBackground) var showsWidgetContainerBackground
    var context: ActivityViewContext<DrinkOrderAttributes>

    var body: some View {
        DetailView(context: context)
            .background {
                if showsWidgetContainerBackground {
                    LinearGradient.barista
                }
            }
            .activityBackgroundTint(.espresso)
    }
}
```

## Declare support for the small activity family

```swift
// Add support for activityFamily small

import ActivityKit
import SwiftUI
import WidgetKit

struct DrinkOrderLiveActivity: Widget {
    var body: some WidgetConfiguration {
        ActivityConfiguration(for: DrinkOrderAttributes.self) { context in
            ActivityView(context: context)
        } dynamicIsland: { context in
            DynamicIsland {
                DynamicIslandExpandedRegion(.leading) {
                    ExpandedLeadingView(context: context)
                }
                DynamicIslandExpandedRegion(.center) {
                    ExpandedCenterView(context: context)
                }
                DynamicIslandExpandedRegion(.trailing) {
                    ExpandedTrailingView(context: context)
                }
                DynamicIslandExpandedRegion(.bottom) {
                    ExpandedBottomView(context: context)
                }
            } compactLeading: {
                CompactLeadingView(context: context)
            } compactTrailing: {
                CompactTrailingView(context: context)
            } minimal: {
                MinimalView(context: context)
            }
        }
        .supplementalActivityFamilies([.small])
    }
}
```

## Provide a custom view for the small family

```swift
// Optimize for small family

struct ActivityView: View {
    @Environment(\.showsWidgetContainerBackground) var showsWidgetContainerBackground
    @Environment(\.activityFamily) var activityFamily

    var context: ActivityViewContext<DrinkOrderAttributes>

    var body: some View {
        contentView
            .background {
                if showsWidgetContainerBackground {
                    LinearGradient.barista
                }
            }
            .activityBackgroundTint(.espresso)
    }

    @ViewBuilder
    var contentView: some View {
        if activityFamily == .small {
            SmallView(context: context)
        } else {
            DetailView(context: context)
        }
    }
}
```

## Add interactivity with a LiveActivityIntent

```swift
// Add interactivity with App Intents

struct RateDrinkIntent: LiveActivityIntent {
    static var title: LocalizedStringResource = "Rate Drink"

    @Parameter(title: "Order ID")
    var orderID: String

    @Parameter(title: "Positive")
    var isPositive: Bool

    func perform() async throws -> some IntentResult {
        await updateLocalDatastore(rating: isPositive ? .great : .poor, dismissPolicy: .after(.now + 15))
        return .result()
    }
}
```

## Associate an intent with a button

```swift
// Associate an intent with a button

struct RatingButtons: View {
    var context: ActivityViewContext<DrinkOrderAttributes>
    var body: some View {
        HStack(spacing: 12) {
            Button(intent: RateDrinkIntent(
                orderID: context.attributes.orderID.uuidString, isPositive: false)) {
                Label("Not Good", systemImage: "hand.thumbsdown.fill")
            }
            .buttonStyle(RatingButtonStyle(color: .red))

            Button(intent: RateDrinkIntent(
                orderID: context.attributes.orderID.uuidString, isPositive: true)) {
                Label("Great", systemImage: "hand.thumbsup.fill")
            }
            .buttonStyle(RatingButtonStyle(color: .green))
        }
    }
}
```
