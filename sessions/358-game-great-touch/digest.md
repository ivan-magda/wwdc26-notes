---
title: "Make your game great with touch — Full Digest"
session: WWDC26 · 358
url: https://developer.apple.com/videos/play/wwdc2026/358/
duration: 24m
speakers: Keyi Yu
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 358 — Make your game great with touch

## TL;DR

Players take their games everywhere on iPhone and iPad, but they don't always have a
controller. The new **Touch Controller framework** lets you add great on-screen touch
controls on top of a game that already uses the **Game Controller framework** — and
because a touch controller surfaces as a standard `GCController`, your existing input
logic barely changes. The talk is a four-act recipe, built around a Dredge-style demo
game:

1. **Set up** — create a `TCTouchController` from a descriptor, `connect()`, render it
   in your Metal pass, and forward UIKit touches.
2. **Flexible layouts** — nine anchor points + section grouping + `safeAreaInsets` so
   controls land comfortably on every screen and dodge the Dynamic Island / home
   indicator / rounded corners.
3. **Fluid interactions** — stop mirroring a physical pad one-to-one. Use contextual
   icons, hide unavailable controls, expand collider shapes to half the screen, drive
   sprint from tilt magnitude, and swap the right stick for a touchpad.
4. **Rich feedback** — lean on built-in press states, add custom layered visuals (a
   sprint halo), and collapse multi-finger combos (QTEs, aim-to-release throws) into
   single intuitive controls.

The throughline: **don't port the controller — redesign for the thumb.** A direct 1:1
mapping clutters the screen; thoughtful touch design makes the game "feel brand new."

---

## 1. Set up a touch controller

The Touch Controller framework builds **on top of** the Game Controller framework. If
your game already reacts to `GCController` connect/disconnect notifications and either
polls input or uses value-changed handlers, you're most of the way there. The framework
ships a rich set of button types and behaviors, lets you customize each control's
appearance, and **integrates directly with Metal** for performance.

Key idea: when Touch Controller is enabled it **shows up as a `GCController` object**,
so you poll its state or attach handlers exactly like any physical controller.

Game Controller's two input styles:

```swift
// Polling
if (button.isPressed) {
    // ...
}

// Change handlers
pressedInput.pressedDidChangeHandler = { (element: any GCPhysicalInputElement,
                                           input: any GCPressedStateInput,
                                           pressed: Bool)
    // ...
}
```

Setup is three calls plus touch forwarding: create from a descriptor, `connect()` to
enable (which automatically enables the game-controller logic), and `render(using:)`
in the Metal renderer. Then forward the UIKit touch lifecycle into the controller.

```swift
private(set) var touchController: TCTouchController?

let descriptor = TCTouchControllerDescriptor(mtkView: mtkView)
if TCTouchController.isSupported {
    touchController = TCTouchController(descriptor: descriptor)
}
touchController?.connect()
touchController?.render(using: renderEncoder)

override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    for touch in touches {
        touchControls.handleTouchBegan(at: touch.location(in: view), index: touch.hash)
    }
}

buttonA?.valueChangedHandler = { (_ button: GCControllerButtonInput, _ value: Float,
                                  _ pressed: Bool) in
    // ...
}
```

Do the same `handleTouch…` forwarding for `touchesEnded` and `touchesMoved`. What's
left is the interesting part: **which controls go where.**

## 2. Design flexible layouts

A flexible layout means the game feels comfortable on **any** screen size — important
now that Apple's unified gaming platform puts your game on Mac, iPad, and iPhone.

- **Nine anchor points** per layout. Assign a control an `anchor`, then position it
  with an `offset` relative to that anchor.
- **Sections** group related controls under the same anchor; as the device shape
  changes, each section keeps a consistent size and distance from its anchor, keeping
  controls at physically comfortable sizes while using screen space well.

**Design for fullscreen → respect safe areas.** Read `safeAreaInsets` from any
`UIView` and add them to your control offsets so nothing is clipped by rounded corners,
the home indicator, or the Dynamic Island.

**Placement strategy** — keep the center clear (character, movement, camera input).
Frequent/important actions go near the thumbs; menus and rarely used controls go up top.

Creating a control follows a descriptor → create → add pattern. Button B, anchored
bottom-right, with the offset adjusted for safe areas:

