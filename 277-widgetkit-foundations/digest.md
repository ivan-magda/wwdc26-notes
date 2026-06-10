---
title: "WidgetKit foundations — Full Digest"
session: WWDC26 · 277
url: https://developer.apple.com/videos/play/wwdc2026/277/
duration: 21m
speakers: Jonathan Long
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 277 — WidgetKit foundations

## TL;DR

A from-scratch primer on WidgetKit, structured around three acts: **build it**,
**integrate it**, **adapt it**. Jonathan Long uses a personal "Book Club" reading
app with three widgets (reading goals, reading log, reading schedule) to walk the
full lifecycle.

1. **Fundamentals** — A widget lives in a **widget extension** (separate process;
   share data via an **app group** container). WidgetKit asks the extension for a
   **timeline** of `TimelineEntry` values; each entry renders a SwiftUI view at a
   point in time, and the rendered views are **archived** and shown by the system.
2. **Configurations & reload** — `StaticConfiguration` vs `AppIntentConfiguration`;
   `TimelineProvider` supplies snapshot / placeholder / timeline; reload policies are
   `.atEnd`, `.afterDate(_)`, `.never`. Reloads are **budgeted** for battery.
3. **Families** — support as many sizes as make sense; the **`.systemExtraLargePortrait`**
   family (visionOS 26) comes to **macOS, iOS, and iPadOS 27**.
4. **Integration** — deep links (`widgetURL`), configurable widgets (App Intents),
   and interactive elements (buttons/toggles backed by App Intents).
5. **Adaptation** — widgets render through a **glass material** in tinted/clear modes;
   SwiftUI handles most of it; `widgetAccentedRenderingMode(.fullColor)` keeps images
   true to color. iOS widgets also surface on **CarPlay** and as **remote widgets on macOS**.

---

## 1. What makes a good widget

Three qualities the talk keeps returning to:

