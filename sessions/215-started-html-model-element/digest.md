---
title: "Get started with the HTML Model Element — Full Digest"
session: WWDC26 · 215
url: https://developer.apple.com/videos/play/wwdc2026/215/
duration: 16m
speakers: Aleksei (Safari team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 215 — Get started with the HTML Model Element

## TL;DR

The HTML `<model>` element — a native tag that puts a 3D model on a web page as
simply as `<img>` puts a picture — graduates from visionOS-only to **iOS, iPadOS,
and macOS** in Safari. The same markup runs everywhere.

Headline themes:

1. **Native, not a library.** `<model>` is rendered by the platform — no
   `model-viewer` JS dependency — and is an **emerging W3C standard**, with a polyfill
   for browsers that don't support it natively yet.
2. **USDZ is the asset format.** One zipped file bundles geometry, materials,
   textures, and animations. Get assets via Capture / Convert / Create or generative
   AI (Tripo3D, Meshy.ai).
3. **Progressive feature set.** Start with a `src`; layer on `<img>` fallback, the
   `ready` promise, `background-color`, `stagemode="orbit"`, JS-driven
   `entityTransform`, and baked-in animation playback — each is a small additive step.
4. **Real-world and spatial.** `<a rel="ar">` gives AR Quick Look on iOS/iPadOS;
   visionOS adds stereoscopic depth and immersive website environments.
5. **Production tooling.** `usdcrush` shrinks files dramatically (demo 7.9 MB → 1.9 MB)
   and `usdrecord` renders thumbnails/fallbacks — both already on every Mac.

The running example is an outdoor-adventure e-commerce store adding 3D product views
(a camping mallet, a boot, a bottle).

---

## 1. What the Model element is

Pioneered on visionOS, `<model>` is a native HTML element that makes adding a 3D model
"as simple as adding an image." This year it expands to iOS, iPadOS, and macOS, with
the same markup working across all Apple platforms in Safari.

Versus the familiar **`model-viewer`** JavaScript library: `<model>` needs no extra
library, is rendered directly by the platform, gets built-in **stereoscopic rendering**
on visionOS, and, as an emerging web standard, tracks toward broad browser support. A
**polyfill** covers browsers that don't yet support it natively (covered below).

## 2. Preparing a USDZ asset

If you have no 3D assets, the recommended approach is **Capture, Convert, Create**:
scan real objects with iPhone, convert existing files, or author from scratch in tools
like Blender. Generative AI is a new fourth path — feed **images** to match a
real-world object, or a **text prompt** for creative generation (Tripo3D, Meshy.ai
are named as examples).

The element's recommended format is **USDZ** — Universal Scene Description, zipped into
a single file that packages geometry, materials, textures, and animations. Safari
supports other formats too, but USDZ gives the best experience.

## 3. Loading a model and handling fallbacks

Point `<model>` at a file with `src`, or use a nested `<source>` with a MIME type —
the same patterns as `<img>` / `<video>`. No plugins required.

```html
<!-- Using the src attribute -->
<model src="mallet.usdz"></model>

<!-- Using a <source> child for MIME type -->
<model>
    <source src="mallet.usdz" type="model/vnd.usdz+zip">
</model>
```

**Fallback:** put an `<img>` inside the `<model>`. Older Safari and non-supporting
browsers render the image instead, so visitors still see the product.

```html
<model id="mallet" src="mallet.usdz">
    <img src="mallet.png"
         alt="Rubber mallet with wooden handle">
</model>
```

**Loading state:** 3D models can be tens of megabytes, so loading takes time. The
`ready` **promise** resolves when the model is loaded and ready to display — use it to
hide a spinner; use `.catch` to show fallback content on failure.

```html
<model id="mallet" src="mallet.usdz"></model>

<script>
    const model = document.getElementById("mallet");
    model.ready.then(result => {
        // Hide the loading indicator
    }).catch(error => {
        // Loading failed, show fallback
    });
</script>
```

**Polyfill:** a polyfill retrofits the new standard's API via JavaScript so you can use
it before it lands everywhere. If the native element isn't on `window`, load the
polyfill library. Test both paths — and note some features (e.g. stereoscopic display
on Apple Vision Pro) **cannot** be polyfilled.

```html
<script type="module">
    if (!window.HTMLModelElement) {
        import("model-element-polyfill.js").then(() => {
            // Polyfill ready to use
        });
    }
</script>
```

## 4. Matching page design — background

The element renders in its **own virtual space** and does not inherit the page's
background. Set `background-color` directly on the element to match your design. The
background is always composited **opaque** — a color with transparency gets converted.

```html
<model id="mallet" src="mallet.usdz"></model>
<style>
    model {
        background-color: #f4f1ec;
    }
</style>
```

## 5. Interaction

### Orbit — one attribute

`stagemode="orbit"` lets visitors rotate the model freely side-to-side; tilting up or
down gently **springs back** to the original angle. The element also rescales the model
slightly smaller so no parts get **clipped** during rotation. "Interactive, but always
looking its best."

```html
<model id="mallet"
       src="mallet.usdz"
       stagemode="orbit">
</model>
```

### Custom angles — `entityTransform`

For precise control, set `entityTransform` (a `DOMMatrix` representing the model's
orientation) from JavaScript. To use it you must **disable orbit** — remove `stagemode`
or set it to `"none"`. When transforming manually, parts may clip or disappear if
rotated out of the visible area, so you may need to adjust position. Capture the initial
transform up front to support a Reset.

```html
<model id="boot" src="boot.usdz"></model>
<button id="button-side">Side</button>
<button id="button-reset">Reset</button>

<script>
    const model = document.getElementById("boot");
    const initialTransform = model.entityTransform;

    document.getElementById("button-side")
            .addEventListener("click", () => {
        const transform = new DOMMatrix();
        transform.rotateSelf(0, 135, 0);   // 135° around Y → side view
        model.entityTransform = transform;
    });

    document.getElementById("button-reset")
            .addEventListener("click", () => {
        model.entityTransform = initialTransform;
    });
</script>
```

### Smooth transitions — `requestAnimationFrame`

Setting `entityTransform` switches instantly. To animate, drive it from a
`requestAnimationFrame` loop: hold current angle, a duration (500 ms feels "snappy but
smooth"), and a handle to cancel in-flight animations. `animateTo` cancels any running
animation, captures the start angle and time, then eases each frame
(`1 - (1 - progress)³`, a cubic ease-out) and requests the next frame until done.

```html
<script>
    const model = document.getElementById("boot");
    const duration = 500;
    let currentAngle = 0;
    let animationId = null;

    function animateTo(targetAngle) {
        if (animationId) cancelAnimationFrame(animationId);
        const startAngle = currentAngle;
        const startTime = performance.now();

        function step(now) {
            const progress = Math.min((now - startTime) / duration, 1);
            const ease = 1 - Math.pow(1 - progress, 3);
            currentAngle = startAngle + (targetAngle - startAngle) * ease;
            model.entityTransform = new DOMMatrix().rotateSelf(0, currentAngle, 0);
            if (progress < 1) animationId = requestAnimationFrame(step);
        }

        requestAnimationFrame(step);
    }

    document.getElementById("button-side").addEventListener("click", () => animateTo(135));
    document.getElementById("button-reset").addEventListener("click", () => animateTo(0));
</script>
```

**Trade-off the talk calls out:** custom transforms give full control but require extra
work (bounding boxes, clipping, manual animation code). If the use case allows,
`stagemode="orbit"` gets you interactive 3D with a single attribute.

## 6. Animation playback

Animations authored in Blender/Maya and **baked into the USDZ** play through the
element. It plays the **first animation track**. With a couple of lines of JS, control
`playbackRate` and call `play()`. Positive plays forward, negative reverses, magnitude
scales speed (the demo uses `5` and `-5`).

```html
<model id="bottle" src="bottle.usdz"></model>
<button id="button-play" onclick="play(5)">Play</button>
<button id="button-reverse" onclick="play(-5)">Reverse</button>

<script>
    const model = document.getElementById("bottle");

    function play(rate) {
        model.playbackRate = rate;
        model.play();
    }
</script>
```

## 7. AR and spatial

**AR Quick Look:** wrap the model in `<a rel="ar">` pointing at the same resource. On
iOS and iPadOS, customers get a full AR Quick Look experience — place the product in
their environment.

```html
<a rel="ar" href="bottle.usdz">
    <model id="boot" src="bottle.usdz"></model>
</a>
```

**visionOS:** stereoscopic rendering gives models real depth — customers can pull the
product out of the page and examine it. visionOS also supports **immersive website
environments** built on the Model element, placing the audience inside a scene, all in
Safari. (Deep-dive: "Explore immersive website environments.")

## 8. Optimizing assets for production

Two USD command-line tools, already installed on every Mac, part of the broader USD
tool suite:

- **`usdcrush`** — shrinks a USDZ with no perceived quality change. Demo: a boot model
  goes **7.9 MB → 1.9 MB** (~4x), visually identical side-by-side in Safari.
- **`usdrecord`** — renders a thumbnail or fallback image straight from a 3D file;
  supports output format and rendering from a custom camera baked into the file. Unlike
  a screenshot, it's scriptable across a whole catalog.

Background on the suite: WWDC24's "What's new in USD and MaterialX."

## 9. Standards and ecosystem

The Safari team is actively contributing to the `<model>` specification at the **W3C**
and invites feature requests and use cases via the **Immersive Web Community Group**.
For the file format, the **Alliance for OpenUSD** publishes the full USDZ spec (a
vendor-neutral reference) plus conversion tools and pipeline resources.
