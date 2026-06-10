# Code samples — Session 320

Extracted from the Code tab. Mix of HTML markup, JavaScript, and one shell command.

## `<model>` element — basic 3D model

```html
<model src="teapot.usdz">
</model>
```

## `<model>` element — with environment map

```html
<model src="teapot.usdz"
	environmentmap="kitchen.hdr">
</model>
```

## Theater inline preview markup

```html
<div class="seat-preview">
	<model id="theater"
		   src="theater-model.usdz"
		   environmentmap="theater-lighting.hdr">
	</model>
</div>
```

## Identity transform — remove default fit-to-bounds scaling

```javascript
const theater = document.getElementById("theater");

async function updateModelTransform() {
	// Make sure the model is loaded
	await theater.ready;
	// Create a transform matrix
	const identity = new DOMMatrix();
	// Apply the transform matrix to the model
	theater.entityTransform = identity;
}

updateModelTransform();
```

## Eye-level translation (down 1m)

```javascript
const theater = document.getElementById("theater");

async function updateModelTransform() {
	// Make sure the model is loaded
	await theater.ready;
	// Create a transform matrix
	const transform = new DOMMatrix();
	// Translate model down, for eye level preview
	transform.translateSelf(
		0, 		// x
		-1.0, 	// y
		0 		// z
	);
	// Apply the transform matrix to the model
	theater.entityTransform = transform;
}

updateModelTransform();
```

## Build per-seat transform (rotation + translation)

```javascript
function buildTransform(seat) {
	const transform = new DOMMatrix();
	const { x, y, z, ry } = seat;
	// Rotate and translate the model to match
	// the seat's origin and orientation
	transform.rotateSelf(0, -ry, 0);
	transform.translateSelf(-x, -y, -z);
	// Translate the model down, for eye level preview
	transform.translateSelf(0, -1.0, 0);
	return transform;
}
```

## Feature detection — show the immersive button only where supported

```javascript
if (document.immersiveEnabled) {
	immersiveButton.hidden = false;
}
```

## Request immersive on user interaction

```javascript
immersiveButton.addEventListener("click", async () => {
	await model.requestImmersive();
});
```

## Branch the transform on inline vs immersive

```javascript
function buildTransform(seat, immersive) {
	const transform = new DOMMatrix();
	// [...] Seat transform logic
	if (immersive) {
		// Rotate to the left
		transform.rotateSelf(
			0,		// x
			45,		// y
			0			// z
		);
	} else {
		// [...] Eye level translation
	}
	return transform;
}
```

## React to `immersivechange` — recompute transform + update layout

```javascript
theater.addEventListener("immersivechange", () => {
	const isImmersive = !!document.immersiveElement;
	const transform = buildTransform(isImmersive, currentSeat);
	theater.entityTransform = transform;
	document.body.classList.toggle("immersive", isImmersive);
});
```

## Escape room — hide the inline preview with `display: none`

```html
<model id="escapeRoom"
	   src="escape-room.usdz"
	   environmentmap="room-lighting.hdr"
	   style="display: none">
</model>
```

## Escape room — request immersive on button click

```javascript
const enterButton = document.getElementById("enterButton");
const escapeRoom = document.getElementById("escapeRoom");

enterButton.addEventListener("click", () => {
    await escapeRoom.requestImmersive();
});
```

## Escape room — loading animation around the request

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

## Video docking — request fullscreen on the video

```javascript
const trailerVideo = document.getElementById("trailerVideo");
const demoButton = document.getElementById("demoButton");

demoButton.addEventListener("click", async () => {
	await trailerVideo.requestFullscreen();
});
```

## On video end — exit fullscreen, then play the door animation

```javascript
const trailerVideo = document.getElementById("trailerVideo");
const escapeRoom = document.getElementById("escapeRoom");

trailerVideo.addEventListener("ended", async () => {
	await document.exitFullscreen();
	escapeRoom.play();
});
```

## Compress USDZ textures with usdcrush

```shell
usdcrush model.usdz -o optimized.usdz
```

---

## Useful API facts surfaced by the code

- The HTML `<model>` element takes `src` (USDZ) and an optional `environmentmap` (360° HDR for reflections/lighting).
- `model.ready` is a promise to await before reading/writing `entityTransform`.
- `entityTransform` is a `DOMMatrix`; `new DOMMatrix()` is identity, overriding the default fit-to-bounds scaling. Use `translateSelf(x, y, z)` and `rotateSelf(x, y, z)` (degrees) — note `-ry` is applied for the seat's stage orientation.
- Coordinates are right-handed Y-up (web convention); eye-level preview is a -1.0 m Y translation.
- `document.immersiveEnabled` — feature detection; `document.immersiveElement` — the currently immersive element (null when none).
- `model.requestImmersive()` is async and must be called from a user gesture; mirrors `element.requestFullscreen()`.
- `immersivechange` event fires on the model element when entering/exiting immersive (e.g. via Digital Crown).
- `style="display: none"` on a `<model>` hides the inline layer but still permits `requestImmersive()`, and defers asset download/decode until entry.
- Video docking reuses the standard Fullscreen API: `video.requestFullscreen()` docks onto a RealityKit-tagged surface; `document.exitFullscreen()` undocks.
- `model.play()` triggers a USDZ-embedded animation; `model.currentTime` scrubs the animation timeline.