- **Glanceable** — understandable at a quick glance (e.g. Weather's current forecast).
- **Relevant** — content matches expectations for time, personal patterns, and
  location (e.g. Calendar showing the next events and updating through the day).
- **Personalizable** — configurable with the content that matters to the person
  (e.g. the Photos memory widget).

The running example is a Book Club app with three widgets:

- **Reading goals** — a motivational message that changes through the day.
- **Reading log** — tracks progress through a specific book; only refreshes on interaction.
- **Reading schedule** — a day-by-day plan recalculated nightly.

## 2. How an app provides widgets

- Whether the app is **SwiftUI or UIKit**, widgets themselves are always built with **SwiftUI**.
- Widgets ship from a **widget extension** that runs as a **separate process** from the app.
- Because it's a separate process, share data via a **shared container in an app
  group** — e.g. a shared database or user defaults.
- WidgetKit asks the extension for a **timeline** = a series of **timeline entries**,
  each carrying the data to render the widget's view at a specific point in time.
- The resulting views are **archived**, and the system displays them at their relevant time.

## 3. Building the widget (StaticConfiguration)

Xcode generates a widget when you add the extension target. The `body` returns a
`WidgetConfiguration`. Two kinds:

- **`AppIntentConfiguration`** — when the widget is user-configurable.
- **`StaticConfiguration`** — simplest; used here because the widget auto-configures
  from the current book.

`StaticConfiguration` takes a **`kind`** (unique identifier), a **`TimelineProvider`**,
and a **closure** mapping an entry to a SwiftUI view. The existing `DailyReadingGoalView`
is reused; `containerBackground(for: .widget)` marks the background so the system can
replace it with glass material under colored/clear tints.

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

## 4. The timeline provider — three states

The `TimelineProvider` supplies entries for three distinct states:

- **Snapshot** — a realistic preview shown in the **widget gallery**; the strong first
  impression. With no real data yet, the demo features a popular book (*Atomic Habits*)
  with a default message.
- **Placeholder** — a stand-in shown when there's no content yet (e.g. first load).
  Must be returned **synchronously**, so it can't fetch from disk or network — the demo
  uses SwiftUI's **`redacted`** modifier for a simplified view.
- **Timeline** — the actual entries, each rendered at its specific time (now or future).
  A `TimelineEntry` for the reading goals widget carries the motivational message, pages
  read, title, and cover name; only the message changes over time.

## 5. Keeping it fresh — reload policies

Timelines specify how they refresh via a **reload policy**:

- **`.atEnd`** — reload once all entries are exhausted. Good when the end isn't a single
  known time (the reading goals widget, whose message updates at varied times).
- **`.afterDate(_)`** — reload at a specific known date. Good for the reading schedule
  widget, which reloads at end-of-day to recalculate the downstream plan.
- **`.never`** — never reload on its own. Good for the reading log widget, which only
  refreshes on app/widget interaction.

When you need an explicit reload, call **`WidgetCenter`** reload APIs or send a **push
notification**.

**Best practices:**

- Provide **multiple** timeline entries so the system always has something to show.
- Reloads are **budgeted** for all-day battery; the budget is influenced by viewing
  habits and updated through the day. Frequent foreground reloads may be **throttled**.
- A final reload when the app **enters background** (if data may have changed) is usually wise.
- For ephemeral content with a start/end and alerting (e.g. a sporting event), use a
  **Live Activity** instead — see *Live Activities essentials* (WWDC26).

(Deeper reload-policy guidance: *Principles of great widgets*, WWDC21.)

## 6. Widget families & sizes

Support as many families as make sense so people have placement choices. Reuse the
same widget + timeline provider and supply a SwiftUI view tuned to each family's shape.

```swift
var body: some WidgetConfiguration {
    StaticConfiguration(kind: kind, provider: DailyReadingGoalProvider()) { entry in
        // view ...
    }
    .supportedFamilies([.systemMedium])
}
```

- New: **`.systemExtraLargePortrait`** — introduced in **visionOS 26**, now available on
  **macOS, iOS, and iPadOS 27**. Lets the reading schedule reuse the medium widget's data
  in a much larger canvas.
- Reach beyond the home screen: iOS widgets also appear on **CarPlay** and as **remote
  widgets on macOS**.

## 7. Integrating with your app

Three integration points beyond the default "tap opens the app":

### Deep links — `widgetURL`

```swift
.widgetURL(URL(string: "bookclub://reading/\(book.bookID)"))
```

The URL encodes the book ID so a tap launches straight to that book's details page.

### Configurable widgets (App Intents)

Let people personalize content — like Weather picking a location, or the reading log
picking which book to track. Configured in place (e.g. from the iOS home screen), and
people can add **multiple** instances with different configurations (three reading log
widgets for three books). Guidance: keep it to **one or two parameters**, provide a
**sensible default** (most recently read book), and **don't force** configuration up
front. (See *Explore enhancements to App Intents*, WWDC23.)

### Interactive elements

Buttons and toggles run actions directly from the widget (Reminders' check-off is the
canonical example). Because widget views are **archived and rendered by the system**,
your code isn't running on screen — so buttons/toggles take an **App Intent** the system
executes on your behalf. Expose your app's single most important action (e.g. a
"complete chapter" button). (See *Bring widgets to life*, WWDC23.)

## 8. Adapting to system appearance

On iOS the home screen can be **tinted** with a color or set to a **clear** tint. In
either case the system renders the widget through a **glass material**, tinting content
and swapping the background for an adaptive glass effect so everything feels cohesive.
SwiftUI does most of the work.

**The gotcha:** in clear mode the demo's book cover became a large white rectangle —
the system couldn't accent the asset-catalog image. The fix is to opt that image out
of accenting:

```swift
struct BookCoverImage: View {
    let imageName: String

    var body: some View {
        Image(imageName: bundle: .main)
            .widgetAccentedRenderingMode(.fullColor)
    }
}
```

`.fullColor` renders the cover in its original colors instead of being accented.

**Testing checklist:**

- Test on local devices in **full color, tinted, and clear** modes.
- Remember iOS widgets show up on macOS as **remote widgets** — verify interactions there.
- Use **SwiftUI previews** in the Xcode canvas to flip across families, color schemes,
  and rendering modes.
- Turn on **WidgetKit developer mode** to lift constraints like reload budgets while iterating.

(See *What's new in widgets*, WWDC25, for appearance adaptation.)
