---
title: "What's new in WebKit for Safari 27 — Full Digest"
session: WWDC26 · 204
url: https://developer.apple.com/videos/play/wwdc2026/204/
duration: 16m
speakers: Jen Simmons
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 204 — What's new in WebKit for Safari 27

## TL;DR

This year's WebKit story is **quality, not quantity**. The team deliberately put
its effort into fixing and refining existing web-platform features rather than
shipping a flood of new ones — **over 1,100 fixes and improvements since last
fall**, which Jen Simmons calls a record. The first beta of Safari 27 still ships
**60+ new features** on top of that.

Two halves to the talk:

1. **A year of quality** organized into five themes, each told as one story:
   - **Compatibility** — an emoji-input bug rooted in `String.fromCharCode`.
   - **Rebuilding foundations** — block-in-inline layout rewritten from scratch.
   - **Going deep** — 75+ SVG fixes, plus reviving a dormant SVG standards group.
   - **Standards alignment** — the CSS `random()` function's name scoping changed
     from per-instance to global by default.
   - **Integration** — `min()`/`max()`/`clamp()` finally work inside `sizes`.
2. **Five new features** worth a closer look: **CSS Grid Lanes**, **Customizable
   Select**, the **HTML `<model>` element** (now beyond visionOS), **immersive
   website environments** on visionOS, and dramatically easier **Web Extension**
   distribution. Plus a quick **MapKit JS** mention.

---

## 1. A year of quality improvements

The framing: web developers find it costly to work around browser-engine bugs, so
WebKit chose to invest in the quality of what already exists. Five themes emerged.

### Theme 1 — Compatibility (the emoji bug)

A real-site report: when users typed emoji, the **wrong character** appeared.

- When a key is pressed, the browser sends a Unicode **number** to the page. `A`
  fits in 7 bits.
- For decades sites have intercepted input and called **`String.fromCharCode`** to
  turn numbers back into characters. `fromCharCode` only handles **16 bits or
  less** — historically fine.
- Newer emoji got assigned bigger code points. The example needs **17 bits**, so on
  sites still using `fromCharCode` the number was **truncated to 16 bits**, mapping
  to a totally different character.
- Asking the whole web to stop using `fromCharCode` isn't realistic. WebKit's fix:
  when a typed character's code point is **beyond 16 bits, WebKit doesn't send the
  number at all — the character arrives as text**. No number, no truncation.

The lesson: a lot of "quality" work is invisible plumbing that makes real sites
work for real users.

### Theme 2 — Rebuilding foundations (block-in-inline layout)

Block elements nested inside inline elements show up constantly. The layout code
handling this was **over two decades old**, tangled, and hard to maintain. The team
**rewrote it from scratch** with a new architecture. You won't see it directly, but
it fixed a batch of issues and makes everything built on top more reliable.

### Theme 3 — Going deep (SVG)

The team is making concentrated progress in specific areas: **media/video playback,
scrolling, SVG, accessibility, WebRTC, even HTML tables.** SVG is the worked example:

- Real progress needed a clearer spec, but there was **no active SVG Working Group**.
  So the team **revived and now leads a new SVG standards effort.**
- Concrete case: a radial gradient with no explicit focal point (`fx`/`fy`). SVG 1's
  text on the **initial values of `fx`/`fy`** was confusing and browsers diverged.
  **SVG 2 removes the ambiguity**, and WebKit is updating to match in Safari 27.
- **75+ SVG improvements** so far, with more to come.

### Theme 4 — Standards alignment (CSS `random()`)

Hundreds of updates to better track web standards, both shoring up old features and
keeping pace with new ones. The example is the new CSS **`random()`** function:

```css
.box {
  /* random lengths for width and height */
  width:  random(100px, 400px);
  height: random(100px, 400px);
}
```

- You can **name** random values to reuse them. Originally names were **scoped per
  instance** — each `.box` recomputed new values.
- Shipped in **Safari 26.2**; developers argued it could be better; after CSS
  Working Group discussion, **names were redefined as global by default**, so all
  boxes share the same random size. Rescoped in **Safari 26.5**.
- WebKit is still the only engine with `random()`, so there was room to refine it.
- Also keeping current with **Anchor Positioning** and **View Transitions**.

### Theme 5 — Integration (`min`/`max`/`clamp` in `sizes`)

Separate features need to work together:

- 2014: WebKit shipped the HTML **`sizes`** attribute (responsive images).
- 2018: shipped CSS **`min()`** / **`max()`** — but missed supporting them **inside
  `sizes`**.
- 2020: **`clamp()`** arrived; that gap inside `sizes` stayed open too.
- **Safari 26.4**: circled back and closed the gap. Thanks to the developers who
  filed the issues.

