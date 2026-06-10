# Code samples — Session 370

Extracted from the Code tab. Timestamps map to the transcript chapters where the
relevant idea is discussed.

## Conform a view to NSTextViewportRenderingSurface

```swift
class MyView: UIView, NSTextViewportRenderingSurface {}
```

## Cache rendering surfaces keyed by layout fragment

```swift
class MyView: UIView, NSTextViewportRenderingSurface {}

var cache: NSMapTable<NSTextLayoutFragment, MyView>
```

## Bring a framework text view into SwiftUI (ViewRepresentable)

```swift
// Using a TextView in SwiftUI

import SwiftUI

struct MyTextView: View {
    var body: some View { TextViewRepresentable() }
}

#if os(macOS)
struct TextViewRepresentable: NSViewRepresentable {
    func makeNSView(context: Context) -> NSTextView {
      NSTextView()
    }
    func updateNSView(_ nsView: NSTextView, context: Context) {
    }
}
#else
struct TextViewRepresentable: UIViewRepresentable {
    func makeUIView(context: Context) -> UITextView {
        UITextView()
    }
    func updateUIView(_ uiView: UITextView, context: Context) {
    }
}
#endif
```

## Code editor: text view subclass + container scaffold

```swift
// Create a text view subclass for a code editor

import UIKit

class TextView: UITextView {}

class ContainerView: UIView {
    let textView = TextView()
    let lineNumberView = UIView()

    textView.font = UIFont.monospacedSystemFont
}
```

## Override the three viewport controller delegate methods

```swift
// Override viewport controller delegate methods

class TextView: UITextView {
    // Set up
    override func textViewportLayoutControllerWillLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerWillLayout(textViewportLayoutController)
        //...
    }

    // Get paragraph bounds
    override func textViewportLayoutController(_ textViewportLayoutController: NSTextViewportLayoutController, configureRenderingSurfaceFor textLayoutFragment: NSTextLayoutFragment) {
        super.textViewportLayoutController(textViewportLayoutController, configureRenderingSurfaceFor: textLayoutFragment)
        //...
    }

    // Share accumulated info back to ContainerView
    override func textViewportLayoutControllerDidLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerDidLayout(textViewportLayoutController)
        //...
    }
}
```

## Compute the starting line number with enumerateTextElements

```swift
func startingLineNumber(for viewportRange: NSTextRange?) -> Int {
    guard let viewportRange,
          let storage = textLayoutManager?.textContentManager
              as? NSTextContentStorage else { return 0 }
    let startLocation = storage.documentRange.location
    var count = 1
    storage.enumerateTextElements(from: startLocation) { element in
        guard let range = element.elementRange else { return true }
        if range.location.compare(viewportRange.location)
            != .orderedAscending { return false }
        count += 1
        return true
    }
    return count
}
```

## didLayout: convert to viewport coordinates and fire the closure

```swift
// Override viewport controller delegate methods

class TextView: UITextView {
    private var lines: [CGRect] = []
    private var startingLineNumber = 0
    var onDidLayout: ((Int, [CGRect]) -> Void)?

    // Share accumulated info back to ContainerView
    override func textViewportLayoutControllerDidLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerDidLayout(controller)
        let origin = controller.viewportBounds.origin
        onDidLayout?(startingLineNumber, lines.map { $0.offsetBy(dx: 0, dy: -origin.y) })
    }
}
```

## Draw line numbers in the ContainerView

```swift
// Draw line numbers in the ContainerView

class ContainerView: UIView {
    let textView = TextView()
    let lineNumberView = UIView()
    func setup() {
        textView.onDidLayout = { startingLineNumber, lines in
            let attributes: [NSAttributedString.Key: Any] = [
                .font: UIFont.monospacedSystemFont(ofSize: 11, weight: .regular),
                .foregroundColor: UIColor.secondaryLabel
            ]
            for (i, frame) in lines.enumerated() {
                let number = "\(startingLineNumber + i)" as NSString
                number.draw(at: CGPoint(x: 8, y: frame.minY),
                    withAttributes: attributes)
            }
        }
    }
}
```

## Collapsible sections via NSTextContentStorageDelegate

```swift
// Add collapsible sections to your text view

class TextView: UITextView, NSTextContentStorageDelegate {
    var collapsedSections: Set<Int> = []

    // Set up
    override func textViewportLayoutControllerWillLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerWillLayout(textViewportLayoutController)
        //...
    }

    // Get paragraph bounds
    override func textViewportLayoutController(_ textViewportLayoutController: NSTextViewportLayoutController, configureRenderingSurfaceFor textLayoutFragment: NSTextLayoutFragment) {
        super.textViewportLayoutController(textViewportLayoutController, configureRenderingSurfaceFor: textLayoutFragment)
        //...
    }

    // Share accumulated info back to ContainerView
    override func textViewportLayoutControllerDidLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerDidLayout(textViewportLayoutController)
        //...
    }

    // Skip layout for paragraphs marked as collapsed
    func textContentManager(shouldEnumerate textElement: NSTextElement, options: NSTextContentManager.EnumerationOptions) -> Bool {
        //...
    }

    // Handle section collapse toggling
    func toggleSection(headerOffset: Int) {
        if collapsedSections.contains(headerOffset) {
            collapsedSections.remove(headerOffset)
        } else {
            collapsedSections.insert(headerOffset)
        }
        guard let textLayoutManager = textLayoutManager else { return }

        let textViewportLayoutController = textLayoutManager.textViewportLayoutController
        textViewportLayoutController.delegate?.textViewportLayoutControllerReceivedSetNeedsLayout?(textViewportLayoutController)
    }
}
```

## Register a text attachment view provider reuse policy

```swift
// Cache text attachment view providers

import UIKit

class ViewController: UIViewController {

    var textView: UITextView

    func setupTextView() {
        textView = UITextView()
        textView.register(
            [.onEditingInlineParagraphs],
            forTextAttachmentViewProviderType: AnimatedAttachmentViewProvider.self
        )
    }
}
```
