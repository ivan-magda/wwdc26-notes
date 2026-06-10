---
title: "Refine accessibility for custom controls — Full Digest"
session: WWDC26 · 220
url: https://developer.apple.com/videos/play/wwdc2026/220/
duration: 16m
speakers: Khin (Accessibility team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 220 — Refine accessibility for custom controls

## TL;DR

Custom controls give people gestures and interactions that go beyond standard
controls — but those gestures usually only "read" if you can see the screen. This
session is about translating the implicit visual cues a sighted user gets into
explicit information for assistive technologies (VoiceOver, Switch Control, Voice
Control).

The whole talk hangs on **four guiding principles**, reverse-engineered from a plain
SwiftUI slider:

1. **Purpose** — what the control is (`accessibilityLabel`).
2. **Value** — its current state (`accessibilityValue`).
3. **Actions** — what someone can do and how (traits + adjustable / custom actions / direct touch).
4. **Feedback** — confirmation that something happened (announcements).

It then applies those principles to three controls of increasing complexity:

- **Coffee dispenser** (one-axis custom slider) → label, value, `.adjustable` trait, adjustable action, activation point, throttled announcements.
- **Equalizer pad** (2D pad from iOS Background Sounds) → **custom actions** because a single adjustable axis can't express two dimensions.
- **Virtual cat** (freeform interactive surface) → the **Direct Touch API** so the control's own gestures (pat / tap / pinch) pass straight through.

Core message at the close: turn on VoiceOver, open your app, and check purpose / value
/ actions / feedback for every custom control — and prefer custom actions wherever
possible so Switch Control and Voice Control users get the same interactions.

---

## 1. Guiding principles (the SwiftUI slider)

A standard SwiftUI slider is "understood at a glance": a track, a handle about
halfway along it, obviously grabbable and draggable. From the visuals you infer it's a
continuous value, what the current value is, the gesture to change it, and you get
immediate feedback (the track fills, the handle moves).

Take the visuals away and none of that is knowable. VoiceOver bridges the gap by
speaking the same information:

> "Brightness, 50%, adjustable." / "Swipe up or down with one finger to adjust the value."

That single utterance carries all four principles: **label** ("Brightness"),
**value** ("50%"), **action** ("adjustable" + the swipe hint), and **feedback** (the
new value announced in real time as it changes). Those four become the checklist for
every custom control in the talk.

## 2. Coffee dispenser — a basic custom control

A custom one-gesture control: drag up for more coffee, down for less; the fill level
is the ounces to brew. Out of the box VoiceOver only says "Settings. Button. 6 ounces.
Drag up or down on the cup." — no purpose, no clear way to change the value.

### Label, value, adjustable trait + action

```swift
struct CoffeeDispenserView: View {
    @State var coffee: Double = 0.0
    var body: some View {
        CoffeeSlider(value: coffee)
            .accessibilityElement()
            .accessibilityLabel("Coffee Dispenser")
            .accessibilityValue("\(Int(coffee)) ounces")
            .accessibilityAddTraits(.adjustable)
            .accessibilityAdjustableAction { direction in
                switch direction {
                case .increment: increaseCoffeeAmount()
                case .decrement: decreaseCoffeeAmount()
                }
            }
    }
}
```

- `accessibilityElement()` makes it a single element.
- `.adjustable` tells VoiceOver it can be adjusted with a swipe; `accessibilityAdjustableAction` defines what `.increment` / `.decrement` do.

Result: "Coffee dispenser, 6 ounces, adjustable. Swipe up or down with one finger to
adjust the value." — and swiping steps it one ounce at a time.

### Fine-grained input — the passthrough gesture

One-ounce steps aren't enough for someone who wants half an ounce. VoiceOver's
**passthrough gesture** (double-tap-and-hold, then move the finger) sends touch
events straight to the control for fine control. It starts at the control's
`accessibilityActivationPoint`, which defaults to center — set it to the current fill
level so the gesture always begins "at the coffee level":

```swift
CoffeeSlider(value: coffee)
    .accessibilityActivationPoint(UnitPoint(x: 0.5, y: 1 - coffee))
```

### Feedback — throttled announcements

During passthrough you want spoken updates, but not on every micro-change. Announce
only when the value actually changed **and** at least ~0.3s has passed since the last
one:

```swift
CoffeeSlider(value: coffee)
    .onChange(of: coffee) { _, newValue in
        if sufficientTimeSinceLastAnnouncement() && valueHasChanged() {
            cacheLastSpokenValue(newValue)
            AccessibilityNotification.Announcement(newValue).post()
        }
    }
```

Now the control is draggable with VoiceOver and gives meaningful, non-spammy updates —
delivering on all four principles (label, value, actions, announcements).

## 3. Equalizer pad — custom actions for two dimensions

The example is the equalizer in iOS **Background Sounds** (Accessibility Settings): a
2D pad whose center handle moves anywhere on the surface, adjusting **frequency**
(one axis) and **amplitude** (the other) at the same time.

`.adjustable` only gives increment/decrement on a **single** axis, so it can't express
both. The answer is **custom actions** — each has a VoiceOver-read label and a closure,
and they can do any operation you define:

```swift
EqualizerPad()
    .accessibilityActions("Move Up")    { increaseY(by: 10) }
    .accessibilityActions("Move Right") { increaseX(by: 10) }
    .accessibilityActions("Move Down")  { decreaseY(by: 10) }
    .accessibilityActions("Move Left")  { decreaseX(by: 10) }
```

Each action steps one axis by a fixed amount, clamped to range (the pad's axis bounds
are −100 to 100). VoiceOver: swipe up/down to select an action ("Move up", "Move
right"…), double-tap to perform it; the audio itself is the feedback. This lets someone
explore a 2D space using actions that are already familiar to assistive-tech users —
and it's how the platform's own control handles it.

## 4. Virtual cat — the Direct Touch API

A demo app: an interactive cat surface you can **pat** (purr), **tap** (meow), or
**pinch** (hiss). With no accessibility support VoiceOver just says "Cat fill. Space.
Image." — it has no idea those gestures exist.

Start with purpose + value:

```swift
struct VirtualCat: View {
    var cat: CatModel
    var body: some View {
        InteractiveCatSurface()
            .accessibilityLabel("Virtual Cat")
            .accessibilityValue(cat.currentReaction.description)
            .accessibilityDirectTouch([.requiresActivation])
    }
}
```

Passthrough isn't a great fit here — people may want to repeat a gesture or use
several different ones. **Direct Touch** marks a screen region (trait
`allowsDirectInteraction`) so touch events go straight to the control instead of being
processed by VoiceOver, letting people use **all** the gestures the control supports.

Two options:

- **`.requiresActivation`** — the control won't respond to direct touch until a
  double-tap, so someone can drag a finger across the screen without accidentally
  triggering it. Unlike passthrough, direct touch then **stays active until focus
  moves** to another element.
- **`.silentOnTouch`** — VoiceOver stays completely silent while the area is touched,
  for controls that produce their own audio (so VoiceOver doesn't talk over them).

Important caveat from the talk: **not everyone can perform direct-touch gestures**, so
wherever possible also expose another path — e.g. custom actions. That's the same
inclusivity point as the equalizer pad, and it's what lets Switch Control / Voice
Control users reach the same interactions.

## Wrap-up

The recommended workflow: turn on VoiceOver, open your app, and for every custom
control ask whether people can understand its **purpose, value, actions, and
feedback**. Reach for **direct touch** when the control relies on gestures that
passthrough doesn't support well, and **provide custom actions whenever possible** so
the broadest set of assistive technologies can use it.
