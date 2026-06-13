# Session 8004 — visionOS Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8004/
- **Duration:** 01:03:19 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + engineering panel answering upvoted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`, VAD + `-mc 0`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization.

## Panel

- **Adarsh Pavani** — host; technology evangelist for Apple Vision Pro (Worldwide Developer Relations).
- **Katie** — developer relations/evangelism; helps developers build for Apple Vision Pro & visionOS.
- **Norman** — rendering, simulation, gaming: RealityKit, Reality Composer Pro, USDKit, foveated streaming.
- **John** — RealityKit engineer; worked on the new USDKit framework.
- **Matt** — Vision Products Group; professional/enterprise apps, gaming, third-party interactive.
- **Travis** — Vision Products Group; software standardization strategy (USD/OpenUSD); media & streaming background.

## Description

A live, on-demand visionOS group lab. After a round of "favorite visionOS 27 features," the panel answers ~20 upvoted developer questions spanning camera access, on-device debugging, spatial accessories & object tracking, Gaussian splats, USD/OpenUSD export, foveated streaming, device sharing, visual intelligence, agentic coding, automated testing, and accessibility. The throughline: **file feedback with concrete use cases** — repeated so often it becomes the lab's refrain — because most of these are first-version (beta) features whose roadmap is shaped by developer input.

## Key topics

- **Front-facing / main camera access** — not enterprise-only; standard Developer Program (business-attached) can apply.
- **On-device debugging** — Mac Virtual Display inside an immersive space (the sanctioned "frozen universe" workaround); no caffeination by design (no lock button → doffing locks the device).
- **Spatial accessories** — spec opened in visionOS 27; DF Robot / Micro E append-on accessories & DIY boards; Bluetooth + onboard IMU + IR-emitter constellation; new IR-feed debugging mode.
- **Object tracking** — ~5 fps (visionOS 2.0) → 30 fps high-frame-rate mode in visionOS 27; frame-time-aligned timestamps; trained models now also run on iOS 27; CreateML extended training mode.
- **Gaussian splats** — close-range vignette/cull is intentional (FOV safety, applies to all 3D models); OpenUSD "particle fields" vs RealityKit splats not yet wired together.
- **USD export** — start from your 3D DCC, not an intermediate FBX/GLB; USDA vs USDC for iteration; USD core spec + LLMs.
- **Foveated streaming** — started visionOS 26.4 (NVIDIA CloudXR), expanded in 27; privacy-preserving gaze-region optimization; two-way (can send data back); blends with native RealityKit content.
- **Device sharing** — Vision Pro is a personal device (iPhone/iPad model, not Mac profiles); enrollment saved to iPhone (iOS 26+) + guest mode via app-clip-code glance.
- **Visual intelligence** — system-level, privacy-preserving, multi-sensor blend; no third-party camera access required.
- **Agentic coding** — trust-but-verify LLM output; sample projects as context; models lag current-year APIs.
- **Accessibility** — magnifying glass; wheelchair feature (Global Accessibility Awareness Day); "don't speak to future plans," file feedback.

## Related sessions (referenced on-air — all already in workspace)

- [x] 283 — Explore enhancements to visionOS object tracking (covers accessory tracking too)
- [x] 287 — Build next-generation experiences with visionOS 27 (Norman's; Unity/Unreal/Godot/stream pathways)
- [x] 284 — Collaborate on structured 3D models in visionOS (model manipulator sample; cross-sectioning)
- [x] 282 — Discover the Spatial Preview framework
- [x] 285 — Discover USDKit and what's new in OpenUSD
- [x] 279 — Explore advances in RealityKit
- [x] 286 — Use foveated streaming to bring immersive content to visionOS
- [x] 267 — Migrate to Swift Testing
- [x] 260 — Get the most out of Device Hub

## Chapter summary

No published chapters (group lab). Rough arc: intros (00:00) → favorite visionOS 27 features (02:53) → developer Q&A (08:58) → wrap-up (01:01:54).

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API/feature names are captured in `digest.md`.
