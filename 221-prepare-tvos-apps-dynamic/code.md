# Code samples — Session 221

Extracted from the Code tab. Timestamps map to the transcript.

## Before — fixed font size and fixed-width caption (SwiftUI)

```swift
// Adopt standard text styles

VStack(spacing: 20) {
  Text("Signup information")
    .font(.caption.bold())
    .lineLimit(1)
    .foregroundStyle(.secondary)
    .frame(width: 300, alignment: .leading)
  HStack(alignment: .top, spacing: 40) {
      //* ... *//
  }
}
```

## After — semantic style + flexible width (SwiftUI)

```swift
// Adopt standard text styles

VStack(spacing: 20) {
  Text("Signup information")
    .font(.caption.bold())
    .lineLimit(1)
    .foregroundStyle(.secondary)
    .frame(maxWidth: .infinity, alignment: .leading)
  HStack(alignment: .top, spacing: 40) {
      /* ... */
  }
}
```

> Note: both Code-tab snippets above keep `.font(.caption.bold())`. The transcript
> describes migrating *from* a hard-coded `.font(.system(size:))` *to* `.caption`; the
> load-bearing diff in these extracted snippets is `width: 300` → `maxWidth: .infinity`.

## Hard-coded font vs. Dynamic Type (UIKit)

```swift
// Hard coded text size in UIKit

titleLabel.font = UIFont.boldSystemFont(ofSize: 28)

// Dynamic Type with text styles in UIKit

titleLabel.font = UIFont.preferredFont(forTextStyle: .headline)
titleLabel.adjustsFontForContentSizeCategory = true
```

## Adaptive grid columns via dynamicTypeSize (SwiftUI)

```swift
// A view that shows a collection of movie posters

struct MovieShelf: View {
  @Environment(\.dynamicTypeSize) private var dynamicTypeSize
  var body: some View {
    ScrollView(.horizontal) {
      LazyHStack(spacing: 40) {
        ForEach(Asset.allCases) { asset in
          Button {
            /* ... */
          } label: {
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

## Conditional stack axis with AnyLayout (SwiftUI)

```swift
// A view that shows content in a card

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

## Adaptive layout responding to content size changes (UIKit)

```swift
// UIKit adaptive layout that responds to content size changes

class AdaptiveLayoutViewController: UIViewController {
  let stackView = UIStackView()

  override func viewDidLoad() {
    super.viewDidLoad()
    updateLayout()

    let sizeTraits: [UITrait] = [UITraitPreferredContentSizeCategory.self]
    registerForTraitChanges(sizeTraits, action: #selector(updateLayout))
  }

  private func updateLayout() {
    if traitCollection.preferredContentSizeCategory.isAccessibilityCategory {
      stackView.axis = .vertical
    } else {
      stackView.axis = .horizontal
    }
  }

}
```

---

## Useful API facts surfaced by the code

- SwiftUI: read scale via `@Environment(\.dynamicTypeSize)`; `dynamicTypeSize.isAccessibilitySize` is the gate for "switch to an accessibility layout."
- `containerRelativeFrame(.horizontal, count:spacing:)` — vary the `count` to change how many cells fit per "page" of a horizontal scroller.
- `AnyLayout` type-erases `HStackLayout` / `VStackLayout` so a single `let layout = …` can flip orientation at runtime.
- Flexible width: `.frame(maxWidth: .infinity, alignment:)` instead of `.frame(width:)`.
- UIKit text scaling: `UIFont.preferredFont(forTextStyle:)` + `adjustsFontForContentSizeCategory = true`.
- UIKit live updates: `registerForTraitChanges([UITraitPreferredContentSizeCategory.self], action:)`; branch on `traitCollection.preferredContentSizeCategory.isAccessibilityCategory`.
