# Code samples — Session 289

Extracted from the Code tab. Labels derived from snippet content; timestamps map to
the matching transcript chapters.

## 3:41 — Modern dragging delegate methods

```swift
// Modern dragging delegate methods
func tableView(_ tableView: NSTableView,
        pasteboardWriterForRow row: Int) -> (any NSPasteboardWriting)? {
    let pasteboardItem = NSPasteboardItem()
    pasteboardItem.setString(..., forType: .string)
    return pasteboardItem
}
```

## 4:57 — Control events (no NSButton subclass)

```swift
// Use control events
let button = NSButton()
button.addTarget(
    self,
    action: #selector(trackingEndedOutsideHandler),
    for: .trackingEndedOutside
)
```

## 5:44 — Let clicks fall through an overlay with hitTest

```swift
override func hitTest(_ point: NSPoint) -> NSView? {
    return nil
}
```

## 6:24 — Auto-recalculate the key view loop

```swift
window.autorecalculatesKeyViewLoop = true
```

## 7:37 — Set the expanded interface delegate on a status item

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

## 7:52 — Implement the expanded interface session delegate

```swift
// Implement the delegate methods
extension LightAppDelegate: NSStatusItemExpandedInterfaceDelegate {
    // ...
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

## 8:16 — Cancel the session to dismiss the window

```swift
// Cancel the session when dismissing
extension LightAppDelegate: NSStatusItemExpandedInterfaceDelegate {
    // ...
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

## 9:45 — Allow graceful termination while a modal is up

```swift
window.preventsApplicationTerminationWhenModal = false
```

## 10:18 — Set window identifiers for state restoration

```swift
// Set window identifiers for state restoration
@MainActor class MainWindowController: NSWindowController, NSWindowDelegate {
    // ...
    convenience init() {
        let window = NSWindow( ... )
        // ...
        window.identifier = NSUserInterfaceItemIdentifier(WindowIdentifiers.mainWindow)
        window.setFrameAutosaveName(WindowIdentifiers.mainWindow)
        window.isRestorable = true
        window.restorationClass = WindowRestorationHandler.self
        // ...
    }
}
```

## 11:04 — Preserve state with encodeRestorableState

```swift
// Preserve state to recreate the UI
@MainActor class MainWindowController: NSWindowController, NSWindowDelegate {
    // ...
    override func encodeRestorableState(with coder: NSCoder) {
        super.encodeRestorableState(with: coder)
        // ...
        coder.encode(selectedProduct?.identifier.uuid,
                    forKey: RestorationKeys.productIdentifier)
        // ...
    }
    // ...
}
```

## 11:50 — Invalidate restorable state when the view hierarchy changes

```swift
// Invalidate restorable state when the view hierarchy changes
@MainActor class MainWindowController: NSWindowController, NSWindowDelegate {
    // ...
    convenience init() {
        // ...
        splitViewController.onProductSelected = { [weak self] product in
            self?.invalidateRestorableState()
        }
    }
}
```

## 12:26 — Restore windows in the restoration class

```swift
// Restore windows
class WindowRestorationHandler: NSObject, NSWindowRestoration {
    static func restoreWindow(
        withIdentifier identifier: NSUserInterfaceItemIdentifier,
        state: NSCoder,
        completionHandler: @escaping (NSWindow?, Error?) -> Void
    ) {
        //...
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

## 13:29 — Restore window UI with restoreState

```swift
// Restore window UI
@MainActor class MainWindowController: NSWindowController, NSWindowDelegate {
    //...
    override func restoreState(with coder: NSCoder) {
        super.restoreState(with: coder)
        if let productId = coder.decodeObject(
            of: [NSString.self],
            forKey: RestorationKeys.productIdentifier) as? String {
            splitViewController?.selectedProductId = productId
        }
        //...
    }
}
```

## 16:16 — Concentric corners with cornerConfiguration

```swift
// Subclass NSView to override cornerConfiguration
class LocalWeatherView: NSView {
    // ...
    override var cornerConfiguration: NSViewCornerConfiguration? {
        let radius: NSViewCornerRadius = .containerConcentric(minimumCornerRadius)
        return .uniformCorners(radius: radius)
    }
    // ...
}
```

---

## Spoken APIs not in the Code tab

- Selection observation: `selected` on `NSCollectionViewItem`, `NSTableRowView`;
  `NSTableViewDelegate`, `NSOutlineViewDelegate` selection-change callbacks
- Context menus: `.defaultMenu` (class property on `NSView`), `.menu` (instance on
  `NSResponder`), `menuForEvent:` (instance method on `NSView`)
- Drag delegate methods also exist on `NSCollectionView`, `NSOutlineView`, `NSBrowser`
- `NSTextSelectionManager` — new in macOS 27; attach to a view + text selection data source
- Status item triggers: set `NSStatusItem.button` target/action/image for action items;
  `NSStatusItem.view` for a custom view
- `NSScrollEdgeEffectStyle` — automatic resolution to hard-edge effect with free-floating title text
