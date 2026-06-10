# Session 289 — Modernize your AppKit app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/289/
- **Duration:** 18m
- **Speakers:** Ujjaini (Mac UI frameworks engineer)

## Description

A modern AppKit app takes advantage of how AppKit interfaces with the Mac, so its
form and function feel in harmony with the rest of the system. This session covers
three areas of that harmony: how people drive your app (modern precision input —
mouse, keyboard, status items), how the system manages it (graceful termination and
state restoration across launches), and how it looks and feels (Liquid Glass updates
and the new concentricity API in macOS 27).

## Key topics

- Replace `mouseDown:` overrides and tracking loops with gesture recognizers — the
  common event-handling language shared by AppKit, UIKit (Mac Catalyst), and SwiftUI
- View-based APIs for the common `mouseDown:` use cases: observe `selected` for
  selection, `.menu`/`menuForEvent:` for context menus, modern pasteboard delegate
  methods for drag and drop
- `NSTextSelectionManager` (new in macOS 27) — classic macOS text selection in any
  view outside `NSTextView`
- Control events come to AppKit (`addTarget:action:for:`), no `NSButton` subclass needed
- `hitTest` returning `nil` to let clicks fall through overlapping overlay views
- Keyboard navigation: `autorecalculatesKeyViewLoop` and the key view loop
- Status items with custom UI: the `NSStatusItemExpandedInterfaceSession` /
  `expandedInterfaceDelegate` API so AppKit can manage keyboard focus
- Graceful termination: `preventsApplicationTerminationWhenModal = false`
- State restoration via `NSWindowRestoration` (identifiers, autosave names,
  `isRestorable`, `restorationClass`, `encodeRestorableState`/`restoreState`,
  `invalidateRestorableState`)
- macOS 27 Liquid Glass refinements (mostly automatic): scroll edge effect, sidebar
  edges + semi-bold selection, interactive "bounce" glass effect
- Concentricity: `NSViewCornerConfiguration` + `.containerConcentric` corner radius

## Related sessions to fetch (referenced in this talk)

- [ ] Use SwiftUI with AppKit and UIKit (WWDC26)
- [ ] Gestures documentation (referenced, not a session)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Modern apps harmonize with the system across three areas:
  precision input, continuity across launches, and look and feel.
- **1:06 Modern input** — Precision input has been at the heart of the Mac since the
  first keyboard + mouse; modern APIs cover mouse events, keyboard navigation, status items.
- **1:27 Modern event handling with gesture recognizers** — Gesture recognizers are the
  modern way to handle mouse events; replace `mouseDown:` overrides and tracking loops.
- **2:25 Selection, context menus, and drag and drop** — View-based APIs: observe
  `selected` on collection/table types, `menuForEvent:`/`.menu` for context menus,
  modern pasteboard delegate methods for drag and drop.
- **3:52 Text selection in custom views** — `NSTextSelectionManager` brings classic
  text selection (bidirectional, drag and drop, toggling) to any view outside `NSTextView`.
- **4:26 Control events and gesture recognizers** — Control events react to
  user-driven tracking state changes; use `NSGestureRecognizer` for custom interactions.
- **5:51 Keyboard navigation and status items** — `autorecalculatesKeyViewLoop` keeps
  Tab navigation correct; use the expanded interface session API for custom status-item UI.
- **8:57 Continuity across launches** — Quitting and restoring seamlessly.
- **9:08 Graceful app termination** — Apps should quit without blocking; set
  `preventsApplicationTerminationWhenModal = false` on non-essential modals/sheets.
- **9:55 State restoration** — Use `NSWindowRestoration` to save and recover the UI
  across launches.
- **14:09 Design updates** — Where app and Mac meet at the UI: Liquid Glass and concentricity.
- **14:24 Liquid Glass updates in macOS 27** — Sidebars, scroll edge effects, toolbar
  items get refinements automatically; new interactive glass effect on click.
- **15:41 Concentricity** — `NSViewCornerConfiguration` + `.containerConcentric` so
  corner views match their container's corner radius.
- **16:59 Next steps** — Prioritize gesture recognizers and view-based APIs over
  `mouseDown:`, ensure full keyboard navigability, make quit/relaunch seamless, adopt concentricity.

## Code

See `code.md` — 14 snippets extracted from the Code tab.
