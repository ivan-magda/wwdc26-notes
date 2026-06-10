# Session 219 — Enhance the accessibility of your reading app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/219/
- **Duration:** 20m
- **Speakers:** Josh (Software Engineer, Accessibility team)

## Description

How to make long-form text and reading apps accessible across Apple platforms.
Reading long-form content is fundamentally different from navigating UI — it is
about moving fluidly through text, not jumping between controls. The session
covers what makes a great reading experience for assistive technologies
(VoiceOver, Speak Screen, Accessibility Reader), how to extend the built-in
accessible text views in UIKit and SwiftUI, and how to make fully custom or
custom-rendered text (e.g. scanned pages) accessible by adopting `UITextInput`.

## Key topics

- Three goals of an accessible reading app: **granular text navigation**,
  **continuous reading**, **comprehensive text selection**
- Two target assistive technologies: **VoiceOver** (screen reader) and
  **Speak Screen** (reads a whole page top-to-bottom); plus the **Accessibility
  Reader** (since iOS 26)
- Standard text views already adopt `UITextInput`: UIKit `UITextView`, SwiftUI
  `TextEditor` and selectable `Text`, AppKit `NSTextView` — free line/word/
  character navigation and accessible selection
- **Text navigation APIs** (iOS 18) to link separate text elements:
  `accessibilityNextTextNavigationElement` /
  `accessibilityPreviousTextNavigationElement`
- SwiftUI **`accessibilityLinkedGroup(id:in:)`** (iOS 27) — same effect via a
  shared id + namespace; AppKit equivalent `accessibilitySharedTextUIElements`
- **`causesPageTurn`** accessibility trait + `accessibilityScroll` →
  audiobook-style automatic page advancing during read-all
- **Edit rotor custom actions**: `accessibilityCustomActions` with
  `UIAccessibilityCustomAction.editCategory` to surface selection actions
- Custom text: fully adopt **`UITextInput`** (`selectionRects(for:)`,
  `text(in:)`, tokenizer, text-range methods) + optional `UITextInteraction`
  for visible selection handles/highlights
- `UITextInputStringTokenizer` subclassing for a custom tokenizer

## Related sessions to fetch

- [ ] Creating an Accessible Reading Experience (`UIAccessibilityReadingContent`)

## Chapter summary (Summary tab)

- **0:01 Introduction** — Reading apps are an accessibility challenge distinct
  from UI navigation. Agenda: characteristics of a great reading experience,
  extending UIKit/SwiftUI text views, making custom text accessible.
- **1:26 Characteristics** — Reading needs fluid movement through text for
  VoiceOver and Speak Screen. Three goals: granular navigation, continuous
  reading, text selection — using UIKit, SwiftUI, and AppKit APIs. Demo app: a
  paginated Chicago travel guide.
- **3:45 Standard views** — `UITextView`, SwiftUI `TextEditor` / selectable
  `Text`, and `NSTextView` adopt `UITextInput` automatically (line/word/char
  navigation + accessible selection). `accessibilityNextTextNavigationElement` /
  `accessibilityPreviousTextNavigationElement` (and the new
  `accessibilityLinkedGroup` for SwiftUI) connect separate text elements;
  `causesPageTurn` provides automatic page turning during read-all gestures.
  Edit-rotor custom actions via the edit category.
- **14:05 Custom text** — For custom or custom-rendered text (e.g. scanned
  images), adopting the full `UITextInput` protocol gives the same granular
  navigation and selection as native views. Requires `selectionRects(for:)`, a
  tokenizer, and text-range methods, optionally paired with `UITextInteraction`
  for visible selection handles.

## Code

See `code.md` — 5 snippets extracted from the Code tab.
