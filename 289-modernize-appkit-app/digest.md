---
title: "Modernize your AppKit app — Full Digest"
session: WWDC26 · 289
url: https://developer.apple.com/videos/play/wwdc2026/289/
duration: 18m
speakers: Ujjaini (Mac UI frameworks engineer)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 289 — Modernize your AppKit app

## TL;DR

A practical "bring your AppKit app up to date" tour, organized around three places
where an app meets the Mac:

1. **Modern precision input** — Stop overriding `mouseDown:` and writing tracking
   loops. Gesture recognizers are the shared event-handling language across AppKit,
   UIKit (Mac Catalyst), and SwiftUI. AppKit ships view-based APIs for the common
   `mouseDown:` use cases (selection, context menus, drag and drop), a new
   `NSTextSelectionManager` for text selection in any view, **control events** (now
   in AppKit), full keyboard navigation, and a status-item expanded-interface session
   API so AppKit can manage focus for custom status-item UI.
2. **Continuity across launches** — Quit gracefully (don't block termination for
   modals that don't need it) and restore precisely via `NSWindowRestoration`.
3. **Look and feel in macOS 27** — Liquid Glass keeps evolving (mostly automatic
   refinements), plus a new interactive "bounce" glass effect and a **concentricity**
   API (`NSViewCornerConfiguration` + `.containerConcentric`) so corner views match
   their container's curvature.

The throughline: prioritize user intent and view-based / gesture APIs over low-level
event handling so your app stays in harmony with the platform.

---

## 1. Modern event handling with gesture recognizers

`mouseDown:` overrides and tracking loops were the historical go-to for interactive
behavior. But AppKit is one of three frameworks on the Mac — SwiftUI, UIKit (via Mac
Catalyst), and AppKit — and they share **gesture recognizers** as a common event
language. Gesture recognizers let AppKit provide advanced behaviors without you
building them by hand, with cross-framework compatibility.

Three solutions that interface well with gesture recognizers: **view-based APIs**,
**control events**, and **custom gesture recognizer subclasses**.

### Selection, context menus, drag and drop

Common `mouseDown:` overrides map to dedicated, more-reliable APIs:

- **Selection** — observe the `selected` property on types like `NSCollectionViewItem`
  and `NSTableRowView`, or use selection-change delegate callbacks
  (`NSTableViewDelegate`, `NSOutlineViewDelegate`).
- **Context menus** — three options:
  - `.defaultMenu` (class property on `NSView`) — every instance shows the same menu.
  - `.menu` (instance property on `NSResponder`) — a different menu per responder.
  - `menuForEvent:` (instance method on `NSView`) — build the menu dynamically from the event.
- **Drag and drop** — use modern dragging delegate methods like
  `tableView(_:pasteboardWriterForRow:)`. Similar methods exist on `NSCollectionView`,
  `NSOutlineView`, and `NSBrowser`.

```swift
// Modern dragging delegate methods
func tableView(_ tableView: NSTableView,
        pasteboardWriterForRow row: Int) -> (any NSPasteboardWriting)? {
    let pasteboardItem = NSPasteboardItem()
    pasteboardItem.setString(..., forType: .string)
    return pasteboardItem
}
```

### Text selection in custom views

`NSTextSelectionManager` is **new in macOS 27**. It uses gesture recognizers to bring
classic macOS text selection to any view outside `NSTextView`. Attach it to a view,
set up a text selection data source, and you get bidirectional selection, drag and
drop with text, toggling, and more.

### Control events (now in AppKit)

Control events — familiar from UIKit — arrive in AppKit. Add them to standard controls
(buttons, sliders) to react to user-driven tracking state changes without complex
`mouseDown:` tracking logic. AppKit calls the registered target/action when the event
fires. **No subclassing required**, and most of these control events have been
available since OS 10.11.

```swift
// Use control events
let button = NSButton()
button.addTarget(
    self,
    action: #selector(trackingEndedOutsideHandler),
    for: .trackingEndedOutside
)
```

For more control, add standard gesture recognizers; for maximum flexibility, write
custom `NSGestureRecognizer` subclasses (see the "Gestures" documentation).

### Gotcha: overlapping sibling views silently swallow clicks

Because gesture recognizers operate on a view and its subviews, an overlapping sibling
view can silently block mouse events. If a control isn't responding, check for an
overlapping sibling. Fix it by resizing the view, or — if it's an intentional overlay —
override `hitTest` to return `nil` so hit testing falls through to the content beneath.

```swift
override func hitTest(_ point: NSPoint) -> NSView? {
    return nil
}
```

## 2. Keyboard navigation and status items

Apps should respond to the keyboard as well as the mouse — important for speed,
accessibility, and the many power users who choose the Mac. With keyboard navigation
enabled in System Settings, focus moves between controls via Tab / Shift-Tab in the
order defined by the **key view loop**.

To keep that loop correct as views are added/removed, enable
`autorecalculatesKeyViewLoop` on the window. If you don't, you own maintaining the
loop yourself.

```swift
window.autorecalculatesKeyViewLoop = true
```

### Status items

Keyboard navigation extends into the menu bar and status items, which behave a little
differently from main-menu items:

- Status items that **show a menu** when clicked already behave like menu-bar menus.
- Status items that **trigger an action** — set `NSStatusItem.button`'s target/action
  (and optionally an image). It behaves like a regular button; the action fires
  automatically when Return is pressed during keyboard navigation.
- Status items with a **custom view** — set `NSStatusItem.view`, then add a
  target/action to perform the action.

For status items that show **custom windows / transient UI**, AppKit needs to know when
that UI is active so keyboard focus behaves correctly. Use the **expanded interface
session API**: set an `expandedInterfaceDelegate` when the item is created.

```swift
// Set the expanded interface delegate
@main class LightAppDelegate: NSObject, NSApplicationDelegate {
    lazy var lightStatusItem: NSStatusItem = { ... }()

    func applicationDidFinishLaunching(_ notification: Notification) {
        // ...
        lightStatusItem.expandedInterfaceDelegate = self
    }
}
```

The delegate receives begin/end calls to display or dismiss the window. Show the window
in `statusItem(_:didBegin:)` and order it out in
`statusItemDidEndExpandedInterfaceSession(_:animated:)`. When it's time to dismiss
(e.g., an action was selected), call `cancel()` on `expandedInterfaceSession`. The
session may also be canceled for you if focus naturally moves elsewhere.

```swift
// Implement the delegate methods
extension LightAppDelegate: NSStatusItemExpandedInterfaceDelegate {
    func statusItem(_ statusItem: NSStatusItem, didBegin session:
                    NSStatusItemExpandedInterfaceSession) {
        // Show window
    }
    func statusItemDidEndExpandedInterfaceSession(
        _ statusItem: NSStatusItem, animated: Bool) {
        // Hide window
    }
    func selectedAction() {
        // Take the action
        // Cancel session to request window dismissal
        lightStatusItem.expandedInterfaceSession?.cancel()
    }
}
```

> SwiftUI menu bar extras do a lot of this work for you. See WWDC26 **"Use SwiftUI with
> AppKit and UIKit"** for how an AppKit app can host a SwiftUI menu bar extra.

## 3. Continuity across launches

A great Mac app quits without pushback and comes back as if it was never quit. People
should be able to quit anytime — by choice, or because the system needs to reboot
(e.g., an overnight software update).

### Graceful termination

When an app presents a sheet, its window may not be able to close — and a window that
can't close blocks quit. `NSWindow.preventsApplicationTerminationWhenModal` defaults to
`true` for a good reason (avoid losing unsaved data). Set it to `false` for any
modal/sheet that doesn't strictly require intervention.

```swift
window.preventsApplicationTerminationWhenModal = false
```

### State restoration with NSWindowRestoration

Three steps: **opt in**, **encode** UI state, **decode** to restore windows + UI.

**Opt in / configure the window.** Set a window `identifier`. For common windows (main,
preferences), set an autosave name so the window restores to the same space and frame
(document windows don't need one). Set `isRestorable = true` so AppKit calls
`encodeRestorableState`/`restoreState` (and can auto-restore which window was minimized,
frontmost, or full screen). Set a `restorationClass` to be invoked on relaunch.

```swift
// Set window identifiers for state restoration
@MainActor class MainWindowController: NSWindowController, NSWindowDelegate {
    convenience init() {
        let window = NSWindow( ... )
        window.identifier = NSUserInterfaceItemIdentifier(WindowIdentifiers.mainWindow)
        window.setFrameAutosaveName(WindowIdentifiers.mainWindow)
        window.isRestorable = true
        window.restorationClass = WindowRestorationHandler.self
    }
}
```

**Encode.** Override `encodeRestorableState` to preserve what's needed to recreate the
window's state, and call `super`. Encode UI identifiers — **not** data that lives in
your document or database. The goal is to reconstruct the UI state, not re-serialize
the whole app. All `NSResponder`s have an `encodeRestorableState` you can override, so
manage view state too.

```swift
// Preserve state to recreate the UI
override func encodeRestorableState(with coder: NSCoder) {
    super.encodeRestorableState(with: coder)
    coder.encode(selectedProduct?.identifier.uuid,
                forKey: RestorationKeys.productIdentifier)
}
```

**Invalidate.** `encodeRestorableState` is only called when state has been invalidated.
Whenever a view-hierarchy change should alter saved state, call
`invalidateRestorableState()`; AppKit batches the encode later.

```swift
// Invalidate restorable state when the view hierarchy changes
splitViewController.onProductSelected = { [weak self] product in
    self?.invalidateRestorableState()
}
```

**Decode on relaunch — restore windows first, then UI.** In the restoration class,
implement `restoreWindow(withIdentifier:state:completionHandler:)`. It's called for
every restorable window; recreate the controller/window from the identifier and call
the completion handler with the window (or the error if creation fails). AppKit waits
on every restorable window, so **always** call the handler — save it and call it later
if you must.

```swift
// Restore windows
class WindowRestorationHandler: NSObject, NSWindowRestoration {
    static func restoreWindow(
        withIdentifier identifier: NSUserInterfaceItemIdentifier,
        state: NSCoder,
        completionHandler: @escaping (NSWindow?, Error?) -> Void
    ) {
        if identifier == .mainWindow, let window = appDelegate.mainWindowController?.window {
            completionHandler(window, nil)
        } else if identifier == .imageWindow {
            let controller = ImageWindowController()
            appDelegate.imageWindowControllers.append(controller)
            completionHandler(controller.window, nil)
        } else {
            completionHandler(nil, error)
        }
    }
}
```

Then restore the UI per window in the controller's `restoreState`, using the same coder
keys you encoded.

```swift
// Restore window UI
override func restoreState(with coder: NSCoder) {
    super.restoreState(with: coder)
    if let productId = coder.decodeObject(
        of: [NSString.self],
        forKey: RestorationKeys.productIdentifier) as? String {
        splitViewController?.selectedProductId = productId
    }
}
```

> Code sample: **"Restoring your app's state with AppKit."**

## 4. Design updates in macOS 27

### Liquid Glass (mostly automatic)

Liquid Glass, introduced in macOS 26, keeps evolving — if you adopted it in macOS 26,
you pick up changes for free on macOS 27:

- `NSScrollEdgeEffectStyle` automatically resolves to a **hard-edge** effect when
  there's free-floating text, like the window title in the title bar.
- **Sidebars** extend to the window's edges; selection uses a **semi-bold** text style
  for emphasis; content still flows behind them.
- **Bordered toolbar items** over the sidebar adopt Liquid Glass.

New in macOS 27: an **interactive glass effect** where the glass subtly bounces when
clicked, signaling the control is responding. Maps uses it for a few custom controls.
Apply it to controls/buttons or glass containers of interactive controls — not all
glass. "A little goes a long way."

### Concentricity

`NSViewCornerConfiguration` lets content meant for a corner adapt to its container's
shape instead of clashing with the window. The closer a view sits to the container's
corner, the more its radius should match. (Example: the local weather view in Maps is
concentric with the window.)

Subclass `NSView`, override `cornerConfiguration` to return an
`NSViewCornerConfiguration?`, and use `.containerConcentric` on `NSViewCornerRadius`
(with a minimum so corners are always rounded). Factory methods like `.uniformCorners`
keep the same radii across all four corners.

```swift
// Subclass NSView to override cornerConfiguration
class LocalWeatherView: NSView {
    override var cornerConfiguration: NSViewCornerConfiguration? {
        let radius: NSViewCornerRadius = .containerConcentric(minimumCornerRadius)
        return .uniformCorners(radius: radius)
    }
}
```

## Next steps (recap)

- Find `mouseDown:` overrides → replace with view APIs, control events, or gesture recognizers.
- Prioritize user intent over tracking loops.
- Make the app work as well from the keyboard as from the mouse.
- Make quit and relaunch feel seamless.
- Adopt concentricity in views and buttons.
