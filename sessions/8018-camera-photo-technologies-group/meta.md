# Session 8018 — Camera and Photo Technologies Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8018/
- **Duration:** 00:59:31 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + engineering panel answering submitted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization.

## Panel

- **Sergey** — host; Developer Relations team at Apple. Moderates and reads the submitted questions.
- **Matt Decoff** — Photos Frameworks team (PhotoKit / Photos UI / PhotosPicker).
- **Brad Ford** — Camera Software; 25 years at Apple, has worked on every iPhone. AVFoundation capture, AV sync, sessions/graph configuration.
- **Ivan Cabero-Belabonde** (transcript spells "Cabero-Belabonde"; surname uncertain) — Camera Software; specializes in the still-capture pipeline and depth capture. Introduced opportunistic depth capture ("portrait in photo").
- **Davide Conchon** (surname uncertain in transcript) — file format, compression, and RAW; 19 years at Apple. ProRAW, gain maps, Core Image / Core Graphics image handling.
- **Jake** — Camera performance; 5 years at Apple. Deferred start, responsive capture, AVProVideoStorage, fast-launch.
- Plus an unnamed behind-the-scenes team triaging questions.

## Description

A 59-minute live Camera and Photos group lab. After a warm-up round of favorite camera/photos features, the panel answers ~20 submitted developer questions spanning capture performance, depth, ProRAW vs Bayer RAW, AV sync, PhotoKit metadata and keywords, AI-edit provenance, the Siri camera, rotation handling, GenLock/time code/multi-cam, and the new AVProVideoStorage and RAW 9 features. The throughline: lean on the sample code, use the right framework for the job (Core Image for pan/zoom, AVCaptureSession for sync), and file Feedback Assistant requests for the gaps — repeated with "we actually do read them."

## Key topics

- **AI-edit provenance** — Clean Up / Spatial Reframe edits tag IPTC + EXIF metadata; the Photos info panel surfaces which edit was used.
- **Photos keywords** — visible/editable in the iOS Photos info panel and exported to IPTC, but no PhotoKit API to fetch/query by keyword.
- **Thumbnail / pan-zoom performance** — Core Graphics "open image with thumbnails", Core Image scale-factor at decode, region-of-interest caching; CIRAWFilter has the same behavior for 100MP RAW.
- **Depth capture** — preview via cinematic video capture (free) or DepthDataOutput + synchronizer; still via `depthDataDeliveryEnabled`; 24MP + depth on front camera works (requires deferred processing + max photo dimensions).
- **Photo quality prioritization** — `.balanced` and `.quality` override manual exposure/ISO (fusion); only `.speed` preserves manual settings in photo mode.
- **Deferred start vs prepared-settings array** — orthogonal; deferred start moves init after preview, prepared array pre-allocates worst-case still pipeline.
- **ProRAW vs Bayer RAW** — ProRAW is debayered/linearized RGB (sensor-agnostic); native Bayer RAW from a quad-Bayer sensor is binned; quad-Bayer RAW not yet offered (ecosystem decode problem).
- **AV sync** — AVCaptureSession syncs audio+video from the same clock; otherwise use CMClock/`CMSyncConvertTime`; sync video to the audio clock (audio glitches are more perceptible).
- **PHAsset originalResourceChoice** — RAW+JPEG: which resource is "the original" for edits/derivatives; new `rating` property (unset, 1–5) via change request.
- **Common camera-app mistakes** — never run AVCaptureSession on the main thread (dedicated serial queue); wrap multi-step reconfig in begin/commitConfiguration; don't render preview through video data output.
- **Rotation** — `AVCaptureDeviceRotationCoordinator`; set `videoRotationAngle` on the connection; iPhone 17's reoriented front camera.
- **Siri camera** — unlimited use, saves to Siri app not the photo library, screen-resolution quality only.
- **Multi-cam / pro video** — locked frame duration, GenLock (Blackmagic ProDoc), time-code generation, AVProVideoStorage (pre-allocated file, deterministic writes, retrofit to older phones).
- **RAW 9** — new ML-based RAW engine in the 27 OS (debayering, third-party RAW) via Core Image.

## Related sessions (referenced on-air, by spoken title — numbers not stated in the lab)

- [ ] "Build a responsive camera app that launches quickly" (Jake's session; deferred start, responsive capture, the dominoes demo)
- [ ] "Implement high-resolution photo capture" / "capturing high-resolution images" (photo quality prioritization; 24MP capture)
- [ ] "Support the Center Stage front camera in your iOS app" (Tracy's session; iPhone 17 front-camera rotation)
- [ ] "Enhanced RAW image processing" (David Hayward's session; Core Image, RAW 8 vs RAW 9)
- [ ] A gain-map session "two years ago" (ISO gain maps for HEIF/JPEG via Core Graphics and Core Image)
- [ ] An older PhotosPicker session "a few years ago" (sample code for UTType when transferring picked assets)
- [ ] A 2023 responsive-capture session + a recent deferred-processing session (Mohan)

Note: session numbers were not announced on-air. Titles above are as spoken (transcript-approximate); resolve to numbers separately if needed.

## Chapter summary

No published chapters (group lab). Rough arc:
- Intros (00:00–00:01:40)
- Favorite camera/photos features (00:01:40–00:05:26)
- Developer Q&A (00:05:26–00:58:55)
- Wrap-up / survey plug (00:58:55–00:59:31)

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API/feature names are captured in `digest.md`.
