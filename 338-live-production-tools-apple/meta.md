# Session 338 — Build live production tools for Apple Immersive Video

- **URL:** https://developer.apple.com/videos/play/wwdc2026/338/
- **Duration:** 16m
- **Speakers:** Jared King (lead, Apple Immersive Video Live Engineering)

## Description

A foundational tour of live broadcast production for those new to the space, and a
breakdown of what changes when you build for Apple Immersive Video. Apple earlier
this year transported fans courtside to select LA Lakers games — live in Apple Vision
Pro via the Spectrum SportsNet and NBA apps — powered by an Apple-built live broadcast
platform. The talk reviews the components of a modern live production pipeline, then
covers the three pillars that make immersive live possible: a combined media format
(streamed ProRes + ASAF PCM + per-frame JSON), real-time transport over SMPTE 2110,
and lossless record/playback to MOV via AVAssetWriter and the Immersive Media Support
(IMS) framework.

## Key topics

- Live production pipeline anatomy: production domain vs delivery domain; cameras, graphics, replay systems, video switchers, microphones, audio consoles, and a centralized media router
- Why immersive is different: ~32× the 2D video resolution (to match human visual acuity), 2× frame rate, ASAF mixes of 64+ channels
- The combined immersive live media format — three standards in one: streamed **ProRes** video, **ASAF** uncompressed PCM (high-order ambisonics + audio objects), per-frame **JSON** metadata (lens calibrations, creative events, motion/spatial-audio behavior)
- Three device classes: media output, media ingest, and both (e.g. a video switcher)
- **SMPTE 2110** transport over IP via multicast RTP: video as **2110-22** (compressed; both eyes as two data essences in one stream — no frame packing, no per-eye streams), audio as **2110-30**, metadata as **2110-41**
- Lossless record/playback: because everything is already ProRes, frames are copied straight into **MOV** files (no re-encode), avoiding generational loss
- `kVTProjectionKind_AppleImmersiveVideo` — new VideoToolbox property that writes the correct **vexu** static metadata into the MOV
- Audio written to MOV audio tracks; JSON written to **MEBX** metadata tracks via AVAssetWriter; JSON deserialized/parsed and turned into IMS lens-calibration / camera-ID objects, synchronized to video and audio
- **Immersive Media Support (IMS)** framework (introduced visionOS 26) — purpose-built for reading/writing AIV metadata and previewing content
- Frameworks for builders: AVFoundation, VideoToolbox, AudioToolbox, Immersive Media Support

## Related sessions to fetch

- [ ] Learn about Apple Immersive Video technologies
- [ ] Support immersive video playback in visionOS apps

## Chapter summary (Summary tab)

- **0:00 Introduction** — Apple Immersive Video live streaming transports fans to sports, music, and entertainment events on Apple Vision Pro — illustrated by courtside LA Lakers games delivered live through the Spectrum SportsNet and NBA apps.
- **2:08 Live production overview** — high-level overview of the fundamental components and creative tools of a modern live production pipeline: cameras, graphics, replay systems, video switchers, audio consoles, and a media router.
- **5:16 What makes immersive live different** — the unique scale and fidelity required for Apple Immersive Video: massive video resolutions, high frame rates, and rich ASAF mixes.
- **7:05 Immersive live format** — the core formats powering live immersive workflows: streaming ProRes, uncompressed PCM audio, and per-frame JSON metadata.
- **9:09 Real-time media transport** — how live immersive feeds move between devices in real time over IP using the SMPTE 2110 industry standard (2110-22 / -30 / -41).
- **11:25 Recording and playback** — recording live streams to disk and playing them back using AVAssetWriter and the Immersive Media Support framework, with no quality loss.

## Code

See `code.md` — 1 snippet from the Code tab (the `kVTProjectionKind_AppleImmersiveVideo`
compression property), plus the concrete APIs named in the transcript.