### Scope of the effort

- **1,100+ feature improvements and fixes** since last fall — a record.
- Web tech in WebKit/JavaScriptCore underpins **millions of App Store apps** across
  iOS, iPadOS, macOS, visionOS, and watchOS.
- Apple asks developers to test in **Safari Technology Preview / Safari beta** and
  file issues; full detail lives in the **Safari release notes**.

---

## 2. CSS Grid Lanes — pure-CSS masonry

Shipped in **Safari 26.4**. Creates the classic **masonry layout in pure CSS, no
JavaScript**, and it works in both directions. It builds on CSS Grid, so you keep
all of Grid's track-definition power, and it covers cases beyond typical masonry.

- **Field Guide** at **gridlanes.webkit.org** — click through configurations and
  demos.
- **Safari Web Inspector** helps: enable **Order Numbers** to reveal item order —
  useful when tuning **`flow-tolerance`** so keyboard/tab order stays sensible.
- Deep-dive: **"Learn CSS Grid Lanes"** (Brandon).

## 3. Customizable Select

Safari 27 lets you fully restyle `<select>` while keeping built-in form-control
accessibility — no rebuilding a dropdown from `<div>`s.

- Opt in with **`appearance: base-select`** on the element; it then inherits more
  CSS (font family, text color, background color).
- Apply **`appearance: base-select`** to the new **`::picker`** pseudo-element to
  style the popup menu of options.
- New pseudo-elements target specific parts: **`::checkmark`** and
  **`::picker-icon`**.
- You can put **arbitrary HTML inside options** — subtext, images per option — and
  lay them out with Grid/Flexbox or anything in CSS, making it look nothing like a
  traditional dropdown, all while it stays a real, accessible, robust form control.

```css
select,
select::picker(select) {
  appearance: base-select;
}
select::picker-icon { /* style the toggle icon */ }
option::checkmark   { /* style the selected check */ }
```

- Deep-dive: **"Rediscover the HTML Select Element"** (Tim).

## 4. HTML `<model>` element

Shipped last year for Safari **in visionOS**; in **Safari 27 it comes to iOS,
iPadOS, and macOS**. It joins the media-element family (alongside the audio/video
elements) — this time embedding **3D models** in HTML. Use cases: previewing
products, placing an object in your space, or just a fun experience.

- Markup works like other media elements — keep it simple, or use **`<source>`** to
  offer the model in multiple formats.
- Optional attributes: **`environmentmap`** (custom lighting) and **`stagemode`**
  (default interaction behavior).
- Drive it from **JavaScript** for a wide range of behavior, or wrap the model for
  **AR Quick Look** so iOS/iPadOS users can view the product in their space.
- developer.apple.com has years of docs, videos, and sample projects on making/using
  3D models.
- Deep-dive: **"Get started with HTML Model Element"** (Aleksei) — where to get a
  model, how to optimize it for the web, and JavaScript usage.

```html
<model>
  <source src="chair.usdz" type="model/vnd.usdz+zip">
  <source src="chair.glb"  type="model/gltf-binary">
</model>
```

## 5. Immersive website environments (visionOS 27)

In **visionOS 27**, `<model>` goes further: a user visits your site in Safari, taps
to **open an immersive environment**, and **steps into the model** you provide.

- Examples: preview an immersive video game; a ticket site shows the **view from a
  seat** on any platform, and on visionOS lets the user experience the whole theater.
- New **Immersive API** to manipulate models — **"works just like the Fullscreen
  API."**
- Deep-dive: **"Explore immersive website environments in visionOS"** (Jean) — how
  he built the theater demo.

## 6. Web Extensions — one codebase, distributed everywhere

The arc: browser extensions used to mean separate projects per browser. The industry
moved toward interoperability — Mozilla deprecating old Firefox add-ons in **2017**,
Safari shipping **Safari Web Extensions in Safari 14 (2020)**, and Apple helping
create the **W3C WebExtensions Community Group in 2021** to standardize it.

- Today you can build **one extension** — one codebase, one set of scripts, one
  manifest, all interoperable HTML/CSS/JS — and ship it to every browser.
- The remaining friction was distribution. New: the **Safari Web Extension
  Packager** lets you **package and submit via App Store Connect from any browser on
  any OS — no Mac and no Xcode required.**
- Deep-dive: **"Create web extensions for Safari"** (Kiara) — building an extension
  from scratch and using App Store Connect.

## 7. MapKit JS (quick mention)

Embed **interactive maps** on your site/app while **preserving user privacy**. Works
in **all browsers on any OS**. Details at developer.apple.com.
