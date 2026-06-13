# Session 8015 — Safari and Web Technologies Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8015/
- **Duration:** 01:04:17 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + WebKit/Safari engineering panel answering developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so attributions below come from self-introductions and host routing, not from the audio.

## Panel

From the opening self-introduction (cue 2). The host names the panel; only Saranya (host) and Jen (evangelist) state their own roles directly, so the rest are inferred from the topics each was routed to during the Q&A.

- **Saranya Parikh** — host; WebKit team at Apple. Runs the lab and routes questions.
- **Jen Simmons** — web evangelist; delivered the Safari 27 "what's new" preview and most of the standards/philosophy answers.
- **Tim Nguyen** — customizable / appearance-based `<select>` and HTML form-control styling (routed the select questions; authored the first draft of the appearance-based form-controls spec).
- **Alexey Marchenko** — HTML `<model>` element; worked on model-element performance (called himself "a person who worked on performance for the model element").
- **Kiara Rose** — web extensions; works in the Web Extensions Community/Working Group.
- **Brandel Zatranak** — spatial web, WebGPU, USD / `<model>`, web standards strategy (handled most of the 3D, WebGPU, and standards-process answers).
- **John** (off-camera "in the back") — immersive website environments on visionOS; referenced as the presenter of the immersive-environments session, not a seated panelist.

> Name caution: whisper has no diarization and renders spoken first names inconsistently. The roster above is taken only from the clear self-introduction at the top. Mid-transcript spellings ("Brendel/Brando/Brandon," "Alexei/Alexa," "Kara," and the caption-style "TIMOTHY JORDAN" / "MELANIE WARRICK" artifacts) are the same people or pure transcription noise, not additional panelists.

## Description

A live, on-demand Safari and Web Technologies group lab. After a short Safari 27 "what's new in WebKit" preview from Jen Simmons, the panel answers developer questions covering the HTML `<select>` element (customizable and appearance-based), the HTML `<model>` element and 3D/spatial web, Spatial CSS, WebGPU/WebGL, web extensions (porting, permissions, API parity, the new Web Extensions Working Group), the web-standards process and WebKit standards positions, WebKit/JavaScriptCore's integration into the OS, performance, and progressive enhancement. The recurring throughline: **learn the fundamentals, lean on what the platform now does for you ("be lazy"), and file bugs/feature requests at bugs.webkit.org with a concrete use case.**

## Key topics

- **Safari 27 preview** — Grid lanes (shipped 26.4), customizable `<select>`, `<model>` element on iOS/iPadOS/macOS, immersive website environments on visionOS 27, web extensions from a prompt; the headline is **1,100+ feature improvements and fixes** since last fall (a deliberate browser-engine quality push).
- **Fundamentals to learn** — HTML/CSS/JavaScript and *why* there are three; semantic HTML; progressive enhancement; cross-browser/cross-OS support; accessibility for free via semantic elements; plus 3D / spatial web (model element, WebXR).
- **Customizable & appearance-based `<select>`** — always keep textual content for progressive enhancement and accessibility; appearance-based gives a 100% interoperable, fully stylable base with addressable pseudo-elements (picker icon, checkmark, drop-down) and consistent DOM across browsers; you can lay out the picker with Grid/Flexbox/Grid lanes.
- **HTML `<model>` element & 3D** — loading via a `ready` promise (no loading pseudo-class yet, logged as feedback); keep models small (~10 MB target for max visual quality); USD/USDZ; USD Crush tool now compresses textures to AVIF; mesh-compression format under discussion with the Alliance for Open Media.
- **Spatial CSS** — a proposal/spec draft, not shipping; extends absolute/anchor positioning into depth; 3D transforms on multiple objects; relevant to e-commerce 3D configurators on phone/Mac as well as Vision Pro.
- **WebGPU / WebGL** — WebGPU is the successor to WebGL (started ~2010), matched to modern silicon; performance is never "done."
- **Web extensions** — built from JS/CSS/HTML; port from Chrome/Firefox (load a temporary extension via the Develop menu); Safari doesn't fail on unsupported manifest keys; privacy-first permission model (manifest permissions aren't auto-granted; active-tab permission as a least-privilege approach); the **Web Extensions Working Group** was just founded after ~5 years as a community group; extension support moved into WebKit "a few years ago" so developers can contribute.
- **Standards process & WebKit standards positions** — published on GitHub; "users first, developers second, ourselves third" (HTML5 priority-of-constituencies); the `:has()` parent-selector story (long-time "never," then Igalia [transcript: "Agalia"] reopened it and WebKit made it fast); standards positions don't block filing real user needs.
- **WebKit/JavaScriptCore in the OS** — deeply integrated across every platform including watchOS (no browser there, but JavaScriptCore runs); WebKit is now a SwiftUI view; web apps can be as good as or better than native.
- **Performance** — never an endgame; battery as a first-class metric; WebKit PNP (Power and Performance) team runs perf tests per commit; "fastest browser."
- **Quality push** — rewrote the JavaScript module loader to fix the top-level `await` bug; release notes "twice the normal length"; counts 26.2 / 26.4 / 26.x plus 27.

