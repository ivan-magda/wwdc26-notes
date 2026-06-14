---
title: "Modernize your UIKit app — Full Digest"
session: WWDC26 · 278
url: https://developer.apple.com/videos/play/wwdc2026/278/
duration: 16m
speakers: Michael Ochs
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 278 — Modernize your UIKit app

## TL;DR

iOS 27 makes iPhone apps **fully resizable** — both in iPhone Mirroring on the Mac and
when an iPhone-only app runs on iPad. That single shift turns a pile of long-standing
"it'll always be portrait / always be the main screen / always be the phone idiom"
assumptions into bugs. Four headline themes:

1. **Adaptivity is now a hard requirement, not a nice-to-have.** Scene lifecycle is
   mandatory (no `UISceneDelegate` → the app won't launch). `UIScreen.main`, the user
   interface idiom, and supported interface orientation all stop being valid layout
   signals. The replacements are window-scene screen references, `traitCollection`,
   `effectiveGeometry`, and **size classes**.
2. **New bar / menu APIs** for the resizable world: iPhone sidebar opt-in, a pinned
   prominent tab, interactive navigation-bar minimization, a redesigned `.automatic`
   scroll edge effect, and Liquid-Glass menu image visibility control.
3. **Apple Intelligence hooks**: an automatic Ask Siri button in menus, the View
   Annotations API to expose `AppEntity` context, and Siri loading resources through
   your existing drag-and-drop handlers.
4. **An agentic shortcut**: a new Xcode 27 app-modernization skill that does much of
   this migration for you, exportable to other tools via `xcrun agent skills export`.

The mental model the talk keeps returning to is Bruce Nilo's 2014 line: *"A device
rotation is only an animated bounds change."* In iOS 27 it applies to every resize, on
every screen, not just rotation.

---

## 1. App adaptivity — why this matters now

Apps already run side by side, on different screen sizes, and on external displays. What
changed in iOS and macOS 27: apps in these environments are now **exposed to the full
range of dynamic changes** a native app must handle.

- **iPhone Mirroring on Mac** — the user can fully resize the iPhone window.
- **iPhone-only app on iPad** — fully resizable, like any iPad app.

A universal binary is a good start, but not enough. The audit has four areas: scene
lifecycle, main screen references, idiom checks, orientation checks.

## 2. Legacy API: app lifecycle → scene lifecycle

**Scene lifecycle is now required when building with the latest SDKs. Without a
`UISceneDelegate`, the app will no longer launch.** Most apps already adopt it; it's the
foundation for everything else in the talk.

If you haven't migrated: "Make your UIKit app more flexible" (WWDC25) and the doc
"Transitioning to the UIKit scene-based lifecycle."

## 3. Legacy API: main screen references

When the app is mirrored on a Mac, or moved to an external display on iPad, **the screen
associated with the scene changes**. Any `UIScreen.main` reference returns wrong
information for the current environment. Two strategies:

**(a) Read the screen dynamically from the window scene.** When a view/VC isn't in the
immediate context, pass a screen reference into the method that needs it.

```swift
// Access the correct screen through a windowScene
let screen = window?.windowScene?.screen

func generateThumbnail(_ image: UIImage, screen: UIScreen) -> UIImage {
    // existing code, replacing main screen with local screen reference
}
```

**(b) Better: remove screen references entirely.** Two common patterns:

**Scale** → use `traitCollection.displayScale`. Views/VCs keep their trait collections
current with reasonable fallbacks even off-screen. The system tracks which trait
properties are read inside common layout/drawing methods (`layoutSubviews`,
`updateProperties`, `drawRect`, and more) and re-invokes them when a tracked trait
changes — **automatic trait tracking**, no manual observation needed.

```swift
override func layoutSubviews() {
    super.layoutSubviews()
    // called again automatically when displayScale changes
    let displayScale = traitCollection.displayScale
}
```

Where automatic tracking isn't available, `registerForTraitChanges` runs a closure on a
specific trait change — use it to invalidate caches or update view-related data.

```swift
let displayScaleTrait: [UITrait] = [UITraitDisplayScale.self]
registerForTraitChanges(displayScaleTrait) {
    (view: GalleryView, previousTraitCollection: UITraitCollection) in
    view.cache.invalidate()
}
```

**Available space** → don't use screen bounds; the scene rarely owns the whole screen.
Use the window scene's **`effectiveGeometry`** (observe via
`windowScene(_:didUpdateEffectiveGeometry:)`), and in views/VCs prefer the view's /
superview's size over scene bounds. This makes UI presentation-independent — it adapts
better inside e.g. a split view controller.

```swift
func windowScene(
    _ windowScene: UIWindowScene,
    didUpdateEffectiveGeometry previousEffectiveGeometry: UIWindowScene.Geometry
) {
    let geometry = windowScene.effectiveGeometry
    let availableSpace = geometry.coordinateSpace.bounds
}
```

```swift
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()
    let availableSpace = view.bounds.size
}
```

## 4. Full-screen mode for games

