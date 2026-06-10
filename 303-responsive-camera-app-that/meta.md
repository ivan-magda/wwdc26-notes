# Session 303 — Build a responsive camera app that launches quickly

- **URL:** https://developer.apple.com/videos/play/wwdc2026/303/
- **Duration:** 25m
- **Speakers:** Jake (engineer, camera performance team)

## Description

How to make a camera app launch feel fast by getting the preview frame on screen as
quickly as possible, then keeping the experience smooth and reliable. Covers
accelerating launch (UI phasing, off-main-thread session creation, and the new
Deferred Start API), best practices for rendering a steady preview
(`AVCaptureVideoPreviewLayer` vs `AVCaptureVideoDataOutput`), assessing and adapting to
system pressure with hardware-cost and system-pressure APIs, and a new `AVProVideoStorage`
API for deterministic high-bandwidth file writing on ProRes-class captures.

## Key topics

- The single biggest factor in perceived launch speed is **how fast the first preview frame appears**.
- Four launch stages: app launch → session config/start → output initialization → preview streaming.
- **Phase the UI:** create only preview + shutter on launch; fade in image well, mode picker, etc. after.
- **Create `AVCaptureSession` off the main thread** (it blocks); `startRunning`/`stopRunning` are blocking too — never on main.
- Commit a **single** configuration up front; avoid repeated reconfiguration.
- **Deferred Start API** (iOS 26+): postpone initialization of non-preview outputs until after the first frame. `isDeferredStartEnabled` per output; automatic vs manual mode; `sessionWillRunDeferredStart` / `sessionDidRunDeferredStart` callbacks. Claimed ~2× faster launch in the lab.
- Deferring the photo output delays first capture → pair with `isResponsiveCaptureEnabled` so the moment isn't missed.
- **Preview rendering:** `AVCaptureVideoPreviewLayer` (simple, low-overhead, HDR tone mapping, low latency, auto deferred start) vs `AVCaptureVideoDataOutput` (per-frame access, custom overlays, Metal — but you must adopt manual deferred start).
- **Sustained performance:** `AVCaptureSession.hardwareCost` and the system-pressure cost (0–1; >1 unsustainable); observe `AVCaptureDevice.systemPressureState`; reduce frame rate (`frameRateOverride`), use binned formats, throttle GPU/ANE/UI.
- **`AVProVideoStorage`** (iOS 27): system-wide pre-allocated storage singleton for deterministic high-bandwidth I/O; opt in via `usesProVideoStorage` on `AVCaptureMovieFileOutput` (or `AVAssetWriter`).

## Related sessions to fetch (referenced in this talk)

- [ ] Implement High Resolution Photo Capture (WWDC26)
- [ ] Create a More Responsive Camera Experience (WWDC23)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Why a fast-appearing preview frame is the single biggest factor in a camera launch feeling responsive; the falling-dominoes demo (missing the red domino). Agenda: accelerating launch, rendering best practices, capturing the moment.
- **2:02 Fast Launch** — Four launch stages; minimize UI overhead (phase critical vs deferrable UI); create/configure `AVCaptureSession` off the main thread; commit a single configuration; keep `startRunning`/`stopRunning` off main.
- **6:52 Adopt deferred start** — Defer expensive non-preview outputs until after preview is running; `isDeferredStartEnabled`; automatic mode (default when recompiled for iOS 26+) vs manual mode (`runDeferredStartWhenNeeded`); delegate callbacks. Lab result: ~2× faster launch. Pair with `isResponsiveCaptureEnabled` so first capture isn't delayed.
- **15:06 Steady preview** — `AVCaptureVideoPreviewLayer` (simple, optimized, auto deferred start) vs `AVCaptureVideoDataOutput` (per-frame control, Metal, custom overlays; needs manual deferred start). Keep per-frame work short to avoid drops.
- **18:04 Sustained performance** — `hardwareCost` and system-pressure cost APIs (0–1, >1 unsustainable); contributors: camera count, active formats (1080p/4K), frame rate (`frameRateOverride`), binned formats; observe `systemPressureState` and adapt.
- **21:14 Deterministic file writing** — `AVProVideoStorage` (iOS 27): pre-allocated, system-wide storage for sustained high-bandwidth I/O (ProRes); `usesProVideoStorage` on `AVCaptureMovieFileOutput`/`AVAssetWriter`; `isSupported`, `shared`, `remainingCapacity`, `openSettings`, `isBusy`.

## Code

See `code.md` — 8 snippets extracted from the Code tab.
