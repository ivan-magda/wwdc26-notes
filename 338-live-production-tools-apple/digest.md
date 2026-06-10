---
title: "Build live production tools for Apple Immersive Video — Full Digest"
session: WWDC26 · 338
url: https://developer.apple.com/videos/play/wwdc2026/338/
duration: 16m
speakers: Jared King
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 338 — Build live production tools for Apple Immersive Video

## TL;DR

Apple has built — and is now opening up — a live broadcast pipeline for Apple
Immersive Video. Earlier this year it transported fans courtside to select LA Lakers
games, live in Apple Vision Pro, through the Spectrum SportsNet and NBA apps. This
session is Jared King's invitation to developers to build the production tools for
that ecosystem. Three pillars carry the whole talk:

1. **A combined media format.** Immersive live is three existing standards fused into
   one: streamed **ProRes** video, **ASAF** uncompressed PCM audio (high-order
   ambisonics + audio objects, 64+ channels), and per-frame **JSON** metadata (lens
   calibrations, creative events, motion/spatial-audio behavior).
2. **Real-time transport over SMPTE 2110.** Feeds move device-to-device over IP via
   multicast RTP: video as **2110-22**, audio as **2110-30**, metadata as **2110-41**.
   The video stream carries *both eyes* as two data essences in a single stream — no
   frame packing, no per-eye streams.
3. **Lossless record and playback.** Because everything is already ProRes, frames copy
   straight into **MOV** files with no re-encode — eliminating the generational loss
   that plagues traditional 2D replay workflows. AVAssetWriter writes the tracks; the
   **Immersive Media Support (IMS)** framework handles the metadata.

The scale is the headline: immersive video is roughly **32× the resolution** of a 2D
broadcast (to match human visual acuity) at **2× the frame rate**, with audio mixes
far beyond stereo or 5.1. Those numbers ripple through every stage, which is why a new
workflow — not the traditional toolchain — was required.

---

## 1. Live production pipeline — the foundation (2:08)

A live production pipeline captures and creatively produces video/audio/data at one
end (the **production domain**), then encodes and streams it to an audience (the
**delivery domain**). This holds whether the production is a TV studio / broadcast
truck managing many cameras and audio sources, or a small podcast studio or local
music venue using only a few of these elements. The talk focuses on the **production
domain**, which is where immersive diverges most.

Regardless of scale, the same creative tools recur — just scaled in quantity and
sophistication:

- **Live cameras** — capture the scene, often several for different angles (camera 1,
  2, 3).
- **Graphics** — generated and keyed onto the video: a lower-third name, a scoreboard,
  a complex animation.
- **Replay systems** — record media and replay it on cue; or archive for post.
- **Video switchers** — let operators cut between cameras, overlay graphics, and
  produce the final stream the viewer sees.
- **Microphones** — pick up announcers, interviews, musical and other sources.
- **Audio consoles** — ingest all sources and combine them into the final "mix."
- **Media router** — a centralized, unified network layer through which every tool
  exchanges signals with every other tool (camera feeds → switcher inputs, mics →
  console).

## 2. What makes immersive live different (5:16)

Fidelity and presence — preserved through every step — are everything when you put the
customer *inside* the content. The numbers:

- **Video resolution ≈ 32× larger** than typical 2D broadcast, to match human visual
  acuity.
- **2× the frame rate.**
- **ASAF mixes of 64 or more channels**, versus stereo or 5.1 surround.

Not all traditional tools, transport methods, and formats support media at this scale,
so immersive live required an entirely different workflow. The rest of the talk is the
three concepts that unlock it: a media **format**, a **transport** layer (SMPTE 2110),
and **record/playback** to file.

## 3. The immersive live media format (7:05)

Every production has three device classes:

- **Output** devices (camera, microphone, graphics generator)
- **Ingest** devices (video encoder, color-grading monitor)
- **Both** (a video switcher — ingests camera sources, outputs the switched program)

All devices must agree on a unified set of media formats — a common language — to
exchange content through the media router in real time. Three existing standards are
combined:

- **Video — streamed ProRes.** Apple Immersive Live Video is composed entirely of
  streamed ProRes frames (not the uncompressed frames typical of broadcast cameras).
  ProRes balances image quality against bandwidth, reducing signals to a tool-friendly
  size while keeping fidelity. Apple Silicon is optimized for ProRes processing, which
  makes it the ideal platform for these tools. (See the "Apple ProRes" docs.)
