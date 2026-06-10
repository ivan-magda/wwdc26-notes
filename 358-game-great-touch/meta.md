# Session 358 — Make your game great with touch

- **URL:** https://developer.apple.com/videos/play/wwdc2026/358/
- **Duration:** 24m
- **Speakers:** Keyi Yu (Game Technology team)

## Description

A step-by-step guide to designing and implementing great touch controls for games on
iOS and iPadOS using the new **Touch Controller framework**. The framework extends the
existing Game Controller framework so that on-screen touch controls show up as a
standard `GCController`, meaning existing game input logic needs minimal changes. The
session walks through four areas — setup, flexible layouts, fluid interactions, and
player feedback — using the demo game (a Dredge-inspired example) throughout.

## Key topics

- **Touch Controller framework** (`TCTouchController`) — extends Game Controller; a touch controller surfaces as a `GCController`, so polling and value-changed handlers work unchanged. Integrates directly with Metal for performance.
- **Setup** — create `TCTouchController` from a `TCTouchControllerDescriptor(mtkView:)`, `connect()` to enable, `render(using:)` in the Metal renderer, forward `touchesBegan/Moved/Ended` from the `UIView` to the controller.
- **Flexible layouts** — nine anchor points + offsets; group controls into sections that hold consistent size/distance as device shape changes; read UIKit `safeAreaInsets` and add them to offsets to avoid the Dynamic Island, home indicator, and rounded corners.
- **Layout strategy** — keep the center clear (character + movement/camera); frequent/important actions near the thumbs; menu/less-used controls at the top.
- **Fluid interactions** — contextual SF Symbol icons reflecting current state instead of generic button glyphs; hide unavailable controls (`hidesWhenNotPressed`, `isEnabled = false`, or add/remove); replace overlays with direct touch controls; full-half-screen `colliderShape` (`.leftSide`/`.rightSide`); sprint via thumbstick tilt magnitude (no second finger); `TCTouchpad` with `reportsRelativeValues` for camera.
- **Collapsing complex inputs** — single QTE button (replaces hold L1+R1); single aim-to-release power button (hold + drag captured in `touchesMoved`).
- **Rich feedback** — built-in pressed/animated states by default; custom `TCControlContents` layering (e.g. a glowing halo texture over the thumbstick background during sprint).

## Related sessions to fetch (referenced in this talk)

- [ ] Design great interfaces for handheld games
- [ ] Level up with Apple game technologies

## Chapter summary (Summary tab)

- **0:00 Introduction** — why touch controls matter for spontaneous mobile play; Dredge by Black Salt Games as the model; preview of the four areas (setup, layouts, interactions, feedback).
- **1:42 Set up a touch controller** — Touch Controller framework extends `GCController` support to touch; create `TCTouchController` from a descriptor, enable it, and it appears as a standard `GCController` so existing input code barely changes.
- **4:52 Design flexible layouts** — nine anchor points + section grouping; read `safeAreaInsets`; position frequent actions near thumbs, less-critical ones up top; never cover the play area.
- **10:17 Design fluid interactions** — make touch feel native, not a direct controller overlay: contextual icons, hide unavailable controls, replace overlays with direct touch, full-screen thumbstick colliders, sprint via tilt magnitude, `TCTouchpad` camera control.
- **21:16 Provide rich feedback** — built-in press states, custom visuals like the sprint halo, and collapsing multi-finger actions (QTEs, aim-to-release throws) into single intuitive controls.
- **23:49 Next steps** — design with the framework, test on multiple device sizes, iterate on player feedback; watch the two related game sessions.

## Code

See `code.md` — 14 snippets extracted from the Code tab.
