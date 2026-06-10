---
title: "Elevate your app's text experience with TextKit — Full Digest"
session: WWDC26 · 370
url: https://developer.apple.com/videos/play/wwdc2026/370/
duration: 24m
speakers: Tarun Uday
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 370 — Elevate your app's text experience with TextKit

## TL;DR

For years, building text on Apple platforms forced a binary choice: take a framework
text view (`UITextView`, `NSTextView`, `TextEditor`) and get input, selection,
accessibility, undo/redo, dictation, and inline predictions for free — but with almost
no control over how text is drawn — or drop down to raw TextKit and build a custom
text view that gives total control but reimplements everything from scratch. This
session closes that gap.

Three headline changes in the 2027 releases:

1. **New rendering-surface abstraction** — `NSTextViewportRenderingSurface` (a protocol
   any UIView/NSView/CALayer can adopt) plus `NSTextViewportRenderingSurfaceKey` (a key
   that uniquely identifies a surface across viewport layout cycles; `NSTextLayoutFragment`
   works as one). This finally gives TextKit a vocabulary for the *destination views*
   that fragments are drawn into, so you can cache and reuse them.
2. **Framework text views are now extensible** — `UITextView` and `NSTextView` publicly
   conform to `NSTextViewportLayoutControllerDelegate`. Subclass them and override
   `willLayout` / `configureRenderingSurface` / `didLayout` to inject behavior into the
   viewport layout process — without giving up everything the framework view provides.
3. **Text attachment view-provider reuse** — `register(_:forTextAttachmentViewProviderType:)`
   with `onEditingInlineParagraphs` and `onScrollingOutOfViewport` reuse policies, so
   inline animated/stateful attachments survive edits and scrolling instead of being
   torn down and recreated.

The session demonstrates all of this with three `UITextView` subclass examples: a code
editor with line numbers, collapsible recipe sections, and an inline-animation
messaging attachment.

---

## 1. The convenience-vs-control tension

Two paths to a text editing experience on Apple platforms:

- **Framework text view** — `NSTextView` (AppKit), `UITextView` (UIKit), `TextEditor`
  (SwiftUI). You get text input, selection, accessibility, undo/redo, dictation, inline
  predictions, and more for free. They use TextKit internally, but that implementation
  is mostly hidden — limited ability to customize drawing or viewport behavior.
- **Custom text view** — use TextKit as the engine and render into your own view or
  layer. Set up an `NSTextLayoutManager`, implement viewport layout yourself, handle all
  rendering. Total control over storage, layout, and the viewport layout process — but
  you give up everything the framework views provide, and a production-quality editor
  from scratch is a lot of work.

Background sessions for deeper foundation: **"Meet TextKit 2"** (WWDC21) and
**"What's new in TextKit and text views"** (WWDC22).

## 2. TextKit's four-layer architecture

Rendering a long `NSAttributedString` in a custom text view, bottom to top:

1. **Text storage** — encapsulates the text data. `NSTextContentStorage` breaks the
   attributed string into paragraphs, creating an `NSTextParagraph` per paragraph.
   `NSTextContentStorage`/`NSTextParagraph` are the concrete `NSAttributedString`-backed
   types; for a different backing store, subclass the abstract `NSTextContentManager` /
   `NSTextElement`.
