# Session 285 — Discover USDKit and what's new in OpenUSD

- **URL:** https://developer.apple.com/videos/play/wwdc2026/285/
- **Duration:** 14m
- **Speakers:** Lee (engineer, Apple Spatial Standards team)
- **Audio:** English.

## Description

USD is the backbone of Apple's spatial experiences — the way scenes are represented
from the apps you build to the content shown across every platform. This session covers
the foundational OpenUSD updates Apple shipped this year (OpenUSD, MaterialX, OpenVDB,
a new Particle Fields primitive for Gaussian Splats, a formal USD core specification),
then introduces **USDKit**, a new first-party Swift system framework that brings USD
authoring to Apple apps with built-in RealityKit and Spatial Preview integration. Along
the way: 3D editing in Preview with three renderers, Spatial Preview's live Mac↔Vision
Pro link, the Safari `<Model>` tag, accessibility metadata in USD, and 7× asset
compression.

## Key topics

- **OpenUSD ecosystem updates** — OpenUSD, MaterialX (Lucasfilm), and OpenVDB (DreamWorks, new this year) all updated across platforms; Apple in the Academy Software Foundation and a founding member of the Alliance for OpenUSD; first formal USD core specification released (geometry/materials/physics domain specs underway).
- **Particle Fields** — new USD primitive type co-developed with NVIDIA, Adobe, Pixar; natively represents Gaussian Splats (and other research-stage representations) alongside meshes and materials in one scene for the first time.
- **USDKit** — new Swift system framework; `USDStage`, prims, references/composition, transform ops, schema application, `exportPackage`; deep RealityKit + Spatial Preview integration; approachable for USD veterans and Swift-first developers.
- **3D editing in Preview** — direct scene manipulation, property/lighting editing, hierarchy browsing, asset conversion/compression; choice of three renderers (RealityKit, Storm, new Raytracer); all support **OpenPBR** (upgrade over USDPreviewSurface).
- **Spatial Preview framework (macOS 27)** — live link between Preview on Mac and Quick Look on Vision Pro; SharePlay multi-person spatial review; embeddable in your own Mac apps.
- **Safari `<Model>` tag** — interactive 3D USD in web pages on macOS/iOS; spatial breakout on visionOS.
- **Accessibility metadata in USD** — standardized `AccessibilityAPI` schema with label/description attributes; native to USD, authorable via any USD API, supported in Blender and Maya.
- **Asset compression** — mesh compression codec (Alliance for Open Media) up to 90% smaller, plus existing AVIF texture compression → 7× smaller assets on average; via `exportPackage`, Preview UI, or the `usdcrush` CLI; being upstreamed to OpenUSD with Pixar.
- **Three integration paths** — USDKit (Apple app devs), SwiftUSD (SPM, open-source/advanced Swift), OpenUSD as an embeddable C++ framework (cross-platform). Files interoperate across all three.

## Related sessions to fetch (referenced in this talk)

- [ ] Spatial Preview (session)
- [ ] Bringing USD to the web / USD on the web (session)
- [ ] Building rich spatial experiences with RealityKit and Reality Composer Pro (session)

## Chapter summary (Summary tab)

- **0:07 Introduction** — Lee, Apple Spatial Standards team. USD is the backbone of Apple's spatial experiences. Agenda: foundational USD updates, the new USDKit framework, and how it bridges Mac and Vision Pro.
- **0:53 OpenUSD: Industry Foundation and New Standards** — USD as the common 3D language; updates to OpenUSD, MaterialX, OpenVDB across platforms; Apple in the Academy Software Foundation and Alliance for OpenUSD; first formal USD core specification.
- **2:51 Gaussian Splats and Particle Fields** — new Particle Fields primitive co-developed with NVIDIA/Adobe/Pixar; brings Gaussian Splats natively into USD scenes alongside traditional geometry.
- **3:47 Introducing USDKit** — high-level overview of the new first-party Swift framework with RealityKit and Spatial Preview integration; approachable for USD veterans and 3D newcomers alike.
- **4:06 3D Editing in Preview and New Renderers** — Preview gains scene manipulation, property/lighting editing, hierarchy browsing, asset conversion; three renderers (RealityKit, Storm, new high-fidelity Raytracer), all OpenPBR.
- **5:42 Spatial Preview: Live Collaboration Between Mac and Vision Pro** — Spatial Preview framework on macOS 27; live link between Preview and Quick Look on Vision Pro; SharePlay collaboration; integrable into your own apps.
- **6:25 USD on the Web: The Safari Model Tag** — Safari `<Model>` tag for interactive 3D USD on macOS/iOS; full spatial breakout on visionOS.
- **6:57 USDKit: Key Concepts and Swift API Walkthrough** — core USD concepts (Layers, Composition, Stages, Prims, Schemas, Attributes, Metadata); Swift example: open a stage, traverse, add a referenced asset, reposition with transform ops.
- **10:05 Accessibility Metadata in USD** — standardized accessibility metadata in USD; apply `AccessibilityAPI` schema, set label/description attributes; supported in Blender and Maya.
- **11:19 Asset Compression: Mesh and Texture** — mesh compression (up to 90%) with the Alliance for Open Media + AVIF textures → 7× smaller average; via `exportPackage`, Preview, and `usdcrush`.
- **12:36 Integration Paths: USDKit, SwiftUSD, and OpenUSD** — three paths sharing the same USD foundation; files interoperate freely.
- **13:24 Next steps** — recap (Preview editing, Spatial Preview, Safari Model tag, USDKit); pointers to Spatial Preview, USD on the web, and RealityKit/Reality Composer Pro sessions.

## Code

See `code.md` — 5 snippets extracted from the Code tab.
