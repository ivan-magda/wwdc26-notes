# Code samples — Session 277

Extracted from the Code tab. The first three snippets are progressive versions of
the same `DailyReadingGoalWidget`, each adding one capability.

## DailyReadingGoalWidget — StaticConfiguration

```swift
struct DailyReadingGoalWidget: Widget {
    let kind = "DailyReadingGoalWidget"

    var body: some WidgetConfiguration {
        StaticConfiguration(
            kind: kind,
            provider: DailyReadingGoalProvider()
        ) { entry in
            DailyReadingGoalView(book: entry.book,
                                 message: entry.message,
                                 timeOfDay: entry.timeOfDay)
            .environment(\.colorScheme, .dark)
            .containerBackground(for: .widget) {
                Background()
            }
        }
    }
}
```

## Restricting sizes — .supportedFamilies

```swift
struct DailyReadingGoalWidget: Widget {
    let kind = "DailyReadingGoalWidget"

    var body: some WidgetConfiguration {
        StaticConfiguration(
            kind: kind,
            provider: DailyReadingGoalProvider()
        ) { entry in
            DailyReadingGoalView(book: entry.book,
                                 message: entry.message,
                                 timeOfDay: entry.timeOfDay)
            .environment(\.colorScheme, .dark)
            .containerBackground(for: .widget) {
                Background()
            }
        }
        .supportedFamilies([.systemMedium])
    }
}
```

## Deep link — widgetURL

```swift
struct DailyReadingGoalWidget: Widget {
    let kind = "DailyReadingGoalWidget"

    var body: some WidgetConfiguration {
        StaticConfiguration(
            kind: kind,
            provider: DailyReadingGoalProvider()
        ) { entry in
            DailyReadingGoalView(book: entry.book,
                                 message: entry.message,
                                 timeOfDay: entry.timeOfDay)
            .environment(\.colorScheme, .dark)
            .containerBackground(for: .widget) {
                Background()
            }
            .widgetURL(URL(string: "bookclub://reading/\(book.bookID)"))
        }
        .supportedFamilies([.systemMedium])
    }
}
```

## Accented rendering — widgetAccentedRenderingMode(.fullColor)

```swift
struct BookCoverImage: View {
    let imageName: String

    var body: some View {
        Image(imageName: bundle: .main)
            .widgetAccentedRenderingMode(.fullColor)
    }
}
```

> Note: the `Image(imageName: bundle: .main)` line is reproduced as it appears in
> the Code tab — likely a slide typo for `Image(imageName, bundle: .main)`.

---

## Useful API facts surfaced by the code

- `StaticConfiguration(kind:provider:)` with a view-builder closure taking a `TimelineEntry`.
- `containerBackground(for: .widget) { ... }` identifies the background view so the system can swap it for a glass material under tinted/clear modes.
- `.supportedFamilies([...])` declares which widget families the widget supports (e.g. `.systemMedium`, and the new `.systemExtraLargePortrait` on macOS/iOS/iPadOS 27).
- `.widgetURL(URL)` attaches a deep link handled on app launch.
- `.widgetAccentedRenderingMode(.fullColor)` forces a view (e.g. a book cover image) to keep its original colors instead of being accented in tinted/clear modes.
