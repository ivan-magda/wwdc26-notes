# Code samples — Session 219

Extracted from the Code tab. Timestamps map to the transcript.

## 7:30 — Link text elements with the navigation APIs (UIKit)

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

## 8:01 — Link text elements with a linked group (SwiftUI, iOS 27)

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

> Note: the Code-tab extraction had `var paragraphs: [String` (missing the
> closing bracket); corrected to `[String]` above.

## 9:53 — Turn pages automatically after reading (causesPageTurn)

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

## 11:54 — Add a custom action to the editor rotor

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

## 14:54 — Adopt UITextInput on a custom (scanned) view

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
        guard let range = Range(nsRange, in: scannedText) else {
            return nil
        }
        return String(scannedText[range])
    }

    var tokenizer: any UITextInputTokenizer { CustomHandwritingTokenizer(textInput: self) }

    weak var inputDelegate: UITextInputDelegate?

    var selectedTextRange: UITextRange? {
        // Update visuals when assistive technologies change selection
        willSet { inputDelegate?.selectionWillChange(self) }
        didSet { inputDelegate?.selectionDidChange(self) }
    }
}
```

---

## Useful API facts surfaced by the code

- **Text navigation linking (UIKit):** set
  `accessibilityNextTextNavigationElement` /
  `accessibilityPreviousTextNavigationElement` on each element (iOS 18).
- **Text navigation linking (SwiftUI):** `accessibilityLinkedGroup(id:in:)`
  with a shared id + `@Namespace` (iOS 27). AppKit:
  `accessibilitySharedTextUIElements`.
- **Page turning:** insert `.causesPageTurn` into `accessibilityTraits` on the
  last element and implement `accessibilityScroll(_:)`; post
  `.pageScrolled` to announce progress.
- **Edit rotor action:** build a `UIAccessibilityCustomAction`, set
  `.category = .editCategory`, append it in `accessibilityCustomActions`.
- **Custom text:** conform to `UITextInput`; implement at minimum
  `selectionRects(for:)`, `text(in:)`, a `tokenizer` (e.g. subclass
  `UITextInputStringTokenizer`), and the text-range methods. Add a
  `UITextInteraction(for: .nonEditable)` for visible selection handles, and
  notify `inputDelegate` on selection changes.
