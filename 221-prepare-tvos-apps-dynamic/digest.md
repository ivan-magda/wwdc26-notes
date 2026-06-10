---
title: "Prepare your tvOS apps for Dynamic Type — Full Digest"
session: WWDC26 · 221
url: https://developer.apple.com/videos/play/wwdc2026/221/
duration: 10m
speakers: Isis
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 221 — Prepare your tvOS apps for Dynamic Type

## TL;DR

Dynamic Type / Large Text lands **system-wide on tvOS 27** — the same feature
iOS has had for years, now on the TV. Three headline points:

1. **It's mostly free.** Standard UIKit and SwiftUI components — Labels, Buttons,
   navigation tab bars — scale automatically. Your job is to find and fix the
   *custom* spots that don't.
2. **Two failure modes to hunt for:** hard-coded **font sizes** (text won't grow)
   and rigid **width/height constraints** (text truncates/clips). Fix with
   semantic text styles + flexible frames (`maxWidth: .infinity`); in UIKit add
   `adjustsFontForContentSizeCategory = true`.
3. **When scaling isn't enough, adapt the layout.** Read `dynamicTypeSize` to drop
   a grid from 6 columns to 4, or use `AnyLayout` to flip a card from HStack to
   VStack at accessibility sizes. UIKit does the same via `UIStackView.axis` +
   `registerForTraitChanges`.

Payoff beyond accessibility: you can advertise Larger Text support in your
**Accessibility Nutrition Labels for tvOS**, which surfaces the app to users who
filter for accessible apps. The running demo is a movie/media app.

---

## 1. How Large Text works on tvOS

- New on **tvOS 27**: Large Text brings system-wide text scaling to every app.
- If you know **Dynamic Type on iOS, you have a head start** — it works the same
  way. UIKit and SwiftUI adapt text sizes automatically based on user preference.
- Users enable it in **Settings → Accessibility → Display → Text Size**, choosing
  sizes from **Large** up to **Accessibility XXXL**.
- In the media-app demo, the navigation title, tab-bar labels, and all interface
  text scale up. Standard components handle this with no code. You only touch
  custom elements.

## 2. Identifying common issues

Two categories of problem when running with Large Text:

- **Fixed font sizes** — text that won't scale when the size preference changes.
- **Hard-coded width/height constraints** — cause truncation of text or clipping
  of UI, and may need extra padding/spacing as elements grow.

Worked example: a movie **Description View**. Most text scales, but the
"Signup information" caption doesn't, because it was given a fixed font size and a
fixed 300-point width "for a specific layout reason." Appealing for predictable
layouts, but not recommended — it can't adapt.

### The fix (SwiftUI)

Replace the hard-coded font with a semantic style (`.caption`), then replace the
fixed width with a flexible one. The Code-tab snippets show the load-bearing
change as `width: 300` → `maxWidth: .infinity`:

```swift
// Before
VStack(spacing: 20) {
  Text("Signup information")
    .font(.caption.bold())
    .lineLimit(1)
    .foregroundStyle(.secondary)
    .frame(width: 300, alignment: .leading)   // ← fixed width truncates
  HStack(alignment: .top, spacing: 40) { /* ... */ }
}

// After
VStack(spacing: 20) {
  Text("Signup information")
    .font(.caption.bold())
    .lineLimit(1)
    .foregroundStyle(.secondary)
    .frame(maxWidth: .infinity, alignment: .leading)   // ← grows as needed
  HStack(alignment: .top, spacing: 40) { /* ... */ }
}
```

`maxWidth: .infinity` tells SwiftUI to use as much width as the content needs.
First switch to the semantic style (text now grows but truncates), then relax the
width constraint (text fits).

### The fix (UIKit)

Same idea, plus one extra step — opt into automatic updates:

```swift
// Hard coded
titleLabel.font = UIFont.boldSystemFont(ofSize: 28)

// Dynamic Type
titleLabel.font = UIFont.preferredFont(forTextStyle: .headline)
titleLabel.adjustsFontForContentSizeCategory = true   // update on preference change
```

**Audit checklist:** search the app for hard-coded text sizes → migrate to
standard styles; search for hard-coded height/width constraints → use flexible
ones.

## 3. Adapting layout when scaling isn't enough

Sometimes bigger text simply doesn't fit, and you want to change the layout for
large sizes while preserving the default-size layout.

### Reduce grid columns (SwiftUI)

A horizontal shelf of six movie posters: at standard size, six titles fit; at
Large Text there isn't room for six. Read `dynamicTypeSize` and reduce the column
count in `containerRelativeFrame` from 6 to 4, giving each cell more width:

```swift
struct MovieShelf: View {
  @Environment(\.dynamicTypeSize) private var dynamicTypeSize
  var body: some View {
    ScrollView(.horizontal) {
      LazyHStack(spacing: 40) {
        ForEach(Asset.allCases) { asset in
          Button { /* ... */ } label: {
            asset.portraitImage
            Text(asset.title)
          }
          .containerRelativeFrame(
            .horizontal,
            count: dynamicTypeSize.isAccessibilitySize ? 4 : 6,
            spacing: 40)
        }
      }
    }
  }
}
```

For *even longer* titles, consider a custom **marquee** strategy.

### Flip stack orientation (SwiftUI)

Content cards (image + title + subtitle) run out of horizontal padding at large
sizes. Use `AnyLayout` to switch from horizontal to vertical so title and subtitle
get the full cell width (and the cell grows taller):

```swift
struct CardContentView: View {
  @Environment(\.dynamicTypeSize) private var dynamicTypeSize
  var asset: Asset
  var body: some View {
    let layout = dynamicTypeSize.isAccessibilitySize ?
      AnyLayout(VStackLayout(alignment: .leading, spacing: 10)) :
      AnyLayout(HStackLayout(alignment: .top, spacing: 10))
    layout {
      /* ... */
    }
  }
}
```

`AnyLayout` type-erases the two layout types so a single value can drive either.

### Same in UIKit

Drive `UIStackView.axis` off `preferredContentSizeCategory.isAccessibilityCategory`,
and register for live trait changes so it updates while the app is running:

```swift
class AdaptiveLayoutViewController: UIViewController {
  let stackView = UIStackView()
  override func viewDidLoad() {
    super.viewDidLoad()
    updateLayout()
    let sizeTraits: [UITrait] = [UITraitPreferredContentSizeCategory.self]
    registerForTraitChanges(sizeTraits, action: #selector(updateLayout))
  }
  private func updateLayout() {
    stackView.axis = traitCollection.preferredContentSizeCategory.isAccessibilityCategory
      ? .vertical : .horizontal
  }
}
```

## 4. Action plan

- Use **standard text styles** instead of hard-coded fonts.
- **Test systematically** with Large Text enabled.
- **Adapt layouts** when needed for the best experience.
- Indicate **Larger Text support** in your app's **Accessibility Nutrition Labels
  for tvOS**.
