# Session 215 — Get started with the HTML Model Element

- **URL:** https://developer.apple.com/videos/play/wwdc2026/215/
- **Duration:** 16m
- **Speakers:** Aleksei (Safari team engineer)

## Description

The HTML `<model>` element brings 3D content to the web as simply as adding an
`<img>`. Pioneered on visionOS, it now extends to iOS, iPadOS, and macOS in Safari.
This session walks through getting USDZ assets ready, embedding a model on a page,
handling fallbacks and loading state, styling, letting visitors rotate and explore,
driving custom transforms and animations from JavaScript, enabling AR Quick Look and
spatial rendering, and optimizing assets for production with the USD command-line
tools. It also frames `<model>` as an emerging W3C web standard with a polyfill for
browsers that don't support it natively yet.

## Key topics

- Native `<model>` element vs. the `model-viewer` JavaScript library — no extra
  library, rendered by the platform, stereoscopic on visionOS, future-proof standard
- USDZ as the recommended asset format (geometry + materials + textures + animations
  in one zipped file); Capture / Convert / Create plus generative-AI tools (Tripo3D,
  Meshy.ai)
- Loading via `src` attribute or nested `<source>` with MIME type; `<img>` fallback
  inside `<model>`; the `ready` promise for loading state; W3C polyfill
- `background-color` set directly on the element (always composited opaque)
- `stagemode="orbit"` for free rotation with spring-back and clip-safe rescaling
- `entityTransform` (a `DOMMatrix`) for exact JS-driven viewing angles; smooth
  transitions via `requestAnimationFrame`
- `play()` / `playbackRate` for baked-in USDZ animation (negative rate reverses)
- AR Quick Look via `<a rel="ar">`; stereoscopic rendering and immersive website
  environments on visionOS
- Production optimization: `usdcrush` (file shrink, often ~4x) and `usdrecord`
  (thumbnail / fallback image rendering), both shipping with macOS
- W3C Immersive Web Community Group and the Alliance for OpenUSD spec

## Related sessions to fetch

- [ ] Explore immersive website environments (WWDC26)
- [ ] What's new in USD and MaterialX (WWDC24)
- [ ] What's new for the spatial web (WWDC25)

## Chapter summary

- **0:00 Introduction** — `<model>` brings 3D to the web like an image; pioneered on
  visionOS, now on iOS/iPadOS/macOS. Native vs. `model-viewer`; emerging W3C standard;
  polyfill for non-supporting browsers.
- **2:22 Prepare the USDZ model asset** — Capture/Convert/Create (iPhone scan, file
  conversion, Blender), plus generative AI from images or text prompts. Why USDZ:
  one file bundling geometry, materials, textures, animations.
- **4:18 Loading and fallbacks** — embed with `src` or nested `<source>`; nested
  `<img>` fallback; `await` the `ready` promise; load the W3C polyfill when the native
  element is undefined (some features like stereoscopic display can't be polyfilled).
- **6:14 Model background** — set `background-color` on the element; it renders in its
  own virtual space, doesn't inherit page styles, and the background is always opaque.
- **6:48 Interactions** — `stagemode="orbit"` for free rotation with spring-back and
  clip-safe rescaling; disable stagemode and drive `entityTransform` with a `DOMMatrix`
  for exact JS-set angles (mind clipping when transforming manually).
- **8:26 Transition animation** — animate between orientations by updating
  `entityTransform` inside `requestAnimationFrame`: capture start angle, ease each
  frame, cancel any in-flight animation.
- **10:08 Animation playback** — `play()` + `playbackRate` for animation baked into
  USDZ; positive plays forward, negative reverses, magnitude scales speed.
- **10:52 AR and spatial** — wrap in `<a rel="ar">` for AR Quick Look on iOS/iPadOS;
  on visionOS the element renders stereoscopically and can power immersive website
  environments.
- **12:29 Optimize assets for production** — `usdcrush` shrinks USDZ (demo: 7.9 MB →
  1.9 MB, no perceived quality loss); `usdrecord` renders thumbnails / fallback images;
  both ship with macOS as part of the USD tool suite.
- **14:53 Next steps** — generate a model, add a `<model>` tag, optimize with the USD
  tools, try it across platforms, and join the W3C Immersive Web Community Group.

See `code.md` for the extracted snippets.