2. **Layout** — `NSTextLayoutManager` measures glyph metrics and produces an immutable
   `NSTextLayoutFragment` holding the computed layout for a paragraph. **Immutable**:
   editing a paragraph recreates both its `NSTextParagraph` and its `NSTextLayoutFragment`
   (e.g. replacing "sandwich" with "slider" rebuilds that paragraph's objects).
3. **Viewport** — tracks which fragments are currently visible. TextKit organizes all
   work around the viewport, rendering only what the user can see.
4. **View** — where text appears in your app.

The storage, layout, and viewport layers are **shared across all of Apple's UI
frameworks**; the "view" can be any drawable element a framework provides (in UIKit, a
`UIView` or a `CALayer`).

### The viewport layout process

`NSTextViewportLayoutController` (the "viewport controller") coordinates the layout
manager and the text view:

- The text view knows the scroll position and viewport size relative to the whole
  document and provides them to the viewport controller.
- The viewport controller asks the layout manager for all layout fragments intersecting
  the viewport and sends them to the text view for rendering.
- This repeats on **any change of viewport state** — scroll, edit, or selection. That
  coordination cycle is the **viewport layout process**, and it's central to TextKit's
  performance.

To build a custom text view: instantiate an `NSTextContentStorage` and an
`NSTextLayoutManager`, and render via an `NSTextViewportLayoutController` into its
delegate (a framework-provided view).

**Multiple presentations of one document:** connect multiple text layout managers to a
single text content storage; edits in one view propagate through the shared storage and
the views stay in sync automatically.

## 3. What's new: rendering surfaces

Before the 2027 releases, TextKit had no way to refer to the *destination views* where
text is rendered. It tracked layout fragments but not the views they were drawn into.

- **`NSTextViewportRenderingSurface`** — a new protocol representing a visual element
  inside the viewport that you draw into (the view that actually renders a fragment's
  text). Conform a `UIView`, `NSView`, or `CALayer` to it and use it in the viewport
  controller's delegate methods to track which views are visible.

```swift
class MyView: UIView, NSTextViewportRenderingSurface {}
```

- **`NSTextViewportRenderingSurfaceKey`** — a companion protocol. A key is any class
  that uniquely identifies a rendering surface **across viewport layout cycles**.
  `NSTextLayoutFragment` qualifies, so you can use it as a key to cache surfaces in map
  tables or dictionaries. The viewport layout process uses this key→surface mapping
  internally.

```swift
class MyView: UIView, NSTextViewportRenderingSurface {}

var cache: NSMapTable<NSTextLayoutFragment, MyView>
```

- Assign a rendering surface to a key during the process via the `renderingSurfaceFor`
  delegate method. Mappings are **cleared at the start** of each viewport layout
  process. Query a surface for a key within `didLayout` via the viewport controller's
  `renderingSurfaceFor` method.

## 4. Extending framework text views

`UITextView` and `NSTextView` power thousands of long-form experiences (Messages,
TextEdit, Notes, Journal). In SwiftUI the most convenient long-form option is
`TextEditor`, but you can also embed a `UITextView`/`NSTextView` via a ViewRepresentable:

```swift
// Using a TextView in SwiftUI

import SwiftUI

struct MyTextView: View {
    var body: some View { TextViewRepresentable() }
}

#if os(macOS)
struct TextViewRepresentable: NSViewRepresentable {
    func makeNSView(context: Context) -> NSTextView { NSTextView() }
    func updateNSView(_ nsView: NSTextView, context: Context) {}
}
#else
struct TextViewRepresentable: UIViewRepresentable {
    func makeUIView(context: Context) -> UITextView { UITextView() }
    func updateUIView(_ uiView: UITextView, context: Context) {}
}
#endif
```

The key unlock: **starting with the 2027 releases, `UITextView` and `NSTextView`
conform to `NSTextViewportLayoutControllerDelegate`.** Subclass and override the
delegate methods to add behavior to the viewport layout process — always calling `super`
first to preserve default text view behavior.

## 5. Example: code editor with line numbers

Goal: a code editor on iPad. Start with a `UITextView` subclass set to the monospaced
system font, then add line numbers.

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

A `ContainerView` holds the `UITextView` subclass plus a `UIView` for line numbers.
Whenever the viewport changes, recompute the visible paragraphs' line numbers. To know
when that happens, override the three viewport controller delegate methods:

```swift
class TextView: UITextView {
    override func textViewportLayoutControllerWillLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerWillLayout(textViewportLayoutController)
        //...
    }
    override func textViewportLayoutController(_ textViewportLayoutController: NSTextViewportLayoutController, configureRenderingSurfaceFor textLayoutFragment: NSTextLayoutFragment) {
        super.textViewportLayoutController(textViewportLayoutController, configureRenderingSurfaceFor: textLayoutFragment)
        //...
    }
    override func textViewportLayoutControllerDidLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerDidLayout(textViewportLayoutController)
        //...
    }
}
```

**State** added to the subclass: an array of paragraph bounds (`[CGRect]`), an integer
starting-line-number, and a closure to send the accumulated info up to the
`ContainerView`.

- **`willLayout`** — call `super`, clear the `lines` array, and compute the starting
  line number (count of all paragraphs *before* the viewport starts). That count uses
  `enumerateTextElements(from:)`:

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

  (The sample code caches this so you don't recount on every layout pass.)

- **`configureRenderingSurfaceFor:`** — call `super`, then append the fragment's
  `layoutFragmentFrame` to `lines`. Fires once per visible paragraph.

- **`didLayout`** — convert fragment frames from text-container coordinates to viewport
  coordinates (subtract the viewport origin), then fire the closure with the starting
  line number and adjusted frames:

```swift
class TextView: UITextView {
    private var lines: [CGRect] = []
    private var startingLineNumber = 0
    var onDidLayout: ((Int, [CGRect]) -> Void)?

    override func textViewportLayoutControllerDidLayout(_ textViewportLayoutController: NSTextViewportLayoutController) {
        super.textViewportLayoutControllerDidLayout(controller)
        let origin = controller.viewportBounds.origin
        onDidLayout?(startingLineNumber, lines.map { $0.offsetBy(dx: 0, dy: -origin.y) })
    }
}
```

The `ContainerView` sets the closure and draws each number at the right y position:

```swift
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
                number.draw(at: CGPoint(x: 8, y: frame.minY), withAttributes: attributes)
            }
        }
    }
}
```

Result: line numbers added to a `UITextView` in just a few lines of code.

## 6. Example: collapsible recipe sections

Goal: collapse each multi-paragraph recipe down to its heading. Start with the same
three viewport delegate methods, but also **skip layout** for collapsed paragraphs by
conforming the text view to `NSTextContentStorageDelegate` and implementing
`textContentManager(_:shouldEnumerate:)` to mark elements as collapsed or not.

Reminder on abstract vs concrete: `NSTextContentManager` is the abstract version of
`NSTextContentStorage`, and `NSTextElement` is the abstract version of `NSTextParagraph`.

State: a `Set<Int>` of paragraph offsets identifying collapsed sections, plus a toggle
method for disclosure taps. Toggling flips the offset in the set and tells the viewport
controller to relay out:

```swift
class TextView: UITextView, NSTextContentStorageDelegate {
    var collapsedSections: Set<Int> = []

    func textContentManager(shouldEnumerate textElement: NSTextElement, options: NSTextContentManager.EnumerationOptions) -> Bool {
        //...  return false to skip layout for a collapsed paragraph
    }

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

The three pieces: skip layout via the content-storage delegate, process each laid-out
paragraph via the viewport delegate methods, and handle disclosure-button taps. Result:
tap the triangle next to a recipe to collapse it to its heading — all inside `UITextView`.

## 7. Text attachments and view-provider reuse

Text views display more than text — inline photos, stickers (Messages), drawings, and
document scans (Notes). That non-text content is managed by TextKit as **text
attachments**, following the same architecture as regular text:

- An attachment is stored in the text storage like any other character, via
  `NSTextAttachment`.
- When the layout manager encounters one, it asks for an
  `NSTextAttachmentViewProvider` (the layout-layer object), which supplies the info to
  render the attachment.

**The problem:** because layout objects are immutable, editing a paragraph discards and
recreates all its instances — including the view provider. For an inline animation, the
view provider is recreated on every keystroke, so the animation restarts each edit.

**The fix** — register a reuse policy on `UITextView`:

```swift
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

- **`onEditingInlineParagraphs`** — preserves the view provider across paragraph edits,
  so keystrokes don't tear it down (animation no longer restarts).
- **`onScrollingOutOfViewport`** (shown in the sample) — caches the attachment's
  rendering surface when it scrolls off screen and restores it on return.
- The two policies compose; pass the view provider subclass type as the second argument
  and the text view manages all instances of that class.

## 8. Next steps

- Convenient-but-powerful rich text editor → kickstart with `UITextView` (UIKit) /
  `NSTextView` (AppKit); embed via ViewRepresentable in SwiftUI.
- Want more control → build custom text views with TextKit and the new rendering
  surface APIs.
- Sample app demonstrates line numbers, collapsible sections, and inline attachment
  reuse.