`UIRequiresFullscreen` is now **honored on iPhone in resizable environments** (iOS 27),
but its behavior changed: it no longer opts the app fully out of resizing. Instead it
enables **discrete resizing** that honors supported interface orientations — every time
the scene size changes, the system transitions to a new screen configuration matching
that size, so the game always renders at full quality in the available space.

## 5. Legacy API: user interface idiom

The user interface idiom trait is **no longer meaningful for any layout decision**. An
iPhone app on iPad (or in iPhone Mirroring) stays in the **phone** idiom yet is fully
resizable. Stop branching layout on idiom. Use **size classes** to handle constraints
(collapsing menus, re-laying out for available space); for finer control, use the
surrounding view's size.

## 6. Legacy API: interface orientation

In iOS 27, supported interface orientation is **a preference to the system** and is
**ignored in resizable environments**. Don't use it for layout calculations. In iPhone
Mirroring the app **always runs in portrait** regardless of the scene's aspect ratio.
Replace orientation checks with size classes.

This is the iOS 8 idea finally enforced — Bruce Nilo, WWDC 2014: *"A device rotation is
only an animated bounds change."* With many device sizes, resizable iPad windows, and
resizable iPhone apps on Mac, it's more true than ever.

### Motion & location coordinate spaces

In iOS 27 `UIView` conforms to the new **Body protocols** from CoreMotion and
CoreLocation. Connect the managers to the view that visualizes the data (compass, map),
and the data stays in the correct coordinate space regardless of orientation.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    motionManager.deviceMotionBody = view
    locationManager.headingBody = view
}
```

## 7. Testing resizable apps

Xcode 27 lets you iterate across screen sizes without multiple simulators/devices: in
the new **Device Hub** app and in **Xcode Previews**, click the **enter resize mode**
icon and drag the device edges freely. Then verify **iPhone Mirroring and iPad on real
devices**. More in "Get the most out of Device Hub."

## 8. Tab bars and sidebars

On iPad, tab bars can expand into a sidebar when the environment supports it. On iPhone
the bottom tab bar shows across all sizes by default. New in iOS 27, **iPhone apps can
opt into a sidebar**:

```swift
tabBarController.sidebar.preferredPlacement = .sidebar
```

- Unlike iPad, this is an **app choice** — there's no UI toggle between sidebar and tab
  bar. The system decides whether there's room (e.g. when the horizontal size class is
  regular).
- Gate UI on availability:

```swift
tabBarController.sidebar.isAvailable
```

  If the sidebar isn't available, surface that UI behind nested tabs elsewhere.

- **Prominent tab** — always visible, even when the tab bar collapses during scroll:

```swift
let tabBarController = UITabBarController(tabs: tabs)
tabBarController.prominentTabIdentifier = "cart"
```

## 9. Navigation bars

Navigation bars can **interactively slide away on scroll** to free up space. By default
the system minimizes under certain conditions; force it either way:

```swift
navigationItem.barMinimizationBehavior = .always   // or .never
navigationItem.barMinimizationSafeAreaAdjustment = .never  // if you manage safe area yourself
```

**Scroll edge effects** got an updated appearance. Notably, **`.automatic` no longer
switches between soft and hard** — it has its own visuals for clarity. If you previously
overrode `.automatic`, re-evaluate, **especially `.soft`**, which no longer matches the
default system appearance.

## 10. Menus

Under the refined Liquid Glass look, **images on menu elements may not show by default**
in some contexts (menu bars on iPadOS and macOS). Force visibility with:

```swift
menuElement.preferredImageVisibility = /* visible */
```

Check the updated Human Interface Guidelines for when visible menu images are
appropriate.

## 11. Integrate with Apple Intelligence

- **Ask Siri in menus** — iOS 27 menus automatically show an Ask Siri button when
  there's content relevant for Siri, a starting point for a contextual conversation.
- **View Annotations API** — annotate specific views with `AppEntity`s to give Siri
  app-specific context. See "Explore advanced App Intents features for Siri and Apple
  Intelligence."
- **Drag-and-drop as a resource loader** — when Apple Intelligence is invoked from a
  context menu, the system calls your drag delegate methods to load content. Because
  **drag sessions can start without a user gesture**, do not animate or present modal UI
  from `sessionWillBegin`; move stateful UI into **`sessionDidMove`** instead.

## 12. Agentic coding — the modernization skill

New in Xcode 27: an **app modernization skill** with deep understanding of these
adaptivity tasks. With project context it can automatically:

- convert `UIScreen.main` calls to `traitCollection` or scene-bounds checks, adding
  invalidation logic where needed;
- replace interface orientation checks with size class checks;
- convert the app to **scene lifecycle**.

For complex tasks it asks clarifying questions; for work too large for one session it
leaves comments tracking what remains. Export the skills Xcode uses for other tools:

```bash
xcrun agent skills export
```

This produces markdown files importable into other agent workflows.

## Next steps

Build with the iOS 27 SDK, try the resizable simulator in Device Hub, test in iPhone
Mirroring on macOS 27, find the spots that need more flexibility, and try the agentic
modernization skill.
