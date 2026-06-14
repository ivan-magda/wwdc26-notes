---
title: "Enhance the accessibility of your reading app — Full Digest"
session: WWDC26 · 219
url: https://developer.apple.com/videos/play/wwdc2026/219/
duration: 20m
speakers: Josh (Accessibility team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 219 — Enhance the accessibility of your reading app

## TL;DR

Reading long-form text is a fundamentally different accessibility problem than
navigating UI. It is about **moving fluidly through running text** rather than
jumping between controls. The session frames the work as three goals and walks
a Chicago travel guide app from "uses system text views" to "renders scanned
handwritten pages but stays fully accessible."

1. **Reach for system text views first.** `UITextView`, SwiftUI `TextEditor` and
   selectable `Text`, and AppKit `NSTextView` already adopt **`UITextInput`**, so
   line/word/character navigation and accessible selection come for free.
2. **Three goals:** **granular text navigation**, a **continuous reading
   experience**, and **comprehensive text selection** — validated against
   VoiceOver and Speak Screen (and, since iOS 26, the **Accessibility Reader**).
3. **Three extension APIs** stitch a multi-view layout together: text-navigation
   linking (`accessibilityNextTextNavigationElement` /
   `accessibilityPreviousTextNavigationElement`, iOS 18; SwiftUI
   `accessibilityLinkedGroup`, iOS 27), the **`causesPageTurn`** trait for
   audiobook-style read-all, and **edit-rotor custom actions**.
4. **For custom or custom-rendered text** (scanned images, advanced typography,
   shared rendering engines), adopt the **full `UITextInput` protocol** yourself
   — `selectionRects(for:)`, `text(in:)`, a tokenizer, the range methods — to get
   native-grade navigation and selection back.

---

## 1. What makes a great reading experience

The demo app is a paginated Chicago travel guide: multiple paragraphs, text that
wraps across lines, content split across pages. Two assistive technologies are
the focus:

- **VoiceOver** — Apple's screen reader for blind/low-vision users. Reads
  whatever is under the cursor; granularity (lines/words/characters) is user-set;
  movement modes are switched with **rotors** (two-finger rotation).
- **Speak Screen** — reads a whole page top-to-bottom, highlighting as it goes.
  Triggered by a two-finger drag down from the top of the screen.

From these, three goals for the app:

1. **Granular text navigation** — VoiceOver/Speak Screen can move fluidly through
   text at any granularity.
2. **Continuous reading** — no interruptions, even across page boundaries.
3. **Comprehensive text selection** — selection works and selection-related
   actions are discoverable.

## 2. Standard text views (free accessibility)

Standard system text views all adopt **`UITextInput`**, the higher-fidelity
protocol native text views use:

- UIKit: **`UITextView`**
- SwiftUI: **`TextEditor`**, and a plain **`Text` with selection enabled**
  (`.textSelection(.enabled)`) — works on all Apple platforms
- AppKit (macOS): **`NSTextView`**

This gives line/word/character navigation with VoiceOver and Speak Screen, plus
accessible selection, out of the box. The guidance is explicit: **when your
app's constraints allow, always use these components first.**

> Aside: `UIAccessibilityReadingContent` is mentioned as a still-valid way to
> make full-page content accessible — you can layer it on top of everything
> here. It is covered in the older talk "Creating an Accessible Reading
> Experience" and is *not* the focus of this session.

In the demo, the layout requires **one `UITextView` per paragraph** (separate
views, not a single multi-paragraph view) — which is exactly what creates the
cross-element navigation problem in the next section.

## 3. Granular navigation across separate elements

With separate per-paragraph views, VoiceOver can navigate line-by-line *within*
a paragraph but gets stuck at the paragraph boundary (an audible "stuck" sound) —
you can't explore the whole page by line.

The fix is the **text navigation APIs (iOS 18)**: for each text element, return
the next/previous element VoiceOver should move to.

```swift
import UIKit

class TravelGuidePageController: UIViewController {

    var paragraphs: [TravelGuideParagraph]

    func configureNavigationElements() {
        for (index, paragraph) in paragraphs.enumerated() {
            if index + 1 < paragraphs.count {
                paragraph.accessibilityNextTextNavigationElement = paragraphs[index + 1]
            }
            if index - 1 >= 0 {
                paragraph.accessibilityPreviousTextNavigationElement = paragraphs[index - 1]
            }
        }
    }
}
```

After wiring this up, VoiceOver moves past the end of one paragraph onto the
first line of the next.

**SwiftUI (iOS 27)** achieves the same with **`accessibilityLinkedGroup(id:in:)`**
— link elements that share an id and namespace:

```swift
import SwiftUI

struct PageView: View {
    @Namespace private var pageNamespace
    var paragraphs: [String]
    var pageNumber: Int

    var body: some View {
        Text(paragraphs[0])
            .textSelection(.enabled)
            .accessibilityLinkedGroup(id: pageNumber, in: pageNamespace)

        Text(paragraphs[1])
            .textSelection(.enabled)
            .accessibilityLinkedGroup(id: pageNumber, in: pageNamespace)
    }
}
```

**AppKit on Mac:** use **`accessibilitySharedTextUIElements`** for a similar
result.

## 4. Continuous reading across pages (causesPageTurn)

Paginated content normally requires swiping between pages, and a read-all stops
at the bottom of the current page. The desired behavior is audiobook-style:
advance through all pages automatically.

Apply the **`.causesPageTurn`** accessibility trait to the **last** element on a
page (available in both UIKit and SwiftUI). Paired with **`accessibilityScroll`**,
Speak Screen and VoiceOver auto-scroll to the next page when they reach the end.

```swift
import UIKit

class TravelGuidePageController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        self.lastParagraphView.accessibilityTraits.insert(.causesPageTurn)
    }

    override func accessibilityScroll(_ direction: UIAccessibilityScrollDirection) -> Bool {
        moveToPage(direction)
        var scrollString = "Page \(currentPage) of \(pages.count)"
        UIAccessibility.post(notification: .pageScrolled, argument: scrollString)
        return true
    }
}
```

Posting the **`.pageScrolled`** notification announces progress ("Page X of Y").
In the demo, Speak Screen reads the Midday page, then automatically moves focus
to the Evening page and keeps reading.

## 5. Text selection + discoverable actions (edit rotor)

`UITextView` (and SwiftUI `TextEditor` / selectable `Text`) already gives
accessible selection. The app adds a "Save Recommendation" toolbar button to
save the current selection — but a toolbar button isn't obvious to VoiceOver.

Make it discoverable by adding a **custom action to VoiceOver's edit rotor**,
tagging it with the **edit category** so it lands with the selection-related
actions rather than generic ones:

```swift
import UIKit

class TravelGuideParagraph: UITextView {

    override var accessibilityCustomActions: [UIAccessibilityCustomAction]? {
        get {
            let saveAction = UIAccessibilityCustomAction(name: "Save Recommendation") { _ in
                self.saveRecommendation()
            }
            saveAction.category = UIAccessibilityCustomAction.editCategory
            return (super.accessibilityCustomActions ?? []) + [saveAction]
        }
        set { }
    }

    private func saveRecommendation() -> Bool {
        ...
        return true
    }
}
```

Demo flow: text-selection rotor → word selection mode → swipe to expand →
switch to the **Edit** rotor → activate **Save Selection**. Guidance: use the
edit category specifically for actions associated with text selection.

## 6. Accessibility Reader (bonus payoff)

Since **iOS 26**, the **Accessibility Reader** displays text content in a form
that is easier to consume; it can be launched from a Control Center control that
opens the app's content in the reader. Implementing the accessible-text
practices above improves the Accessibility Reader experience too — the same work
pays off across VoiceOver, Speak Screen, *and* the reader.

## 7. Custom text: adopt UITextInput yourself

System views aren't always an option. Custom text shows up in dedicated reading
apps for advanced typography, sharing a rendering engine across apps, or
displaying scanned pages. In the demo, paragraphs are replaced with **scanned
handwritten notebook pages** — which immediately loses all the free behavior,
down to reading the text at all (VoiceOver just announces "Image").

The fix: adopt the **`UITextInput`** protocol on the accessibility element. Fully
implemented, it makes rendered text or text-in-images as accessible as a native
text view — line-by-line touch exploration, rotor + Speak Screen navigation, and
selection. **Implement it in its entirety** to get the full benefit.

Problems you must solve:

- **Geometry / selection rects** — compute selection rectangles for a range, e.g.
  `selectionRects(for:)`. For handwriting from an image, the demo uses known
  per-line height/width to approximate rects via a custom
  `selectionRectFromImage`.
- **Substring for a range** — return just the queried portion of text in
  `text(in:)`.
- **Tokenizer** — drives line/sentence/word/character navigation. The demo
  subclasses **`UITextInputStringTokenizer`** (provided by UIKit) for a custom
  tokenizer.

```swift
import UIKit

class ScannedPage: UIView, UITextInput {

    override init(frame: CGRect) {
        super.init(frame: frame)
        let interaction = UITextInteraction(for: .nonEditable)
        interaction.textInput = self
        addInteraction(interaction)
    }

    func selectionRects(for range: UITextRange) -> [UITextSelectionRect] {
        var rects: [UITextSelectionRect] = []
        let startLine = lineIndex(for: range.start)
        let endLine = lineIndex(for: range.end)
        for line in startLine...endLine {
            let rect = selectionRectFromImage(for: range, in: line)
            rects.append(rect)
        }
        return rects
    }

    func text(in range: UITextRange) -> String? {
        let nsRange = nsRange(from: range)
        guard let range = Range(nsRange, in: scannedText) else { return nil }
        return String(scannedText[range])
    }

    var tokenizer: any UITextInputTokenizer { CustomHandwritingTokenizer(textInput: self) }

    weak var inputDelegate: UITextInputDelegate?

    var selectedTextRange: UITextRange? {
        willSet { inputDelegate?.selectionWillChange(self) }
        didSet { inputDelegate?.selectionDidChange(self) }
    }
}
```

- **Visible selection handles/highlights** — *not* required by `UITextInput`, but
  add a **`UITextInteraction(for: .nonEditable)`** and call the **input delegate**
  on selection changes (`selectionWillChange` / `selectionDidChange`) so the
  system updates the visuals and matches native expectations.
- The three earlier APIs still apply: **`causesPageTurn`** and the
  **navigation-element** APIs work great alongside a custom `UITextInput`, so the
  fully custom version of the app keeps cross-page read-all and cross-element
  navigation.

The final demo shows the scanned-page version doing line navigation, line
selection + Save Selection, and a clean read-all across pages.

## 8. Call to action

Audit your app with VoiceOver on: try the read-all gesture, navigate with the
**lines rotor**, and select text. If you use standard text views, adopt
**`causesPageTurn`** and the **text-navigation-element** APIs for smooth
cross-page reading. If you use custom rendered text, adopt **`UITextInput`**.
