# Session 282 — Discover the Spatial Preview framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/282/
- **Duration:** 15m
- **Speakers:** Quincy German (software engineer, visionOS team)
- **Audio:** English.

## Description

The Spatial Preview framework lets a Mac app extend its content into visionOS over
Mac Virtual Display, with no code required on the visionOS side. Quincy German walks
through the framework's three building blocks — selecting a spatial endpoint, creating
a preview session, and launching Quick Look on Vision Pro — then shows the two session
types: `DocumentPreviewSession` (spatial photos/videos, PDFs, images, Apple Immersive
Video frames) and `USDPreviewSession` (live, editable 3D scenes via USDKit). The talk
covers updating documents in place, live cross-device USD editing, annotations, object
manipulation, playback events, sync-progress reporting, and built-in SharePlay
collaboration.

## Key topics

- **Spatial Preview framework** — new in macOS / visionOS 27; powers the new Preview app for Mac and is exposed as an API for any macOS app
- Three components: pick a `SpatialPreviewEndpoint`, create a preview session, Quick Look launches on Vision Pro automatically (no visionOS code)
- **Endpoint selection** — reuse the active Mac Virtual Display device via `ConnectedSpatialEndpointObserver`, or present `SpatialPreviewDevicePicker` to choose any nearby Vision Pro on the same iCloud account
- **`DocumentPreviewSession`** — send/update files (Apple Immersive Video frames `.aivu`, spatial photos, PDFs, standard images); `updateContents(url:)` reuses the same scene to build galleries; observe `session.state` for invalidation; `close()` to end
- **`USDPreviewSession`** — share a USDKit `USDStage`; volumetric then immersive view; built-in camera viewpoints + material overrides (e.g. wireframe) with no extra Mac-app code
- **Automatic optimization** — mesh decimation, texture downsampling, possible scene reconstruction so content performs on Vision Pro; opt out with `.unmodified` parameter (may throw `assetUnshareable`); reconstructed scenes are view/annotate-only, not editable
- **Live USD editing** — a live stage replicates edits both ways via regular USDKit APIs; Layout variant sets, `AppleTextAnnotation` (under a document annotation group), `spatialEditable` metadata for gesture-movable prims
- **Session events/options** — `options: [.annotations, .perObjectManipulation, .export]` (all on by default); `session.events` for playback time/state; `ProgressReporter` for sync-progress loading bars
- **SharePlay** collaboration is built in on visionOS — multiple participants edit/review the same live session

## Related sessions to fetch (referenced in this talk)

- [ ] Understand USD Fundamentals
- [ ] Discover USDKit
- [ ] What's new in OpenUSD

## Chapter summary (Summary tab)

- **0:00 Introduction** — Spatial Preview lets developers extend Mac content into visionOS via Mac Virtual Display; demoed in the new Preview app (3D editing, photorealistic rendering, camera viewpoints, spatial media output). Agenda: framework overview, document preview, USD preview for live 3D workflows. Cinema 4D and SketchUp cited as adopters.
- **2:37 Learn about Spatial Preview** — the three core components: select a spatial endpoint, create a preview session, Quick Look launches on visionOS. Two session types (Document, USD); no visionOS code required.
- **3:30 Document Preview** — `DocumentPreviewSession` to send files (Apple Immersive Video frames) from Mac to visionOS; endpoint via `ConnectedSpatialEndpointObserver`, start session, provide content URLs, `SpatialPreviewDevicePicker` in SwiftUI, `updateContents` to build a gallery that reuses one scene.
- **6:36 USD Preview** — share and live-edit USD via `USDPreviewSession` with a USDKit stage; select device, open stage, start; handle unshareable assets via the optimization parameter.
- **9:16 Editing Features** — Quick Look editing through USD Preview: layout variants, `AppleTextAnnotation` authoring, object manipulation via `spatialEditable` metadata, session options, playback events, and progress via `ProgressReporter`.
- **13:28 Next steps** — get started with USDKit Swift APIs and bridging for existing USD apps; explore Document/USD preview sessions, live editing, SharePlay collaboration, and the asset review tools.

## Code

See `code.md` — 11 snippets extracted from the Code tab.
