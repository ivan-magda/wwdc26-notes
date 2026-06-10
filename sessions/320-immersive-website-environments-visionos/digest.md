---
title: "Explore immersive website environments in visionOS — Full Digest"
session: WWDC26 · 320
url: https://developer.apple.com/videos/play/wwdc2026/320/
duration: 19m
speakers: Jean (visionOS Safari team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 320 — Explore immersive website environments in visionOS

## TL;DR

visionOS Safari gets an **immersive API for the web**: the HTML `<model>` element plus a
JavaScript `requestImmersive()` call and a `:immersive` CSS pseudo-class let a website open a
full virtual environment *around* the page — without replacing it. Headline themes:

1. **It's the Fullscreen API pattern, for 3D environments.** If you know `requestFullscreen()`,
   you know this: `requestImmersive()`, exit, `document.immersiveEnabled`,
   `document.immersiveElement`, `immersivechange` / error events, and a CSS pseudo-class. The
   key difference: immersive wraps an environment *around* the still-visible page (Fullscreen
   replaces content), and **both can be active at once** — a fullscreen video inside an immersive
   environment.
2. **Two real patterns.** A theater ticket site with an **inline seat preview** that transitions
   into the full theater at the chosen seat; and an escape-room marketing site that **skips the
   inline preview** entirely (`display: none`) and drops you straight into the room.
3. **A handful of lines of code.** The whole immersive transition is feature-detect + a
   `requestImmersive()` in a click handler + an `immersivechange` listener to fix up the
   transform and layout.
4. **Polish via RealityKit annotations + asset hygiene.** Video docking into the scene,
   JavaScript-triggered model animations, Safari window shadow casting, plus vertex/entity
   reduction and `usdcrush` texture compression to keep heavy environments fast.

The author (Jean) authors annotations in **Blender** via a custom plugin; Reality Composer Pro is
the other route. A bonus: `<img controls>` gives spatial photos / panoramas an immersive
fullscreen affordance with one attribute.

---

## 1. Meet the immersive API

It starts with the HTML **`<model>`** element, which displays a 3D model (a USDZ asset) on a
page. An optional **`environmentmap`** — a 360° HDR image capturing the surrounding lighting —
adds reflections and lighting on shiny surfaces.

```html
<model src="teapot.usdz">
</model>
```

```html
<model src="teapot.usdz"
	environmentmap="kitchen.hdr">
</model>
```

> Deeper coverage of the element itself (and its cross-platform behavior) is in **"Get started
> with the HTML Model Element."**

The immersive layer mirrors the **Fullscreen API**:

| Fullscreen API | Immersive API |
| --- | --- |
| `element.requestFullscreen()` | `model.requestImmersive()` |
| `document.exitFullscreen()` | exit immersive |
| `document.fullscreenEnabled` | `document.immersiveEnabled` |
| `document.fullscreenElement` | `document.immersiveElement` |
| `fullscreenchange` / `fullscreenerror` | `immersivechange` / error |
| `:fullscreen` CSS | `:immersive` CSS |

The crucial difference: Fullscreen **replaces** the page content with the element; immersive
**transports the model beyond the browser bounds while keeping the page visible**. And the two
APIs can be active simultaneously — e.g. a fullscreen video player while immersed in an
environment. You can use it trivially (one `requestImmersive()` call) or compose it with other
APIs for richer experiences.

## 2. Preview environments inline (the ticket site)

Inline previews are a good first step — introduce the environment in-page before going
immersive. Drop a `<model>` into the seat-preview div:

```html
<div class="seat-preview">
	<model id="theater"
		   src="theater-model.usdz"
		   environmentmap="theater-lighting.hdr">
	</model>
</div>
```

By default the model is **scaled to fit the element's bounds**, so you see the whole theater
from outside — not useful here. Override that by setting **`entityTransform`** (a `DOMMatrix`
controlling position, rotation, scale). Await `model.ready` first, then apply an identity matrix
to strip the default fit:

```javascript
const theater = document.getElementById("theater");

async function updateModelTransform() {
	await theater.ready;
	const identity = new DOMMatrix();
	theater.entityTransform = identity;
}

updateModelTransform();
```

The theater model's origin sits on its floor, which lands at the layer's center. To preview at
human eye level, translate the model **down 1 m** (the measured eye height of someone seated):

```javascript
const transform = new DOMMatrix();
transform.translateSelf(0, -1.0, 0); // x, y, z
theater.entityTransform = transform;
```

To match the *selected seat's* viewpoint, a JSON file maps each seat to a translation (origin →
seat bottom) and an orientation angle toward the stage. Values are in the **right-handed Y-up**
coordinate system (the web convention). `buildTransform` applies the seat rotation, the seat
translation, then the eye-level drop:

```javascript
function buildTransform(seat) {
	const transform = new DOMMatrix();
	const { x, y, z, ry } = seat;
	transform.rotateSelf(0, -ry, 0);
	transform.translateSelf(-x, -y, -z);
	transform.translateSelf(0, -1.0, 0); // eye level
	return transform;
}
```

This inline preview also works on **macOS and iOS** — not just visionOS.

## 3. Go immersive

