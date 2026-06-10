# Session 341 — Support the Center Stage front camera in your iOS app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/341/
- **Duration:** 18m
- **Speakers:** Tracy (engineer, Camera Software team)

## Description

The Center Stage front camera is available on iPhone 17, iPhone Air, and iPhone 17
Pro. Its square image sensor and 95-degree field of view give greater flexibility to
frame selfies, group shots, and video calls without rotating the device. This session
shows how to adopt the new AVFoundation APIs — dynamic aspect ratio, the smart framing
monitor (Auto Zoom / Auto Rotate), sensor orientation compensation, and Center Stage
for video recordings and video calls.

## Key topics

- **Hardware:** square image sensor + 95° FOV (widest on any iPhone front camera); shoot any aspect ratio (portrait/landscape) without rotating the phone; centered camera = more natural eye contact, secure one-handed grip.
- **Dynamic aspect ratio** (`AVCaptureDevice.dynamicAspectRatio`, iOS 26) — crops a chosen aspect ratio out of the square sensor without rebuilding the session or interrupting preview. Five ratios: 3x4, 4x3, 9x16, 16x9, 1x1. Square formats only, 1280–4032; the 4032 photo format supports only 3x4 / 4x3.
- **Smart framing monitor** (`AVCaptureSmartFramingMonitor`, iOS 26) — periodic framing recommendations (aspect ratio + zoom factor) from face/gaze detection; powers Auto Zoom + Auto Rotate. Photo-only: recommendations require the 4032 photo format.
- **Sensor orientation compensation** — the Center Stage sensor is mounted in Portrait (older front cameras were Landscape Left). `AVCapturePhotoOutput` compensates by default for HEIC/JPEG/uncompressed processed photos (never Bayer RAW / ProRAW); toggle via `cameraSensorOrientationCompensationEnabled`.
- **Video recordings** — dynamic aspect ratio works with `AVCaptureMovieFileOutput` (auto-stops recording on ratio change) or `AVCaptureVideoDataOutput` + `AVAssetWriter` (use the completion timestamp to split recordings). Face-aware cinematic stabilization: `cinematicExtended`, `cinematicExtendedEnhanced`.
- **Video calls** — Center Stage enabled per process; works automatically with VoIP background mode (Control Center toggle), or adopt the API directly with `centerStageControlMode` (`.cooperative` / `.app`) + `isCenterStageEnabled`. Low-latency stabilization via `preferredVideoStabilizationMode = .lowLatency` on the connection.

## Related sessions to fetch (referenced in this talk)

- [ ] Support external cameras in your iPadOS app (WWDC 2023) — `AVCaptureRotationCoordinator`
- [ ] What's New in camera capture (WWDC 2021) — original Center Stage API for iPad
- [ ] Implement high resolution photo capture (WWDC 2026) — 18-megapixel capture

## Chapter summary (Summary tab)

- **0:00 Introduction** — Center Stage front camera on iPhone 17 / iPhone Air / iPhone 17 Pro; square sensor + wide FOV for flexible framing.
- **1:07 Center Stage front camera** — hardware: square image sensor + 95° FOV; shoot any orientation without rotating the device; secure grip, natural eye contact.
- **2:09 Center Stage for photos** — Auto Zoom + Auto Rotate combine the wide FOV with face/gaze detection to adjust framing as people enter/leave.
- **3:09 Capture session setup** — `AVCaptureSession` with `.builtInUltraWideCamera`; add preview layer + `AVCapturePhotoOutput`.
- **3:56 Dynamic aspect ratio** — query `supportedDynamicAspectRatios`, `setDynamicAspectRatio`; switch ratios without interrupting preview. Tap-to-Rotate.
- **6:47 Smart framing monitor** — `AVCaptureSmartFramingMonitor`, `enabledFramings`/`supportedFramings`, KVO on `recommendedFraming`; apply ratio then zoom.
- **9:24 Sensor orientation compensation** — portrait-mounted sensor; `AVCapturePhotoOutput` auto-compensates; `cameraSensorOrientationCompensationEnabled`.
- **11:53 Center Stage for video recordings** — `AVCaptureMovieFileOutput` / `AVAssetWriter`; recording transitions on ratio change; cinematic stabilization.
- **13:16 Center Stage for video calls** — cooperative / app control mode; VoIP background mode; low-latency stabilization.

## Code

See `code.md` — 3 snippets extracted from the Code tab.
