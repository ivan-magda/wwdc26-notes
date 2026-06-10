# Session 220 — Refine accessibility for custom controls

- **URL:** https://developer.apple.com/videos/play/wwdc2026/220/
- **Duration:** 16m
- **Speakers:** Khin (software engineer, Accessibility team)

## Description

Custom UI controls let people do unique, creative things in your app with gestures
and interactions that go beyond standard controls — but those gestures often only make
sense visually. This session shows how to make any control in your app accessible to
people using VoiceOver, Switch Control, Voice Control, and other assistive
technologies, by translating implicit visual cues into explicit labels, values,
traits, and actions. It starts with a small set of guiding principles, then applies
them to progressively more complex controls: a custom one-axis coffee slider, a
two-dimensional equalizer pad, and a freeform interactive surface (a virtual cat).

## Key topics

- The four guiding principles: **purpose** (label), **value**, **actions**, and **feedback** — derived from the implicit cues a sighted user gets from a control's visual form
- Basic custom control: `accessibilityElement()`, `accessibilityLabel`, `accessibilityValue`, the `.adjustable` trait, and `accessibilityAdjustableAction` (with `.increment` / `.decrement`)
- Fine-grained input via the **passthrough gesture** (double-tap-and-hold), positioned with `accessibilityActivationPoint`
- Throttled feedback during passthrough using `AccessibilityNotification.Announcement` (only announce on real change + after ≥0.3s)
- **Custom actions** (`accessibilityActions`) for multi-axis controls like a 2D equalizer pad where a single adjustable axis isn't enough
- **Direct Touch API** (`accessibilityDirectTouch` / `allowsDirectInteraction` trait) for freeform gesture surfaces, with options `.requiresActivation` and `.silentOnTouch`
- Always pair direct touch with another path (e.g. custom actions) since not everyone can perform direct-touch gestures

## Related sessions to fetch

- [ ] _(none named explicitly in this talk)_

## Chapter summary (Summary tab)

- **0:01 Introduction** — Why custom controls need accessibility support so everyone can use what your app was built to do; the session covers guiding principles and how to apply them to complex controls.
- **1:02 Guiding principles** — Translate implicit visual cues into explicit information for assistive technologies. Apply labels, values, traits, and actions so controls are universally understood and actionable. Worked through a SwiftUI slider (how VoiceOver describes it) and then a custom coffee-dispenser control (label, value, `.adjustable`, adjustable action, activation point, throttled announcements).
- **8:41 Complex controls** — Controls like multi-dimensional pads and highly interactive virtual surfaces need techniques beyond labels and values: custom actions for the 2D equalizer pad (move up/right/down/left), passthrough gestures, and the Direct Touch API for the interactive virtual-cat surface.

See `code.md` for the five extracted snippets.
