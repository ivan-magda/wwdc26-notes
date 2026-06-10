# Session 204 — What's new in WebKit for Safari 27

- **URL:** https://developer.apple.com/videos/play/wwdc2026/204/
- **Duration:** 16m
- **Speakers:** Jen Simmons

## Description

See what Safari 27 brings to the web platform. The first beta ships over 60 new
features (Customizable Select, `img sizes=auto`, the `stretch` keyword, and more),
but the headline this year is **quality**: the WebKit team shipped over 1,100 fixes
and improvements across five themes — compatibility, rebuilding foundations,
going deep on specific areas, aligning with web standards, and improving how
features integrate. The back half tours five new features: CSS Grid Lanes,
Customizable Select, the HTML `<model>` element (now on iOS/iPadOS/macOS),
immersive website environments on visionOS, and easier Web Extension distribution.

## Key topics

- **Quality over quantity** — 1,100+ fixes since last fall (a record); focus on existing features, not new ones
- **Compatibility** — emoji input bug: `.fromCharCode` truncates code points beyond 16 bits, so WebKit now delivers high-code-point characters as text, not a number
- **Rebuilding foundations** — block-in-inline layout rewritten from scratch (two-decade-old code)
- **Going deep** — 75+ SVG fixes; team revived/leads a new SVG standards effort (SVG 2 disambiguates `fx`/`fy` radial-gradient focal-point defaults). Also: media/video, scrolling, accessibility, WebRTC, HTML tables
- **Standards alignment** — CSS `random()` function: name scoping changed from per-instance to global by default (shipped Safari 26.2, refined 26.5); keeping up with Anchor Positioning, View Transitions
- **Integration** — `min()`/`max()`/`clamp()` now supported inside the `sizes` attribute (closed in Safari 26.4)
- **CSS Grid Lanes** — pure-CSS masonry, shipped Safari 26.4; Web Inspector "Order Numbers" + `flow-tolerance`; Field Guide at gridlanes.webkit.org
- **Customizable Select** — `appearance: base-select`; `::picker`, `::checkmark`, `::picker-icon` pseudo-elements; arbitrary HTML inside options while keeping form-control accessibility
- **HTML `<model>` element** — 3D models in HTML, now on iOS/iPadOS/macOS in Safari 27 (was visionOS-only); `environmentmap`, `stagemode` attributes; JS control; AR Quick Look wrap
- **Immersive website environments** — visionOS 27: `<model>` launches a full immersive environment via a new Immersive API modeled on the Fullscreen API
- **Web Extensions** — Safari Web Extension Packager: submit to App Store Connect from any browser/OS, no Mac or Xcode required
- **MapKit JS** — privacy-preserving interactive maps, all browsers/OSes

## Related sessions to fetch (referenced in this talk)

- [ ] Learn CSS Grid Lanes (Brandon)
- [ ] Rediscover the HTML Select Element (Tim)
- [ ] Get started with HTML Model Element (Aleksei)
- [ ] Explore immersive website environments in visionOS (Jean)
- [ ] Create web extensions for Safari (Kiara)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Jen Simmons; what Safari 27 brings to the web platform, a focus on quality, and a quick tour of new features.
- **1:07 A year of quality improvements** — 1,100+ fixes across five themes: compatibility (emoji input bug), rebuilding foundations (block-in-inline layout), going deep (SVG), aligning with standards (CSS `random()`), and integration (`min`/`max`/`clamp` in `sizes`).
- **9:06 CSS Grid Lanes** — pure-CSS masonry layouts, shipped Safari 26.4; Web Inspector tooling; gridlanes.webkit.org.
- **10:06 Customizable Select** — `appearance: base-select`, new pseudo-elements, arbitrary HTML in options, accessibility preserved.
- **11:24 HTML Model element** — embed 3D models; coming to iOS/iPadOS/macOS in Safari 27; HTML attributes, JavaScript, AR Quick Look.
- **12:51 Immersive Website Environments** — visionOS 27: `<model>` launches a full immersive environment via a new Immersive API modeled on Fullscreen.
- **13:38 Web Extensions** — one cross-browser extension distributed everywhere; Safari Web Extension Packager, App Store Connect, no Mac/Xcode.
- **15:18 MapKit JS** — privacy-preserving interactive maps across all browsers and OSes.
- **15:40 Next steps** — webkit.org, related WWDC sessions, file issues at bugs.webkit.org.

## Code

See `code.md` — no Code tab for this session; the session is a feature tour with snippets shown only as on-screen markup, not extracted code.
