# Code samples — Session 215

Extracted from the session's Code tab. Timestamps map to the transcript chapters.

## Embedding a model — `src` attribute or nested `<source>`

```html
<!-- Using the src attribute -->
<model src="mallet.usdz"></model>

<!-- Using a <source> child for MIME type -->
<model>
    <source src="mallet.usdz" type="model/vnd.usdz+zip">
</model>
```

## Image fallback for unsupported browsers

```html
<model id="mallet" src="mallet.usdz">
    <img src="mallet.png"
         alt="Rubber mallet with wooden handle">
</model>
```

## Loading state — the `ready` promise

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

## Loading the W3C polyfill when the native element is missing

```html
<script type="module">
    if (!window.HTMLModelElement) {
        import("model-element-polyfill.js").then(() => {
            // Polyfill ready to use
        });
    }
</script>
```

## Matching page design — `background-color`

```html
<model id="mallet" src="mallet.usdz"></model>
<style>
    model {
        background-color: #f4f1ec;
    }
</style>
```

## Free rotation — `stagemode="orbit"`

```html
<model id="mallet"
       src="mallet.usdz"
       stagemode="orbit">
</model>
```

## Custom viewing angles — `entityTransform` with a `DOMMatrix`

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
        transform.rotateSelf(0, 135, 0);
        model.entityTransform = transform;
    });

    document.getElementById("button-reset")
            .addEventListener("click", () => {
        model.entityTransform = initialTransform;
    });
</script>
```

## Smooth transition animation with `requestAnimationFrame`

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

## Playing baked-in animation — `play()` and `playbackRate`

```html
<model id="bottle" src="bottle.usdz"></model>
<button id="button-play" onclick="play(5)">
    Play
</button>
<button id="button-reverse" onclick="play(-5)">
    Reverse
</button>

<script>
    const model = document.getElementById("bottle");

    function play(rate) {
        model.playbackRate = rate;
        model.play();
    }
</script>
```

## AR Quick Look — wrap in `<a rel="ar">`

```html
<a rel="ar" href="bottle.usdz">
    <model id="boot" src="bottle.usdz"></model>
</a>
```

---

## Command-line tools named in the talk

- **`usdcrush`** — shrinks a USDZ file with no perceived quality change (demo:
  7.9 MB → 1.9 MB). Ships with macOS.
- **`usdrecord`** — renders a thumbnail / fallback image from a 3D file; supports
  output format and rendering from a custom camera baked into the file. Ships with macOS.