First, feature-detect and reveal the button only where immersive is supported:

```javascript
if (document.immersiveEnabled) {
	immersiveButton.hidden = false;
}
```

Then request immersive **from a user gesture** (the button tap):

```javascript
immersiveButton.addEventListener("click", async () => {
	await model.requestImmersive();
});
```

**Inline and immersive models have different reference frames:**

- **Inline:** origin at the center of the inline layer; scale follows CSS conventions.
- **Immersive:** origin at the **person's feet on the floor**; scale is **true to the real world**.

Also, the immersive environment **opens from behind Safari's window**, so keep the focal point
(here, the stage) visible without the visitor having to move the window. `buildTransform` gains an
`immersive` flag: when immersive, add a slight rotation so the stage clears the window, and apply
the eye-level translation **only when inline**:

```javascript
function buildTransform(seat, immersive) {
	const transform = new DOMMatrix();
	// [...] Seat transform logic
	if (immersive) {
		transform.rotateSelf(0, 45, 0); // rotate to the left
	} else {
		// [...] Eye level translation
	}
	return transform;
}
```

Because the transform depends on immersive state, recompute it whenever the model enters or
exits immersive — listen to **`immersivechange`**. While there, update page layout (swap the
model interface, show an exit button):

```javascript
theater.addEventListener("immersivechange", () => {
	const isImmersive = !!document.immersiveElement;
	const transform = buildTransform(isImmersive, currentSeat);
	theater.entityTransform = transform;
	document.body.classList.toggle("immersive", isImmersive);
});
```

Always present a **clear exit affordance**. And note: an Apple Vision Pro wearer can press the
**Digital Crown** to dismiss the environment at any time, so any UI that depends on immersive
state *must* react to `immersivechange`.

### The escape room — skip the inline preview

To keep the surprise (and save bandwidth), set the model to `display: none`. That hides the
inline layer but still permits `requestImmersive()` — and the asset isn't downloaded or decoded
until the immersive request actually fires, which matters for heavy environment models if the
visitor never enters:

```html
<model id="escapeRoom"
	   src="escape-room.usdz"
	   environmentmap="room-lighting.hdr"
	   style="display: none">
</model>
```

```javascript
enterButton.addEventListener("click", () => {
    await escapeRoom.requestImmersive();
});
```

Because the model isn't preloaded, entry can take a moment for heavy assets, so wrap it in a
loading animation:

```javascript
enterButton.addEventListener("click", async () => {
	showLoadingAnimation();
	try {
		await escapeRoom.requestImmersive();
	} catch (error) {
		console.log(error);
	} finally {
		hideLoadingAnimation();
	}
});
```

## 4. Optimize the experience

Small touches that make the escape room come alive — most rely on **custom RealityKit
annotations** baked into the USDZ. These aren't yet standards; the author adds them with a
custom **Blender** plugin (Reality Composer Pro is the alternative).

### Video docking

Instead of playing inline, **dock** the video into the scene — onto a TV, projector, or
billboard — and add materials that diffuse/reflect its light. Tag the surface as the video
docking region (Blender plugin), bake the light spill onto materials, then dock by requesting
**fullscreen on the video**, which moves it to the tagged surface and hides Safari's window:

```javascript
demoButton.addEventListener("click", async () => {
	await trailerVideo.requestFullscreen();
});
```

The TV's light then spills onto floor and walls, dramatically raising realism.

### Model animations from JavaScript

The door-opening animation is authored in Blender, then played at the right moment. When the
trailer ends, exit fullscreen (undocking the video, bringing back the site), then play the model
animation:

```javascript
trailerVideo.addEventListener("ended", async () => {
	await document.exitFullscreen();
	escapeRoom.play();
});
```

Animations can be richer than one-shots: build a timeline and use the model's **`currentTime`**
property to scrub through it, transforming the environment in stages. More in **"What's new for
the spatial web."**

### Window shadow casting

Safari's window casting a shadow onto the environment helps people place the window in space.
Enable it with a RealityKit annotation: tag the meshes that should receive shadows with the
**Scene Understanding component**. Use a dedicated **low-poly mesh** for this — computing shadows
on a complex mesh is expensive.

### Performance

Environment models are heavier than object models. To keep them fast to render and download:

- **Reduce vertex count** — don't export meshes invisible from the origin viewpoint.
- **Reduce entity count** — merge (e.g. desk + its decorations) to avoid many separate entities.
- **Use low-poly meshes** where appropriate (e.g. the shadow-receiving mesh).
- **Keep shaders simple** — bake lighting/shadows into textures so materials can be **unlit**,
  skipping runtime shading.
- **Compress textures with `usdcrush`** (built into any Mac):

```shell
usdcrush model.usdz -o optimized.usdz
```

> Deeper asset optimization is in **"Optimize your custom environments for visionOS."**

## 5. Image controls (bonus)

Adding a single **`controls`** attribute to an `<img>` element gives native, platform-specific
controls. On visionOS that includes making a **panorama** fullscreen (wrapping it around the
viewer) — and it works for **spatial photos** captured on Apple Vision Pro or iPhone. One small
markup change, big payoff.