```swift
let buttonBDesc = TCButtonDescriptor()
buttonBDesc.label = TCControlLabel.buttonB     // maps to physical button B
buttonBDesc.anchor = .bottomRight
buttonBDesc.offset = adjustedOffset(CGPoint(x: -35, y: -106), for: buttonBDesc.anchor)
buttonBDesc.contents = .buttonContents(forSystemImageNamed: "b.circle",
                                       size: buttonBDesc.size, shape: .circle,
                                       controller: touchController)
touchController.addButton(descriptor: buttonBDesc)

func adjustedOffset(_ offset: CGPoint, for anchor: TCControlLayoutAnchor) -> CGPoint {
    // Adjust offset for other anchors ...
    case .bottomRight:
        x -= safeArea.right
        y -= safeArea.bottom
}
```

Because the label maps to the physical button B, **no new game logic is needed** — the
existing controller handler already fires. You're only placing a button on screen.

After adding every control this way, the demo has a working overlay that respects safe
areas and doesn't cover the character — but it's a **1:1 mirror of a physical pad**,
which clutters the screen. That's the cue for act three.

## 3. Design fluid interactions

The goal: controls that feel **native to touch**, not a controller overlay.

### Dynamic, contextual icons

On-screen controls can change appearance, unlike a physical button. Pick a glyph that
represents the actual function rather than a generic "B".

```swift
buttonBDesc.contents = .buttonContents(forSystemImageNamed: "figure.fencing",
                                       size: buttonBDesc.size,
                                       shape: .circle,
                                       controller: touchController)
```

When behavior changes with context, update the icon to match — here button B doubles as
strike / fireball / water power:

```swift
func setButtonBContents(symbolName: String) {
    for button in touchController.buttons {
        if button.label == TCControlLabel.buttonB {
            button.contents = .buttonContents(forSystemImageNamed: symbolName, size: buttonSize,
                                              shape: .circle, controller: touchController)
        }
    }
}

func cyclePower() {
    switch currentPower {
        case .strike:       touchControls?.setButtonBContents(symbolName: "figure.fencing")
        case .fireball:     touchControls?.setButtonBContents(symbolName: "flame.fill")
        case .waterBlaster: touchControls?.setButtonBContents(symbolName: "drop.fill")
    }
}
```

### Hide what players can't use

Don't leave dead controls on screen. Three mechanisms:

- **Thumbsticks** — `hidesWhenNotPressed = true`.
- **Buttons in a fixed spot** — `isEnabled = false`.
- **Repositioned controls** (e.g. a pickup button next to a nearby item) — add when
  shown, `removeControl` when dismissed.

```swift
let leftStickDesc = TCThumbstickDescriptor()
leftStickDesc.hidesWhenNotPressed = true
touchController.addThumbstick(descriptor: leftStickDesc)

func showPickupButton(at projectedPosition: CGPoint) {
    descriptor.offset = CGPoint(x: ptX, y: ptY)   // recompute each time
    touchController.addButton(descriptor: descriptor)
}

func hidePickupButton() {
    for button in touchController.buttons {
        if button.label == TCControlLabel.buttonY {
            touchController.removeControl(button)
        }
    }
}
```

### Controls as input *and* output

Touch controls can replace overlay UI. Instead of cycling actions in an overlay, show
the available powers directly as touch controls and auto-dismiss after a few seconds:

```swift
buttonX?.pressedChangedHandler = { (_ button: GCControllerButtonInput, _ value: Float,
                                    _ pressed: Bool) -> Void in
    if pressed { self.openPowerWheel() }
}

func openPowerWheel() {
    touchControls?.showPowerWheelButtons(fireballCount: fireballCount, has: hasWaterBlaster)
    wirePowerWheelHandlers()
    DispatchQueue.main.asyncAfter(deadline: .now() + 3.0) { [weak self] in
        guard let self = self, self.powerWheelActive else { return }
        self.closePowerWheel()
    }
}
```

### Movement and camera: use the whole screen

Physical sticks have a fixed size and the player can feel them; touch has neither.
Expand the input area to **half the screen** with `colliderShape` so the player doesn't
have to land precisely on a visual control.

```swift
let leftStickDesc = TCThumbstickDescriptor()
leftStickDesc.colliderShape = .leftSide   // not .circle
touchController.addThumbstick(descriptor: leftStickDesc)
```

