# Session 304 — Implement high resolution photo capture

- **URL:** https://developer.apple.com/videos/play/wwdc2026/304/
- **Duration:** 18m
- **Speakers:** Mohit Setia (engineer, Camera Software team)

## Description

How to capture high-resolution photos (12, 24, 48 MP) on iPhone with AVFoundation
while managing the tradeoff between processing time and final image quality. Covers
the resolution options across iPhone cameras and the photonic engine, the four types
of high-resolution captures you can request, how to configure the capture session and
photo output, and the responsiveness APIs — overlapping captures, deferred photo
processing, and fast capture prioritization — that keep a camera app from blocking
between shots.

## Key topics

- Resolution tiers: 12 MP (default high-res), 24 MP, 48 MP; quad-pixel 48 MP sensor since iPhone 14 Pro / 14 Pro Max; 24 MP since iPhone 15; 24/48 MP extended to telephoto on iPhone 16 Pro and ultra wide on iPhone 17
- Photonic engine: fuses a 12 MP multi-frame HDR image with a full-resolution 48 MP frame to produce a 24 MP image (~50% larger file than 12 MP)
- Four capture types you can request: fully processed photo, exposure brackets, Bayer RAW, Apple ProRAW
- Session setup: only the `.photo` preset supports 24/48 MP; `maxPhotoQualityPrioritization`; `supportedMaxPhotoDimensions` / `maxPhotoDimensions`; configure photo output before `commitConfiguration`
- Quality prioritization: speed / balanced / quality — resolution availability depends on the level (12 MP at all three; 48 MP at balanced or quality; 18/24 MP multi-frame fused at quality only)
- `maxPhotoDimensions` is a request, not a guarantee; resolved values arrive in `AVCaptureResolvedSettings`
- Preallocate resources with `setPreparedPhotoSettingsArray`; the prepare settings object cannot be reused for the actual capture
- Responsiveness: `photoProcessingTimeRange`, shot-to-shot delay, responsive capture + `captureReadiness` (overlapping captures), deferred photo processing (`didFinishCapturingDeferredPhotoProxy`), fast capture prioritization
- 18 MP is only available on the Center Stage front camera on iPhone 17

## Related sessions to fetch (referenced in this talk)

- [ ] Capture and process ProRAW images (WWDC 2021)
- [ ] Capture High-Quality Photos Using Video Formats (WWDC 2021)
- [ ] Support Center Stage front camera in your iOS app (WWDC 2026)
- [ ] Create a More Responsive Camera Experience (WWDC 2023)
- [ ] Build a responsive camera app that launches quickly (WWDC 2026)

## Chapter summary (Summary tab)

- **0:00 Introduction** — The tradeoffs high-resolution capture requires, particularly between processing time and image quality; agenda: photo types, configuring/capturing, keeping the app responsive.
- **0:52 High-resolution photos** — The resolutions available across iPhone cameras (12, 24, 48 MP) and how the photonic engine balances light and detail; quad-pixel sensor since iPhone 14 Pro.
- **4:07 Types of captures** — The four high-resolution captures you can request: fully processed photos, exposure brackets, Bayer RAW, and Apple ProRAW.
- **5:20 Configure a capture session** — `AVCaptureSession` setup: select quality prioritization, configure maximum photo dimensions, and preallocate resources; finish photo output config before committing.
- **9:41 Responsive capture best practices** — Minimize shot-to-shot delay with overlapping captures, deferred photo processing, and fast capture prioritization.

## Code

See `code.md` — 5 snippets extracted from the Code tab.
