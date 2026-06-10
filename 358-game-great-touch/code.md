# Code samples — Session 358

Extracted from the Code tab. Timestamps are approximate, mapped to the transcript.

## Game Controller input: polling vs. change handlers

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

## Set up a TCTouchController

```swift
// Set up a TCTouchController
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

## Create a circular button B with a safe-area-adjusted offset

```swift
// Create a standard circular button B
let buttonBDesc = TCButtonDescriptor()
buttonBDesc.label = TCControlLabel.buttonB
buttonBDesc.anchor = .bottomRight
buttonBDesc.offset = adjustedOffset(CGPoint(x: -35, y: -106), for: buttonBDesc.anchor)
buttonBDesc.contents = .buttonContents(forSystemImageNamed: "b.circle",
                                       size: buttonBDesc.size, shape: .circle,
                                       controller: touchController)
// Set other properties ...
touchController.addButton(descriptor: buttonBDesc)

func adjustedOffset(_ offset: CGPoint, for anchor: TCControlLayoutAnchor) -> CGPoint {
    // Adjust offset for other anchors ...
    case .bottomRight:
        x -= safeArea.right
        y -= safeArea.bottom
}
```

## Change a button's icon to reflect its action

```swift
// Change icon image
buttonBDesc.contents = .buttonContents(forSystemImageNamed: "figure.fencing",
                                       size: buttonBDesc.size,
                                       shape: .circle,
                                       controller: touchController)
```

## Update button B's icon based on game state

```swift
// Update contents for button B based on context
func setButtonBContents(symbolName: String) {
    for button in touchController.buttons {
        if button.label == TCControlLabel.buttonB {
            button.contents = .buttonContents(forSystemImageNamed: symbolName, size: buttonSize,
                                              shape: .circle, controller: touchController)
        }
    }
}

func cyclePower() {
    // Get the current power type ...
    switch currentPower {
        case .strike:       touchControls?.setButtonBContents(symbolName: "figure.fencing")
        case .fireball:     touchControls?.setButtonBContents(symbolName: "flame.fill")
        case .waterBlaster: touchControls?.setButtonBContents(symbolName: "drop.fill")
    }
}
```

## Hide a thumbstick when it isn't touched

```swift
// Hide left thumbstick when it is not touched
let leftStickDesc = TCThumbstickDescriptor()
leftStickDesc.hidesWhenNotPressed = true
// Set other properties ...
touchController.addThumbstick(descriptor: leftStickDesc)
```

## Show / hide a contextual pickup button by add + remove

```swift
// Show pickup button when there's an item nearby
func showPickupButton(at projectedPosition: CGPoint) {
    // Calculate the position(ptX, ptY) for pickup button ...
    descriptor.offset = CGPoint(x: ptX, y: ptY)
    // Set other properties ...
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

## Replace an overlay: show power options as direct touch controls

```swift
// Show power options as touch controls
buttonX?.pressedChangedHandler = { (_ button: GCControllerButtonInput, _ value: Float,
                                    _ pressed: Bool) -> Void in
    if pressed {
        self.openPowerWheel()
    }
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

## Full-half-screen collider shape for the movement thumbstick

```swift
// Use the left half of the screen for character movement
let leftStickDesc = TCThumbstickDescriptor()
leftStickDesc.colliderShape = .leftSide // Don't set as .circle
// Set other properties ...
touchController.addThumbstick(descriptor: leftStickDesc)
```

## Sprint via thumbstick tilt magnitude (no second finger)

```swift
// Calculate left thumbstick's tilt magnitude to trigger sprint
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

## Replace the right thumbstick with a TCTouchpad for camera control

```swift
// Replace right thumbstick with touchpad
let touchpadDesc = TCTouchpadDescriptor()
touchpadDesc.label = TCControlLabel.rightThumbstick
touchpadDesc.colliderShape = .rightSide
touchpadDesc.reportsRelativeValues = true
// Set other properties ...
touchController.addTouchpad(descriptor: touchpadDesc)
```

## Collapse two QTE buttons into one, toggled with isEnabled

```swift
// Collapse 2 QTE buttons into 1 single button
func setupControls() {
    let desc = TCButtonDescriptor()
    desc.label = TCControlLabel(name: "escape_button", role: .button)
    // Set up other properties ...
    touchController.addButton(descriptor: desc)
}

func showEscapeButton() {
    // Find escape button in touchController ...
    escapeButton.isEnabled = true
}

func hideEscapeButton() {
    // Find escape button in touchController ...
    escapeButton.isEnabled = false
}
```

## Aim-to-release power on a single button (hold + drag)

```swift
// Use button B to aim, move, and release power
buttonB?.valueChangedHandler = { (_ button: GCControllerButtonInput, _ value: Float,
                                  _ pressed: Bool) -> Void in
    self.releasePower(pressed: pressed)
}

override func touchesMoved(_ touches: Set<UITouch>, with event: UIEvent?) {
    for touch in touches {
        let point = touch.location(in: metalView)
        // Handle touch input ...
        if let gc = gameController, gc.isAiming {
            let prev = touch.previousLocation(in: metalView)
            gc.aimTouchDelta += simd_float2(Float(point.x - prev.x), Float(point.y - prev.y))
        }
    }
}
```

## Custom feedback: layer a halo over the thumbstick during sprint

```swift
// Add a halo effect around left thumbstick with customized TCControlContents
let haloLayer = TCControlImage(texture: haloTexture, size: haloSize, highlight: nil,
                               offset: .zero, tintColor: tint)
let normalBgImages = TCControlContents.thumbstickStickBackgroundContents(size: bgSize,
                                                                         controller: controller).images
haloThumbstickBg = TCControlContents(images: [haloLayer] + normalBgImages)
thumbstick.backgroundContents = active ? haloThumbstickBg : normalThumbstickBg
```

---

## Useful API facts surfaced by the code

- A touch controller surfaces as a `GCController` — `valueChangedHandler` / `pressedChangedHandler` and polling all work as with a physical controller.
- Setup trio: `TCTouchController(descriptor:)` + `connect()` + `render(using:)`; forward UIKit `touchesBegan/Moved/Ended`.
- `TCTouchControllerDescriptor(mtkView:)`; gate creation on `TCTouchController.isSupported`.
- Controls follow a descriptor → create → `add…(descriptor:)` pattern: `TCButtonDescriptor`, `TCThumbstickDescriptor`, `TCTouchpadDescriptor`; added via `addButton`, `addThumbstick`, `addTouchpad`; removed via `removeControl`.
- `TCControlLabel` maps to physical inputs (`.buttonB`, `.rightThumbstick`) or custom (`TCControlLabel(name:role:)` with `.button`).
- Layout: `anchor` (e.g. `.bottomRight`) + `offset`; safe areas applied by adding `safeAreaInsets` to the offset.
- Visibility: `hidesWhenNotPressed` (thumbsticks), `isEnabled` (buttons), or add/remove for repositioned controls.
- `colliderShape`: `.leftSide`, `.rightSide`, `.circle`.
- `TCTouchpad`: `reportsRelativeValues = true` for position-independent camera input.
- Contents via `TCControlContents` (layered `TCControlImage` array); `.buttonContents(forSystemImageNamed:size:shape:controller:)`; `thumbstickStickBackgroundContents(size:controller:)`.