- **Audio — ASAF / PCM.** ASAF mixes are standard uncompressed PCM audio tracks
  carrying high-order ambisonic beds and spatial audio objects.
- **Metadata — per-frame JSON.** JSON objects describe attributes of the related video
  and audio feeds: lens calibrations, creative events, spatial-audio behavior, and
  more.

Together these three define the live immersive production format, and every tool must
be compliant with each media type for ecosystem interoperability.

## 4. Real-time transport over SMPTE 2110 (9:09)

Devices exchange feeds as individual **SMPTE 2110** media streams — the industry
standard for professional media transport over IP, widely deployed across broadcast
facilities and interoperable with a broad ecosystem of professional tools. 2110 uses
multicast **RTP** (Real-time Transport Protocol); RTP streams carry timing info, user
flags, and other metadata alongside the main payload. Each 2110 stream carries exactly
one of video, audio, or metadata, and each media type maps to a sub-standard:

- **Video → 2110-22** (compressed media over IP). The flow contains both the **left and
  right eye** as **two separate data essences within a single stream**. No need to
  frame-pack the eyes side-by-side into one raster, and no need for separate per-eye IP
  streams — which eliminates the complexity of managing independent left/right feeds in
  the production architecture.
- **Audio → 2110-30.** Carries the high-order ambisonics and audio-object channels that
  compose the ASAF mix.
- **Metadata → 2110-41** (user-defined metadata over IP). Carries the per-frame JSON —
  lens calibrations, creative events, motion data — in real time alongside the -22
  video and -30 audio.

## 5. Recording and playback — lossless by design (11:25)

Recording, editing, and playing feeds back out (e.g. Instant Replay) is core to any
live workflow. In traditional 2D, recording to file repeatedly encodes/decodes/
re-encodes content, introducing visual loss — and that **generational loss compounds**
over multiple cycles. In immersive, even small reductions meaningfully hurt the
customer experience.

The format solves this: **everything is already ProRes.** Live media is natively
generated in a file-friendly ProRes payload, so recording needs no extra encode/decode.
The same ProRes frames are copied directly into **MOV** files and read back out into
live 2110 streams during playout — **untouched.** A clip can be produced by a camera,
transported between devices, recorded, edited, and played back live on repeat with no
quality impact.

How the three media types are written to the MOV (all via AVFoundation's
**AVAssetWriter**):

- **Video** → QuickTime MOV video tracks. The MOV keeps the same resolution, frame
  rate, and stereo image data as the live stream. When writing the track, set the new
  VideoToolbox constant so the file is correctly tagged as immersive:

```swift
import VideoToolbox

let compressionProperties: [String: Any] = [
    // ...
    kVTCompressionPropertyKey_ProjectionKind as String: kVTProjectionKind_AppleImmersiveVideo
    // ...
]
```

  This adds the correct **video extended usage (vexu)** static metadata, signaling the
  file as Apple Immersive Video to other applications. (In the transcript the constant
  is described as being set in the `AVVideoCompressionPropertiesKey`.)

- **Audio** → MOV audio tracks. The uncompressed PCM carried in the 2110 stream is
  written directly, the usual way.

- **Metadata** → **MEBX** (Metadata Box Exchange) tracks in the MOV. The streamed JSON
  is first deserialized and parsed, then the **Immersive Media Support (IMS)** framework
  creates lens-calibration objects, camera IDs, and other metadata objects, written into
  the MOV synchronized with the video and audio.

### Immersive Media Support (IMS)

IMS was introduced in **visionOS 26**. It reads and writes the essential metadata for
Apple Immersive Video and supports previewing content in creative workflows. The video
and audio rely on well-known technologies (AVFoundation, VideoToolbox, Core Audio), but
IMS is purpose-built for AIV and is, per the speaker, "one of the most important
frameworks to understand" for building production tools. (See the "Immersive Media
Support" docs.)

### Playback reverses everything

On file playback all steps reverse: video, audio, and metadata are read from their MOV
tracks and retransmitted into 2110 **output** streams for the wider production — using
the same frameworks and libraries.

## 6. Getting started (15:08)

Build immersive tools with **AVFoundation, VideoToolbox, AudioToolbox, and Immersive
Media Support**. Every layer of the stack is open for innovation. Go deeper on 2110
(visit the **SMPTE** website for standards and network best practices) and connect
tools into a true live workflow.
