---
title: "Use SwiftUI with AppKit and UIKit — Full Digest"
session: WWDC26 · 272
url: https://developer.apple.com/videos/play/wwdc2026/272/
duration: 14m
speakers: David Nadoba
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 272 — Use SwiftUI with AppKit and UIKit

## TL;DR

SwiftUI was built to live alongside AppKit and UIKit, so you can adopt it
incrementally instead of rewriting. Apple already does this — Logic Pro plugins,
Xcode's Coding Assistant, and even AppKit controls (`NSSlider`, `NSSwitch`,
`NSSegmentedControl`) and Liquid Glass render via SwiftUI under the hood. Using a
sample macOS lighting-control app, David Nadoba walks five integration points, each
of which can be adopted on its own:

1. **`@Observable` updates NSViews automatically** — track model reads inside
   `draw` / `layout` / `updateConstraints` / `updateLayer` and drop manual
   `needsDisplay = true`. Back-deployable to macOS 15 / iOS 18; default on in 2026.
2. **Host a SwiftUI view in AppKit** — reimplement a custom control as a SwiftUI
   `Canvas` (immediate-mode drawing) and embed it with `NSHostingView`.
3. **Reuse an AppKit gesture in SwiftUI** — `NSGestureRecognizerRepresentable` lets an
   existing `NSGestureRecognizer` subclass attach via the standard `.gesture` modifier.
4. **Build main-menu items in SwiftUI** — author them as a `View`, add to `NSMenu` via
   `NSHostingMenu`.
5. **Add SwiftUI scenes from an AppKit app delegate** — `NSHostingSceneRepresentation`
   registers a `MenuBarExtra` and a `Settings` scene from `NSApplicationDelegate`.

The throughline: a single `@Observable ColorModel` is reused unchanged across all five
surfaces. Every API shown ships in the 2026 releases or earlier. There is no
expectation that an app become fully SwiftUI to benefit.

---

## 1. Observation in AppKit (2:33)

The sample app's color picker has three custom sliders (subclasses of `NSSliderCell`
overriding draw methods like `drawKnob` and `drawBar`). A slider redraws itself when
its own value changes, but because hue affects the appearance of the saturation and
brightness sliders, those have to be invalidated by hand with `needsDisplay = true` —
and that has to be wired up for every cross-dependency and external change.

Adding `@Observable` to the model removes all of it. AppKit (and UIKit) track which
`@Observable` properties are read inside observation-aware methods and redraw
automatically when any of them change.

```swift
import Observation

@Observable @MainActor
final class ColorModel {
    var hue: Double = 0.6
    var saturation: Double = 1.0
    var brightness: Double = 1.0
}
```

Methods that participate in observation tracking:

- `NSView.draw(_:)` (and anything called as part of it, e.g. `NSSliderCell.drawKnob` /
  `drawBar`)
- `updateConstraints()`, `layout()`, `updateLayer()`
- The `NSViewController` equivalents
- On UIKit, even more: beyond `UIView` / `UIViewController` to `UIButton`,
  `UICollectionViewCell`, and more.

Back-deployment:

- macOS 15 — add `NSObservationTrackingEnabled` to `Info.plist`
- iOS 18 — add `UIObservationTrackingEnabled`
- Enabled by default in the 2026 releases and later.

Deep dive on the UIKit side: "What's new in UIKit" (WWDC25).

## 2. Hosting SwiftUI in AppKit (5:41)

Heuristic from the talk: when a new feature would require very different drawing or
interaction code, that's the moment to move to SwiftUI. Here, redesigning the picker
as a circular slider (an outer hue ring, two inner semicircles for saturation and
brightness, a center color preview) changes essentially all the drawing and gesture
code, so it's reimplemented in SwiftUI.

The new picker uses `Canvas` — an immediate-mode drawing API analogous to `drawRect`.
Each redraw hands you a fresh `GraphicsContext` you issue strokes, fills, transforms,
and filters against. Existing CoreGraphics code can be reused via `withCGContext`.

```swift
@Animatable
struct HSBColorPicker: View {
    var hue: Double
    var saturation: Double
    var brightness: Double
    @AnimatableIgnored var model: ColorModel

    var body: some View {
        Canvas { context, size in
            let metrics = PickerMetrics(size: size)
            drawPicker(in: &context, metrics: metrics, hue: hue, saturation: saturation, brightness: brightness)
        }
        .contentShape(Circle())
        .modifier(ColorPickerDragGesture(model: model))
        .aspectRatio(1, contentMode: .fit)
    }
}
```

(Full drawing/gesture/metrics implementation in `code.md`.) Crucially, the **same**
`@Observable ColorModel` from the AppKit picker is reused — no model changes.

To slot the SwiftUI view back into the many places where the picker lives in an
`NSView` hierarchy, wrap it in `NSHostingView` (itself an `NSView` subclass):

```swift
NSHostingView(
    rootView: HSBColorPicker(model: model)
)
```