## Related sessions (referenced on-air)

Named by title during the preview and Q&A; session numbers not stated on-air, so left unnumbered.

- [ ] "Rediscover the HTML select element in Safari 27" (Tim) — customizable `<select>`.
- [ ] "Get started with the HTML model element" (Alexey) — `<model>` element.
- [ ] "Explore immersive website environments in visionOS" (John) — immersive web environments.
- [ ] "Create web extensions for Safari" (Kiara) — building/distributing extensions without Xcode or a Mac.
- [ ] "What's new in WebKit for Safari 27" — the quality/improvements overview.
- [ ] "Learn CSS Grid lanes" (referenced as "Brandon will walk you through it") — Grid lanes tutorial.

## Chapter arc (rough, from timestamps)

No published chapters (group lab). Approximate arc:

- **00:00** — Welcome, panel intro, ground rules (forums + bugs.webkit.org).
- **01:05** — Jen Simmons: Safari 27 / WebKit preview (Grid lanes, customizable select, model element, immersive environments, web extensions, 1,100+ fixes).
- **04:30** — Developer Q&A begins.
  - **~04:39** — What every new web dev should learn (fundamentals, accessibility, 3D, "be lazy").
  - **~09:52** — Upcoming standards the team is most excited about (Web Extensions WG, CSS random item, Spatial CSS).
  - **~16:06** — Web standards advocacy, WebKit standards positions, the `:has()` story.
  - **~26:47** — WKWebView cookie sync (deferred to forums).
  - **~27:41** — Extension API parity with Chrome/Firefox (tab group APIs, etc.).
  - **~29:31** — Spatial CSS on Vision Pro / what ships today (model element).
  - **~30:42** — Performance endgame? (WebGL→WebGPU, batteries, per-commit perf tests).
  - **~35:11** — SEO extension surfacing reports in a SwiftUI window (native messaging).
  - **~36:08** — Progressively enhancing `<select>` (keep textual content).
  - **~41:25** — `<model>` loading states/errors; model size guidance; USD Crush / AVIF.
  - **~47:33** — Extension permissions vs other browsers (privacy-first, active-tab).
  - **~49:05** — Immersive API interaction with page content / handoff.
  - **~50:23** — Is `<model>` the on-ramp to spatial web for everyday devs?
  - **~51:48** — Appearance-based select: what "just works" vs needs resetting (the "spicy" disagreement).
  - **~56:39** — Porting a Chrome/Firefox extension to Safari (effort, manifest gotchas).
  - **~58:46** — Closing round: one thing each panelist wants developers to know.
- **~01:03:19** — Wrap-up (forums, bugs.webkit.org, webkit.org, grid lanes field guide, survey).

## Code

See `code.md` — no Code tab for this session; the lab is verbal Q&A with no on-screen code. API/feature names are captured in `digest.md`.