**Sprint without a second finger.** On a pad, sprint = hold the stick *and* move it; on
touch that's two simultaneous fingers, which is hard. Instead, embed the sprint into
the stick itself and trigger it from **tilt magnitude** — small tilt walks, big tilt
sprints.

```swift
func pollInput() {
    if let gamePad = gameController.extendedGamepad {
        let gamePadLeft = gamePad.leftThumbstick
        var moveInput = simd_make_float2(gamePadLeft.xAxis.value, -gamePadLeft.yAxis.value)
        let magnitude = simd_length(moveInput)
        if magnitude > 0.8 {
            self.runModifier = 1.3
        }
        self.characterDirection = moveInput
    }
}
```

**Camera via a touchpad.** Mapping the right stick to camera causes over-rotation and
feels sluggish. A `TCTouchpad` gives speed *and* precision: the camera moves exactly as
far as the finger moves, with no latency or drift, and leaves no visible control on
screen.

```swift
let touchpadDesc = TCTouchpadDescriptor()
touchpadDesc.label = TCControlLabel.rightThumbstick   // reuse existing camera logic
touchpadDesc.colliderShape = .rightSide
touchpadDesc.reportsRelativeValues = true             // works anywhere on screen
touchController.addTouchpad(descriptor: touchpadDesc)
```

### Rethink multi-finger combos

Two demo cases need redesign, not direct mapping:

**Quick time event** — escaping a boss freeze means holding L1 + R1 *while* moving with
the left stick: too many fingers. Collapse the two buttons into one QTE button that's
always in the same spot, toggled with `isEnabled`:

```swift
func setupControls() {
    let desc = TCButtonDescriptor()
    desc.label = TCControlLabel(name: "escape_button", role: .button)
    touchController.addButton(descriptor: desc)
}
func showEscapeButton() { /* find it */ escapeButton.isEnabled = true }
func hideEscapeButton() { /* find it */ escapeButton.isEnabled = false }
```

**Aim-to-release power** — throwing a fireball requires aiming, moving, and releasing at
once. Combine aim + release into a single button: the `valueChangedHandler` drives the
release on press-state, and the raw drag delta is captured independently in
`touchesMoved` (because it's tracked separately from the button's pressed state).

```swift
buttonB?.valueChangedHandler = { (_ button: GCControllerButtonInput, _ value: Float,
                                  _ pressed: Bool) -> Void in
    self.releasePower(pressed: pressed)
}

override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?) {
    for touch in touches {
        let point = touch.location(in: metalView)
        if let gc = gameController, gc.isAiming {
            let prev = touch.previousLocation(in: metalView)
            gc.aimTouchDelta += simd_float2(Float(point.x - prev.x), Float(point.y - prev.y))
        }
    }
}
```

Now the player holds B, drags to aim, keeps moving with the left stick, and releases B
to fire — one finger for the whole throw.

## 4. Provide rich feedback

Every touch control should have a **visible pressed state**, and the framework provides
this by default — thumbsticks animate as they move, buttons highlight when pressed.

For visually busy games, go further with custom feedback. The demo adds a **glowing
halo** around the left thumbstick while sprinting by building `TCControlContents`
manually — it's essentially an array of layers, so you stack a halo `TCControlImage`
(from a Metal texture, sized slightly larger than the stick background) on top of the
standard background images and swap it in when sprint is active.

```swift
let haloLayer = TCControlImage(texture: haloTexture, size: haloSize, highlight: nil,
                               offset: .zero, tintColor: tint)
let normalBgImages = TCControlContents.thumbstickStickBackgroundContents(size: bgSize,
                                                                         controller: controller).images
haloThumbstickBg = TCControlContents(images: [haloLayer] + normalBgImages)
thumbstick.backgroundContents = active ? haloThumbstickBg : normalThumbstickBg
```

## The before / after

Starting point: every physical button mirrored onto the screen, cluttering the game.
After the four acts: left thumbstick appears only on touch; the pickup button shows only
near an item; the right half is an invisible camera touchpad with no over-rotation;
one button picks a power; hold-and-drag a single button to aim and release; a sprint
halo signals state. Players can jump in with **just two fingers, anywhere, anytime.**

## Next steps

Design your touch controls with the Touch Controller framework, test on multiple device
sizes, and iterate on player feedback. Related: **"Design great interfaces for handheld
games"** and **"Level up with Apple game technologies."**