Because the model was already `@Observable`, that's all that's needed. Related: "Add
rich graphics to your SwiftUI app" (WWDC21, Canvas), "Compose advanced graphics
effects with SwiftUI" (WWDC26, Metal shaders), and "Use SwiftUI with AppKit" / "Use
SwiftUI with UIKit" (WWDC22) for the hosting types in depth.

## 3. AppKit gestures in SwiftUI (7:48)

The app already has a `ForceClickGestureRecognizer` (an `NSGestureRecognizer`
subclass) used elsewhere — a force click is a firm trackpad press, detected at
pressure stage 2. Rather than reimplement it, the new `NSGestureRecognizerRepresentable`
protocol bridges the existing recognizer into SwiftUI.

```swift
struct ForceClickReset: NSGestureRecognizerRepresentable {
    var model: ColorModel

    func makeNSGestureRecognizer(context: Context) -> ForceClickGestureRecognizer {
        ForceClickGestureRecognizer()
    }

    func handleNSGestureRecognizerAction(_ recognizer: ForceClickGestureRecognizer, context: Context) {
        withAnimation {
            model.saturation = 1
            model.brightness = 1
        }
    }
}
```

The recognizer itself fires by observing `event.stage >= 2` in `pressureChange` and
setting `state = .ended`:

```swift
final class ForceClickGestureRecognizer: NSGestureRecognizer {
    private var didActivate = false

    override func pressureChange(with event: NSEvent) {
        if event.stage >= 2 && !didActivate {
            didActivate = true
            state = .ended
        }
    }
    // mouseDown / mouseUp reset didActivate and state = .possible
}
```

Attach it with the ordinary `.gesture` modifier, and it coexists with the existing
SwiftUI drag gesture with no other changes. The talk also notes the inverse direction:
`NSViewRepresentable` embeds `NSView`s into SwiftUI.

## 4. SwiftUI in the main menu (9:16)

Force Click isn't available on all input devices (Magic Mouse, the MacBook Neo
trackpad), so the reset needs a non-gesture path: a menu item with a keyboard
shortcut. The app's main menu is AppKit's `NSMenu`, but the menu content is authored in
SwiftUI as a plain `View` — Buttons with actions and `keyboardShortcut`, plus a
palette-style `Picker` for common colors.

```swift
struct ColorMenu: View {
    var model: ColorModel

    var body: some View {
        Button("Full Intensity") {
            withAnimation { model.saturation = 1; model.brightness = 1 }
        }
        .keyboardShortcut(.upArrow, modifiers: [.command, .shift])
        // Blackout / Brighten / Dim buttons, Dividers …

        Picker("Color", selection: Bindable(model).hue) {
            ForEach(Self.hues, id: \.hue) { entry in
                Label(entry.name, systemImage: "circle.fill")
                    .tint(Color(hue: entry.hue, saturation: 1, brightness: 1))
                    .tag(entry.hue)
            }
        }
        .pickerStyle(.palette)
    }
}
```

Bridge it into the AppKit menu with `NSHostingMenu` (an `NSMenu` subclass), set its
`title`, wrap it in an `NSMenuItem` as a submenu, and add that to the main menu:

```swift
let colorMenu = NSHostingMenu(rootView: ColorMenu(model: colorModel))
colorMenu.title = "Color"

let colorMenuItem = NSMenuItem()
colorMenuItem.submenu = colorMenu
mainMenu.addItem(colorMenuItem)
```

The rest of the AppKit app keeps working exactly as before — this is an incremental
addition.

## 5. SwiftUI scenes in AppKit (11:30)

Finally, complete SwiftUI **scenes** can be added from an existing
`NSApplicationDelegate` via `NSHostingSceneRepresentation`. The sample adds a
`MenuBarExtra` for quick light control and a `Settings` scene. A good place to register
them is `applicationWillFinishLaunching`:

```swift
func applicationWillFinishLaunching(_ notification: Notification) {
    let scenes = NSHostingSceneRepresentation {
        LightMenuBarExtra(appModel: model)
        LightSettings(appModel: model)
    }
    NSApplication.shared.addSceneRepresentation(scenes)
    openSettingsAction = { scenes.environment.openSettings() }
}

@IBAction func openSettings(_ sender: Any?) {
    openSettingsAction?()
}
```

Two nice touches:

- The `MenuBarExtra` uses `isInserted: Bindable(appModel).showMenuBarExtra` with
  `.menuBarExtraStyle(.window)`, so a `Toggle("Show in Menu Bar")` in the Settings
  scene inserts or removes it dynamically.
- `NSHostingSceneRepresentation` exposes an `environment` property carrying scene
  actions like `openSettings()`, callable from an `@IBAction` to open the Settings
  window from the AppKit main menu.

Related: "Bring multiple windows to your SwiftUI app" (WWDC22).

## Next steps (13:04)

- Start with `@Observable` to keep models and NSViews in sync — it also smooths the
  later move to SwiftUI.
- Reach for SwiftUI when building a new component or rewriting an existing one.
- Reuse existing gesture recognizer subclasses in SwiftUI via the representable
  protocol.
- Use SwiftUI for new scenes, even in existing apps.
- No expectation that an app be entirely SwiftUI to take advantage of it.
