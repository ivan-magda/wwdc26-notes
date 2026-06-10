# Session 272 — Use SwiftUI with AppKit and UIKit

- **URL:** https://developer.apple.com/videos/play/wwdc2026/272/
- **Duration:** 14m
- **Speakers:** David Nadoba (UI Frameworks team)

## Description

How SwiftUI is designed to work alongside existing AppKit and UIKit apps for
incremental adoption — no rewrite required. Apple already ships SwiftUI inside
AppKit (Logic Pro plugins, Xcode's Coding Assistant, and even AppKit controls like
NSSlider/NSSwitch/NSSegmentedControl render via SwiftUI under the hood). Using a
sample lighting-control app, the talk walks through five integration points:
adopting @Observable for automatic NSView redraws, hosting a SwiftUI Canvas view in
an AppKit hierarchy, reusing an NSGestureRecognizer in SwiftUI, building main-menu
items in SwiftUI, and adding complete SwiftUI scenes from an NSApplicationDelegate.

## Key topics

- **@Observable in AppKit/UIKit** — automatic redraw tracking in `draw`,
  `updateConstraints`, `layout`, `updateLayer`, and NSViewController/UIViewController
  equivalents; replaces manual `needsDisplay = true`. Back-deployable to macOS 15 /
  iOS 18 via `NSObservationTrackingEnabled` / `UIObservationTrackingEnabled`; default
  on in the 2026 releases.
- **Hosting SwiftUI in AppKit** — reimplement the color picker as a SwiftUI `Canvas`
  (immediate-mode drawing, `withCGContext` to reuse CoreGraphics code), then embed via
  `NSHostingView` (an `NSView` subclass).
- **AppKit gestures in SwiftUI** — `NSGestureRecognizerRepresentable`
  (`makeNSGestureRecognizer` + `handleNSGestureRecognizerAction`), attach with the
  standard `.gesture` modifier; reuses an existing `ForceClickGestureRecognizer`.
- **SwiftUI in the main menu** — build menu items as a regular `View` (Buttons with
  keyboard shortcuts, palette-style Picker), add to `NSMenu` via `NSHostingMenu` (an
  `NSMenu` subclass) wrapped in an `NSMenuItem`.
- **SwiftUI scenes in AppKit** — `NSHostingSceneRepresentation` +
  `NSApplication.shared.addSceneRepresentation` from `applicationWillFinishLaunching`;
  add a `MenuBarExtra` and a `Settings` scene; toggle the MenuBarExtra dynamically;
  open settings via `environment.openSettings()` from an `@IBAction`.

## Related sessions to fetch (referenced in this talk)

- [ ] What's new in UIKit (WWDC25) — Observation Tracking in UIKit
- [ ] Add rich graphics to your SwiftUI app (WWDC21) — Canvas intro
- [ ] Compose advanced graphics effects with SwiftUI (WWDC26) — SwiftUI + Metal shaders
- [ ] Use SwiftUI with AppKit (WWDC22) — NSHostingView deep dive
- [ ] Use SwiftUI with UIKit (WWDC22) — UIHostingController deep dive
- [ ] Bring multiple windows to your SwiftUI app (WWDC22) — SwiftUI scenes

## Chapter summary (Summary tab)

- **0:00 Introduction** — David Nadoba; SwiftUI designed to work alongside AppKit/UIKit
  for incremental adoption. Already in Logic Pro plugins, Xcode's Coding Assistant, and
  AppKit controls (NSSlider, NSSwitch, NSSegmentedControl) rendering via SwiftUI.
  Agenda previewed with a sample lighting-control app.
- **2:33 Observation in AppKit** — `@Observable` on the model gives automatic redraws;
  AppKit/UIKit track property reads in `draw`, `updateConstraints`, `layout`,
  `updateLayer`, and controller equivalents. Back-deploy via
  `NSObservationTrackingEnabled` / `UIObservationTrackingEnabled`; default on in 2026.
- **5:41 Hosting SwiftUI in AppKit** — reimplement the color picker as a SwiftUI
  `Canvas` (immediate-mode, `withCGContext` for CoreGraphics reuse); embed with
  `NSHostingView`.
- **7:48 AppKit gestures in SwiftUI** — reuse `NSGestureRecognizer` via
  `NSGestureRecognizerRepresentable`; attach with `.gesture`. Adds a Force Click reset
  alongside the existing drag gesture.
- **9:16 SwiftUI in the main menu** — build the menu as a SwiftUI `View`; add to the
  AppKit main menu via `NSHostingMenu` wrapped in an `NSMenuItem`. Provides the reset
  feature to input devices without force gestures.
- **11:30 SwiftUI scenes in AppKit** — `NSHostingSceneRepresentation` adds full SwiftUI
  scenes from an AppKit lifecycle. A `MenuBarExtra` plus a `Settings` scene whose Toggle
  inserts/removes the MenuBarExtra, all from the existing `NSApplicationDelegate`.
- **13:04 Next steps** — start with `@Observable`, reach for SwiftUI on new/rewritten
  components, reuse gestures via the representable protocol, use SwiftUI for new scenes.
  No expectation an app be entirely SwiftUI to benefit.

## Code

See `code.md` — 6 snippets extracted from the Code tab.
