---
title: "Discover USDKit and what's new in OpenUSD — Full Digest"
session: WWDC26 · 285
url: https://developer.apple.com/videos/play/wwdc2026/285/
duration: 14m
speakers: Lee (Apple Spatial Standards team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 285 — Discover USDKit and what's new in OpenUSD

## TL;DR

USD is the substrate of everything spatial on Apple platforms, and this year Apple
both deepened the open standard and shipped a first-party Swift framework to author it.
Five headline themes:

1. **OpenUSD as a real standard** — Apple updated OpenUSD, MaterialX, and OpenVDB (new this year) across platforms; it sits in the Academy Software Foundation and is a founding member of the Alliance for OpenUSD; the **first formal USD core specification** is out, with geometry/materials/physics specs underway.
2. **Particle Fields** — a brand-new USD primitive type (co-developed with NVIDIA, Adobe, Pixar) that represents **Gaussian Splats** natively in USD, compositing them alongside meshes and materials in one scene for the first time.
3. **USDKit** — a new Swift **system framework** for authoring USD, with RealityKit and Spatial Preview integration baked in. `USDStage`, prims, references/composition, transform ops, schema application, and a compressing `exportPackage`.
4. **New experiences on top of USDKit** — Preview gains real **3D editing** with three renderers (RealityKit, Storm, new Raytracer), all OpenPBR; **Spatial Preview** gives a live Mac↔Vision Pro link with SharePlay; Safari's **`<Model>` tag** brings USD to the web with spatial breakout on visionOS.
5. **Accessibility + compression** — standardized **accessibility metadata** in USD (`AccessibilityAPI` schema, supported in Blender/Maya), and mesh + texture compression yielding **7× smaller assets** on average.

The running demo is the **ALab** scene (Pixar's reference asset lab): open the stage,
discover a missing oscilloscope, reference it in, position it, label it for
accessibility, and export a compressed `.usdz`.

---

## 1. Foundational OpenUSD updates

USD has become the common 3D language across films, AAA games, factory floors,
surgical suites, autonomous vehicles, and AI-driven simulation. The open-source project
behind it is **OpenUSD**, originally from Pixar. It doesn't work alone:

- **MaterialX** (originally Lucasfilm) — rich material descriptions.
- **OpenVDB** (originally DreamWorks, **new this year**) — volumetric data.

All three were updated across Apple's platforms this year. Apple's standards posture:

- Member of the **Academy Software Foundation** (home of MaterialX and OpenVDB), actively contributing upstream.
- Founding member of the **Alliance for OpenUSD**, pushing USD to be a standard "not just in practice, but on paper."
- Helped release the **first formal specification for the core of USD**, with **geometry, materials, and physics** domain specs already underway. These working groups are framed as where the future of USD gets decided — an explicit call to get involved.

## 2. Gaussian Splats → Particle Fields

Gaussian Splats capture a scene as millions of fuzzy, overlapping particles, each
encoding **position, color, and opacity**, to reconstruct real-world environments with
very subtle lighting response — without traditional geometry.

Working with **NVIDIA, Adobe, and Pixar** through the Alliance for OpenUSD, Apple is
introducing a new USD primitive type, **Particle Fields**, that can describe Gaussian
Splats as well as other representations from this fast-moving research area. The key
claim: this brings splats into the **same scene** as meshes, materials, and other
traditional 3D data **for the first time**.

## 3. 3D editing in Preview + new renderers

Preview has always been the out-of-the-box editor for images and PDFs on Mac; this year
that philosophy extends to 3D. Preview now offers essential 3D editing:

- Manipulate objects directly in the scene.
- Edit properties and lighting.
- Work with full scene hierarchies.
- Convert and compress assets.

…all without a dedicated 3D app, backed by a production-quality pipeline. Preview and
Quick Look on Mac give a **choice of three renderers**:

- **RealityKit** — consistency across Mac, iPhone, iPad, and Vision Pro.
- **Storm** — for existing production-pipeline needs.
- **Raytracer** (new) — high-fidelity ground-truth rendering on every Mac: accurate reflections, precise shadows, physically correct lighting; aimed at architectural visualization and product imagery.

All three support **OpenPBR**, described as a significant upgrade over
`USDPreviewSurface` for richer, more physically accurate materials.

## 4. Spatial Preview — live Mac ↔ Vision Pro

The new **Spatial Preview framework on macOS 27** creates a direct connection between
Preview on Mac and **Quick Look on Vision Pro**. As you edit a USD scene in Preview,
changes appear **live** in Quick Look on the Vision Pro, in your own space. Via
**SharePlay**, a whole team can join — creative directors and artists walking around the
same scene, reviewing lighting, composition, and spatial scale together in real time.

The same collaborative spatial workflow is available to embed in your **own Mac apps**.
→ See the **Spatial Preview** session.

## 5. USD on the web — Safari `<Model>` tag

Safari introduces a **`<Model>` tag** that brings 3D USD content to web pages as
naturally as images or video:

- On **macOS and iOS**: a fully interactive 3D experience right in the browser.
- On **visionOS**: the model **breaks out of the page** and is presented spatially in the user's space.

→ See the **USD on the web** session.

## 6. USDKit — concepts and the Swift walkthrough

USDKit brings first-class USD support to Swift apps, with deep RealityKit and Spatial
Preview integration. It's pitched at both USD veterans (familiar concepts) and
Swift-first developers new to 3D (familiar patterns).

**Key USD concepts** (as framed in the talk):

- **Layer** — a single data file.
- **Composition** — combining layers together.
- **Stage** — the composed result of one or more layers; your window into the full scene.
- **Prim** — everything in a scene is a USD prim.
- **Schema** — defines a prim's type.
- **Attributes** — hold the actual data.
- **Metadata** — describes information about the prim itself.

### Open or create a stage

```swift
import USDKit

// Create a new empty in-memory stage
let stage = USDStage()

// Open a stage from a file on disk (throws — file access)
let url = URL(fileURLWithPath: "/ALab/entry.usda")
let stage = try USDStage.open(url)
```

### Traverse, then define + reference the missing asset

The demo opens the ALab scene, notices the oscilloscope is missing, searches for it,
and — finding nothing — defines a new `Xform` prim and **references** the asset's own
file rather than copying its data in. That's composition: everyone authors their own
piece, USD assembles it, and upstream edits to the referenced file automatically flow
into your stage.

```swift
for prim in stage.descendants {
    if prim.name == "scope" {
        // There it is! 🔬
    }
}

let scope = stage.definePrim(at: "/World/scope", type: "Xform")
try scope.references.add("/ALab/assets/scope.usda")
```

### Position it with a transform operation

`addTransformOperation` creates the right attributes and maintains `xformOpOrder`
automatically; then you just set the translation.

```swift
scope.addTransformOperation(type: .translate)
scope["xformOp:translate", as: USDValue.Vec3d.self] = [2.5, 0.0, -1.0]
```

## 7. Accessibility metadata in USD

Apple has driven **standardization of accessibility metadata directly in USD** —
defining how assistive **labels** and **descriptions** are authored on 3D objects across
the industry, with flexibility to evolve. Because it's native to USD, it's authorable
through any USD API, and there's direct support in **Blender** and **Maya**.

In USDKit you apply the `AccessibilityAPI` schema, then — since USDKit doesn't expose
every schema-specific convenience — create the spec-named attributes directly and set
them:

```swift
try scope.applyAPISchema("AccessibilityAPI", instanceName: "default")

scope.makeAttribute(named: "accessibility:default:label", as: .string)
scope.makeAttribute(named: "accessibility:default:description", as: .string)

scope["accessibility:default:label", as: String.self] = "Oscilloscope"
scope["accessibility:default:description", as: String.self] =
    "Vintage signal analyzer with a 3D wireframe display, topped by a color bar test monitor"
```

Note this is a **multi-apply** schema (hence `instanceName: "default"`), and the
attribute names (`accessibility:<instance>:label` / `:description`) come straight from
the specification.

## 8. Asset compression — 7× smaller

Production USD scenes (the ALab scene included) can run to many gigabytes. Two codecs
combine:

- **Mesh compression** — a state-of-the-art codec developed with the **Alliance for Open Media**, up to **90%** mesh size reduction.
- **Texture compression** — existing **AVIF** texture compression.

Together: the average asset is **7× smaller** with no visual-quality compromise — faster
delivery, lower storage cost. It's built into `exportPackage`:

```swift
let output = URL(fileURLWithPath: "/ALab/alab_compressed.usdz")

try stage.exportPackage(
    to: output,
    options: [
        .preferSmallTextureFiles(quality: .standard),   // textures (AVIF)
        .preferSmallMeshFiles                            // mesh geometry
    ]
)
```

Same result is available without code in **Preview** or via the **`usdcrush`** command
line tool. Apple is working with Pixar to upstream this compression into **OpenUSD** so
the whole ecosystem benefits.

## 9. Three integration paths

USD integration can be complex, so there are three on-ramps, all on the same foundation
with files that move freely between them:

- **USDKit** — for app developers on Apple platforms. System-provided, deeply integrated; everything in this session is built on it. The recommended starting point.
- **SwiftUSD** — open-source Swift bindings via **Swift Package Manager**, for advanced needs or cross-platform Swift workflows beyond USDKit.
- **OpenUSD** — embed the C++ framework directly for cross-platform C++ codebases; made easier than ever this year.
