---
title: "Safari and Web Technologies Group Lab — Full Digest"
session: WWDC26 · 8015
url: https://developer.apple.com/videos/play/wwdc2026/8015/
duration: 01:04:17
sources: transcript.md (whisper.cpp ggml-large-v3, SD video audio), meta.md
compiled: 2026-06-13
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below come from self-introductions and host routing, not the audio. Garbled API/spec/name spellings are flagged inline.
---

# WWDC26 · 8015 — Safari and Web Technologies Group Lab

> A 64-minute live developer Q&A with a WebKit/Safari panel. It opens with a Safari 27
> "what's new in WebKit" preview, then runs through upvoted developer questions on the
> `<select>` element, the `<model>` element and 3D/spatial web, Spatial CSS, WebGPU,
> web extensions, the standards process, and performance. Two refrains carry the hour:
> **learn the fundamentals and let the platform do the work for you** ("be lazy"), and
> **file bugs/feature requests at bugs.webkit.org with a concrete use case**, because
> use cases (not vote counts) set WebKit's priorities.

## TL;DR

- **The headline of Safari 27 is quality, not a feature.** The panel framed the biggest
  WebKit news of the year as **1,100+ feature improvements and fixes since last fall**: a
  deliberate effort to chase down "little paper cuts." Jen Simmons said the Safari 27
  beta release notes are roughly **twice the normal length**, and that counts on top of
  26.2 / 26.4 / the other 26.x notes. They **rewrote the JavaScript module loader** to
  fix the long-standing **top-level `await` bug**.
- **What actually shipped vs. what's a proposal.** Shipping in/around Safari 27:
  **Grid lanes** (masonry-style layout, shipped in **26.4**), **customizable `<select>`**
  (fall, Safari 27), the **HTML `<model>` element** on **iOS / iPadOS / macOS 27**, and
  **immersive website environments on visionOS 27**. **Spatial CSS** is explicitly a
  **proposal/spec draft, not shipping** and with no ship news; the panel feels positive
  about it because it originates from Apple.
- **`<select>`, done right, is progressive enhancement for free.** Always keep textual
  content in your options. Browsers without customizable select fall back to the plain
  text list; browsers with it get the styled, icon-rich version, and the developer does
  nothing extra. **Appearance-based select** gives a **100% interoperable base** with a
  **consistent DOM across browsers** and **addressable pseudo-elements** (picker icon,
  checkmark, drop-down). You can even lay out the picker pop-over with **Grid, Flexbox,
  or Grid lanes**. The "spicy" on-air disagreement: Tim and Jen don't fully agree on what
  the *default* appearance-based look should be (retro-plain vs. modern-by-default).
- **The `<model>` element is the easy on-ramp to spatial web.** It "works just like the
  other HTML media elements" and is scriptable. Loading is signaled by a **`ready`
  promise**; there is **no loading pseudo-class yet** (logged on-air as good feedback).
  Guidance: treat models like images, keep them small. They found people hit max visual
  quality around **~10 MB** (bigger than the same thing as an image). **USD Crush** (an
  Alliance for OpenUSD command-line tool) now compresses textures to **AVIF** instead of
  JPEG, and Apple is in talks with the **Alliance for Open Media** about a mesh-compression
  format.
- **Web extensions are getting a real standard.** The **Web Extensions Working Group** was
  just founded after ~5 years as a bi-weekly community group, pushing toward
  cross-browser interoperability and an actual spec. Safari's permission model is
  **privacy-first**: manifest permissions are **not auto-granted**, and **active-tab
  permission** is the recommended least-privilege approach. Porting from Chrome/Firefox:
  load a **temporary extension via the Develop menu**, and Safari **doesn't fail on
  unsupported manifest keys**. Extension support **moved into WebKit** a few years ago, so
  developers can contribute directly.
- **WebKit/JavaScriptCore is OS infrastructure, not just a browser.** JavaScriptCore is "a
  big chunk of the operating system"; any app anywhere running JS is using it. **WebKit is
  now a SwiftUI view.** It runs even on **watchOS** (no browser there, but WebKit and
  JavaScriptCore are running). That integration is why performant web apps can match or
  beat native, and why performance/battery work never ends. The **WebKit PNP (Power and
  Performance) team** runs perf tests on (close to) every commit.

## Panel

Host **Saranya Parikh** (WebKit team) with **Jen Simmons** (web evangelist),
**Tim Nguyen** (customizable / appearance-based `<select>`, HTML form-control styling),
**Alexey Marchenko** (HTML `<model>` element, model performance), **Kiara Rose** (web
extensions; Web Extensions Working Group), and **Brandel Zatranak** (spatial web, WebGPU,
USD/`<model>`, standards strategy). A teammate, **John**, is off-camera "in the back" and
is the presenter of the immersive-environments session, not a seated panelist. Saranya
noted a triage team works behind the scenes on inbound questions.

> Name caution: this transcript has no speaker diarization. The roster is taken only from
> the self-introduction at the start. Later spellings ("Brendel/Brando/Brandon,"
> "Alexei/Alexa," "Kara," and caption-style artifacts like "TIMOTHY JORDAN" and
> "MELANIE WARRICK") are the same people or pure transcription noise. Attribution below
> is by host routing where stated and left unattributed otherwise.

## Safari 27 preview (Jen Simmons, 01:05–04:26)

- **Grid lanes** — shipped in **Safari 26.4**; masonry-style layouts with "really simple
  CSS." Field guide at **gridlanes.webkit.org**; tutorial "Learn CSS Grid lanes."
- **Customizable `<select>`** — coming this fall in **Safari 27**; fully style the `<select>`
  element to match your design and add custom content with the accessibility and
  robustness of HTML forms. Session: "Rediscover the HTML select element in Safari 27."
- **HTML `<model>` element** — comes to **iOS, iPadOS, and macOS**; add a 3D model to a
  page, works like other HTML media elements, scriptable via JavaScript. Session: "Get
  started with the HTML model element."
- **Immersive website environments** — come to **visionOS 27**; use the web to provide an
  immersive environment (e.g., previewing tickets while choosing a seat). Session:
  "Explore immersive website environments in visionOS."
- **Web extensions** — "Create web extensions for Safari" walks through building and
  distributing an extension to Safari users **without Xcode or even without a Mac**.
- **The real headline: quality.** "Not any particularly exciting specific one feature,"
  but a deliberate effort to improve the engine. **Over 1,100 feature improvements and fixes
  since last fall.** Session: "What's new in WebKit for Safari 27." Test projects in the
  latest versions; file at **bugs.webkit.org** or Feedback Assistant.

## Developer Q&A

### Fundamentals, learning, and "be lazy"

- **What every new web developer should learn for the future (opening question).**
  The answer is "kind of the same" even as everything changes: understand **why** there
  are three web languages (HTML/CSS/JS); learn **semantic HTML**, which now does
  declaratively much of what used to require JavaScript; learn **CSS layout** and making a
  site work on every screen; **progressive enhancement** and cross-browser/cross-OS/older
  support. These fundamentals are what let you judge whether **LLM-generated code is good
  code**, and they "last your entire career." A second voice: the platform has absorbed
  many old workarounds (**CSS preprocessors, jQuery**), so know the "state of the art" and
  ship the **simplest** code. A third added **3D and spatial web** (model element, WebXR):
  the web was 2D for decades; that's changing. A fourth pushed **accessibility**: using
  semantic HTML "instead of custom JavaScript … helps accessibility already." The
  running theme, attributed to John "in the back": **make it easy, choose the easier
  path.** Semantic HTML gives you accessibility, robustness, and progressive enhancement
  "for free." Stated as the **"lesson of the group lab: be lazy."**
- **Where do web extensions fit into the fundamentals? (host asks Kiara).** Extensions
  are built from **JavaScript, CSS, HTML**, so the same skills apply, and you can **port
  extensions from other browsers** to Safari. Apple has built developer tools to help, and
  has worked in the **Web Extensions community group** to standardize extension APIs for a
  smoother cross-browser process.

### Web standards the team is most excited about

- Excitement named on-air (multi-voice, partly garbled in transcript): an **install
  element**, **declarative partial updates** in HTML and canvas (asker credited as
  "dfa BULIC" / "D Fabulic," **transcript unclear**); the newly founded **Web Extensions
  Working Group** (after 5 years of bi-weekly community-group meetings, now able to produce
  an actual spec and push interoperability); the **random item function** in the new CSS
  values spec (like the recently shipped CSS `random()` but able to pick between keyword
  values across properties, "opens up a lot of possibilities"); and **Spatial CSS**.
- **Spatial CSS, in detail.** Described as one of the most exciting things the spatial-web
  contributors are working on, "a spec," still a **draft**. From the draft you can already
  see **3D transforms on multiple objects in real 3D space**, on platforms like Apple
  Vision Pro and others, but also relevant to objects shown **on a phone or Mac**: almost
  every e-commerce product today appears in some **3D configurator**. The `<model>` element
  already displays such objects on visionOS/macOS/iOS 27, with **JavaScript polyfills** for
  other platforms; Spatial CSS would let you **combine, annotate, and tie them into the
  accessibility tree**. Key design point: it **extends naturally from what you already
  know**, where absolute positioning and anchor positioning gain a **depth** axis on top of
  top/left/right/bottom. **Anchoring** lets you combine multiple models into a single
  customizable object (called out as huge for e-commerce).
- **On the standards process itself.** The panel resisted romanticizing it: "a hard
  process," can "take years longer than you want." But it **goes slow on purpose**:
  "if it's hard to change, it's hard to change back." Maybe Grid lanes took ~3 years
  longer than hoped, "but will anyone care 40 years from now? No, they'll be happy it was
  the best-designed API it could be." "We're willing to go slow … if that's what it takes
  to end up with a result that's excellent." And: **"HTML is HTML is HTML."** There's no
  throwing away HTML5 to "replace it with HTML17."

### Standards advocacy & WebKit standards positions

- **How should web devs advocate for features, what shouldn't they do, and do WebKit
  standards positions factor in our radars? (asker "D Fabulic," transcript unclear).**
  Channels: **bugs.webkit.org** and the evangelists on social media. Because there are
  "so many fantastic ideas and not enough human beings on Earth to get them all done,"
  WebKit prioritizes like any team, driven by **use case and end-user benefit**: what
  you're trying to create, and how the missing API makes it hard or impossible.
- **What a standards position actually is.** Web standards is "a conversation between lots
  of different people"; many things get introduced or even implemented in one browser
  without consensus. WebKit's published positions (**on GitHub**) often say "we don't know
  about that" because an API is judged **too hard to use**, or not private/secure/performant
  enough, or bad for battery: it "needs to be designed better." Guiding principle: **"users
  first, developers second, ourselves third,"** echoing the **HTML5 priority of
  constituencies** (credited to "Anna and Machi" who "wrote that principle" and ran WebKit
  "back in the day," **names transcript-unclear**). Sometimes a feature would be great for a
  developer but **dangerous for users if misused**, so WebKit can't ship it: "if you ever
  click a link … what happens after that link is clicked is safe" is a top priority, along
  with privacy.
- **Positions aren't the end of the conversation.** Even with a standards position, that
  should **never stop developers/users from stating real needs**: the web is vast and
  there's "often a … webbier way" to meet a need. Filing requests is "absolutely vital."
- **The `:has()` parent-selector case study.** For "years and years," a parent selector
  was "never going to happen," too slow, "the chips aren't fast enough." Then **Igalia**
  ("Agalia," transcript unclear) argued **`:has()` could work**, which "triggered our
  engineers to look at it again." A "genius stroke" of doing it a different way, plus
  "the hardware's a lot faster now than 20 years ago," and **the WebKit team at Apple
  figured out how to make `:has()` fast.** "And now it's shipped in every browser."
  Lesson: **things change.**
- **What to do, concretely.** Be vocal; **build JS libraries, demos**, share them; make
  demos of your **actual use cases**, not just flashy ones that show off an API. It helps
  WebKit know whether it's "a couple people or a lot of people," and "one or two use cases
  or a lot." And: **be kind**, "these are humans … trying to figure out what's best."

### WKWebView, cookies (deferred)

- **Cookie synchronization in WKWebView like the old UIWebView behavior? (Harsha).** The
  back room suggested using **`WKHTTPCookieStore`** to observe and manipulate cookies for
  WKWebView. The panel had no cookie expert on stage and **routed this to the developer
  forums.**

### Web extensions

- **Closing the API gap with Chrome/Firefox, e.g. tab group APIs? (Jay Kingens).** Excited
  that users can now **create an extension from a prompt**. On parity: **file at
  bugs.webkit.org with the use case**, since there are "so many APIs" and limited
  time/people/resources, so **use case and user benefit** decide. Apple is also working in
  the community/working group to **standardize APIs and get the same behavior across
  platforms**. "If you see an API available in another browser but not Safari, be loud
  about it and let us know why."
- **An SEO extension that surfaces reports in a SwiftUI window across platforms (Dirkio).**
  Use an **extension pop-up** for in-extension UI; **web-navigation APIs** to monitor page
  changes; and **native messaging** to pass information between the app and the extension
  so it can be displayed in the native (SwiftUI) app and palette.
- **Permissions in Safari vs other browsers (is Safari more restrictive?) (audience).**
  Yes, by design: the model is **privacy-first**. Extensions can be powerful and touch
  sensitive browsing data, so **manifest-declared permissions aren't automatically
  granted**; the **user chooses which sites** an extension runs on. Developers often
  request **too much access** out of uncertainty; the fix is **active-tab permission**,
  where the extension is only active on the current tab and **loses host permissions once
  the user navigates.** Goal: users aware of, and in control of, what an extension accesses.
- **Porting an existing Chrome/Firefox extension (effort and gotchas?) (audience).**
  "It depends." Some extensions convert and **just work**. Workflow: in the **Develop
  menu**, **load a temporary extension** straight from your existing extension resources,
  test what works. Where an API **behaves differently** across browsers (or Safari aligns
  with Firefox but not Chrome), bring it to the community/working group or **file a bug
  (bugs.webkit.org / Feedback Assistant).** On manifests: **no grave differences**, and
  importantly Safari **does not fail on unsupported manifest keys**, a deliberate choice
  to ease migration.

### The `<select>` element (customizable & appearance-based)

- **Safest way to progressively enhance an existing select with appearance-based select
  (routed to Tim).** **Always keep textual content** in your options. It's tempting to go
  icon-only or color-swatch-only, but text content means a non-supporting browser shows
  the plain text instead of a **blank pop-over**, "that's the beauty of progressive
  enhancement." Text is also what **screen readers** read out, and a text label often
  improves **UX** where icons are ambiguous. Restated plainly: put all the text options in
  the traditional `<select>`, then **layer images / extra content / CSS on top**; supporting
  browsers get "all the fanciness," others "get the OG list of text," and the developer
  "can just be totally lazy."
- **Why reuse `<select>` rather than a new element.** The original proposal was a
  **`selectmenu`** element, but a standards engineer (credited "Anna," transcript-unclear)
  suggested **reusing `<select>`** so older browsers get a nice **fallback** for free. The
  panel framed semantic HTML as a **"secret superpower"** feeding accessibility trees,
  AI-readable content, and localization, plus **keyboard navigation and every input/output
  device (including Braille keyboards) for free.** Related: there's a **spec in progress
  (first draft by Tim)** for **appearance-based** styling of *all* HTML form controls,
  because needing controls to match a brand is a top reason people reach for divs/spans.
  It's a "30-year-old problem": giving developers **full control over styling real HTML
  form controls** while keeping them interactive.
- **Appearance-based select: what "just works" vs. what you must reset (the "spicy"
  question).** Out of the box you get **correct layout**, and the **font comes under your
  control** (unlike `appearance: none`, which forces a custom font). The standout for Tim:
  **addressable pseudo-elements**: style the **picker icon**, the **drop-down menu**, or
  the **checkmark** independently without touching the rest. Appearance-base (coming later
  for *all* form controls, **not shipping now**) delivers a **functional** control (unlike
  `appearance: none`) and is **100% interoperable** across browser engines, with a
  **consistent DOM** you can address because the under-the-hood HTML is the same everywhere.
  That consistency is described as the hardest and "most profound" part of the work. It uses
  **no layout magic / special hacks**: it lays out like normal CSS/HTML. You can use **Grid, Flexbox,
  or Grid lanes inside the picker** to lay out options ("wild," "incredible").
- **The on-air disagreement.** Tim and Jen flagged that they "don't agree completely yet"
  on the **default** appearance-base look: should it look **plain like the 1990s** (you
  style it up) or **modern by default** (less CSS to look good)? "What does modern even
  mean? … Is that a fashion trend that's going to change?" Either way, the intent is to
  **inherit as much as possible** from the page (background color, font, font color) and
  give the UA stylesheet **as little opinion as possible**, "that's how dialog works,
  that's how details works."

### The `<model>` element, 3D & loading

- **How does `<model>` handle loading states and errors, and what control do developers
  have during loading? (routed to Alexey).** A **`ready` promise** signals whether the
  model is loaded; combine it with your own **spinner** for a good experience, then reveal
  the model and use JS APIs like **entity transform**. Before it's loaded, "you can't do
  anything with it." Asked on-air for a **CSS pseudo-class for the loading state**, the
  answer was: **"that's great feedback"** (i.e., not available, now logged).
- **Bytes vs. loaded resource (added context).** Like images, there's a difference between
  **downloading the bytes** and the resource being **actually loaded and usable**: true
  for a JPEG/PNG and equally for a `<model>`. Even a **procedurally generated** model (built
  with a JS library, bytes already local) must still be **processed** into the usable model.
  These are distinct phases you can reason about and present.
- **How big are models on the web / should they be?** "Models can go crazy," but **treat
  them like images: keep them small and optimize** before publishing, because loading and
  processing time scale with size, and memory is finite. No hard guidelines, but people hit
  **maximum visual quality around ~10 MB**, larger than the same thing as an image, but
  reasonable given motion/animation. **Environments** (as in John's session) can be larger
  (more polygons/textures) because they stand in as a **full-screen experience**, much like
  a long/large video can dwarf its host page. Historical framing: images, then video, each
  used to mean "a lot of waiting"; designers/developers should again **think through the
  loading experience**, and tooling will keep improving.
- **Tooling / formats.** **USD Crush** (Alliance for OpenUSD command-line tool) used to
  reduce everything to **JPEGs**; now it compresses textures to **AVIF** ("much smaller").
  Apple is **in discussion with the Alliance for Open Media** about an appropriate **mesh
  compression format**; when that lands, it becomes "the kind of thing people will be able
  to do."

### Spatial web, immersive environments & WebGPU

- **With Spatial CSS, will I see web content in 3D on Vision Pro? (Paul Sheldon).** Spatial
  CSS is **a proposal** with **no specific shipping news**, but "positive" because it comes
  from Apple. What exists **today** is the **inline `<model>` element**: place **USD and
  other 3D file formats** in a page, **drag them out**, or (with immersive environments)
  view them **at full scale**; you can also **create things procedurally** (CSS-like). The
  long-term right answer is **Spatial CSS plus the full accessibility tree**, but nothing
  stops you from **experimenting today** in a way that "looks a lot like that."
- **How does the immersive API interact with existing page content / attention handoff?
  (audience).** In an immersive environment **the web page is still up**, so you keep full
  ability to act on it. It's a **single-app experience**: e.g. a **Messages** window on the
  side may **dim**, and a **crown press** restores the general shared view. If the page
  needs to **alter the environment** (reposition you, play/pause an animation), put that
  **UI on the web page** for the user.
- **Is `<model>` the thing that makes spatial relevant to everyday developers?
  (audience).** Yes: **`<model>` is the first and easiest step** into 3D/spatial web, lower
  friction than custom 3D. You don't even need an existing model: find one online or
  **generate one** (creative generation, or from a set of images). A natural progression
  follows toward tools like **Blender**, but `<model>` shows results fast.
- **Have we reached the endgame for web performance? (asker "Waggy," transcript unclear).**
  No. Performance has **many definitions**: JS speed, image load, time-to-usable, and
  crucially **battery** ("my battery is down to 15% on the train"). As the web gets **more
  powerful**, it must be made **more performant**, repeatedly. On 3D specifically: WebGL
  started ~**2010**; after a few years it became clear the real bottleneck across the
  industry differed from initial expectations, which is **why WebGPU is the successor**,
  matched to **Apple's (and other vendors') silicon**. WebKit engineers **can't regress
  benchmarks**, are "constantly looking for improvements," and find it "**very surprising
  what can or can't affect performance**"; when a ceiling is hit they often **make a new
  benchmark** for the web's evolving needs. The **WebKit PNP (Power and Performance) team**
  runs perf tests on **loading, rendering, and power for (close to) every commit**:
  "we're obsessed, which is why we have the fastest browser."

### WebKit / JavaScriptCore as OS infrastructure

- **Why can a JavaScript-driven web app feel more performant than native? (Josh D., a
  native macOS/iOS developer using agentic coding).** Early iOS once **planned to ship no
  native apps**: everything as web apps (per a book by an early Apple developer; the
  **Stocks app** and others were built that way). They learned **performant web apps are
  hard**, but that also means when web apps *are* performant they can be **as good as or
  better than native**. WebKit does a lot **under the hood** (content refreshes, etc.).
  Bigger point: **WebKit is deeply integrated into the OS.** **JavaScriptCore** "is a big
  chunk of the operating system"; any app anywhere using JS uses it. **WebKit is now a
  SwiftUI view**, so a SwiftUI app can drop into web technology mid-build. It runs even on
  **watchOS** (no browser, but WebKit and JavaScriptCore run), where performance on a small
  device matters. "Native vs. web" is a false sports rivalry: **users don't know or care**;
  people pick whatever tool fits. "A big thank you to JavaScriptCore."

### Closing round — one thing each panelist wants developers to know

- **The release notes are enormous.** Possibly the easiest thing to miss: the Safari 27
  beta release notes are about **twice the normal length**, and that's before adding
  26.2 / 26.4 / the rest of 26.x. It reflects the year's work **hunting down paper cuts**:
  "we're not done, nobody thinks we're done." And a direct message to frustrated developers
  who think Apple doesn't care: **"we absolutely care … why in the world would making the
  web worse in Safari be our goal?"** Concrete proof point repeated: they **rewrote the
  JavaScript module loader** to fix the **top-level `await` bug**.
- **Extensions live in WebKit now.** "A few years ago we moved all of our support for
  extensions into WebKit," so you can **contribute**: check out the code base, file a bug,
  help improve an API.
- **Play with `<model>`** ("as a father of three, I can't pick a favorite child"): see how
  it works across platforms and "enjoy 3D on the web."
- **See how little you have to do.** ~25 years a professional web developer, "mostly … not
  using any frameworks … couldn't be happier." Look at how much the platform now does for
  you and how little you may need to build.
- **Show off Safari-first tech.** A wish for more demos using **Safari-specific
  technologies** WebKit shipped first (CSS **random function**, **filter function**,
  **hanging punctuation**) that are web standards heading to other browsers. "We often
  invent new technology and ship things first, and people don't realize."

## Unconventional facts & takeaways (the live-Q&A gold)

- **The Safari 27 headline is a number, not a feature: 1,100+ fixes/improvements since
  last fall**, with beta release notes about **2× the usual length**.
- **The top-level `await` bug fix required rewriting the JavaScript module loader**: a
  concrete, specific quality win named on-air.
- **`:has()` history, candidly told:** Apple said "never" for years (parent selector "too
  slow," "chips aren't fast enough"); **Igalia** reopened it; **the WebKit team made it
  fast**, "and now it's shipped in every browser."
- **`<select>` was *almost* a new `selectmenu` element**; reusing `<select>` (an Anna
  suggestion, name transcript-unclear) was a deliberate **progressive-enhancement** choice.
- **You can put Grid / Flexbox / Grid lanes *inside* the `<select>` picker** to lay out
  options, repeatedly called "wild" and "incredible."
- **Appearance-based form-control styling is a deliberate "30-year-old problem" fix**, and
  it's **not shipping now** (the all-controls version is later; only customizable `<select>`
  is shipping). There's a **real, named on-air disagreement** about the default look
  (retro-plain vs. modern-by-default).
- **`<model>` sizing rule of thumb: ~10 MB for max visual quality**, bigger than the same
  thing as an image, smaller than an environment.
- **USD Crush switched texture compression from JPEG to AVIF**, and a **mesh-compression
  format is under discussion with the Alliance for Open Media** (no spec/format named).
- **There is no `<model>` CSS loading pseudo-class today**, only a `ready` promise;
  the panel explicitly took "we should add one" as feedback live.
- **WebGPU is the successor to WebGL** (WebGL started ~2010) because the real cross-industry
  GPU bottleneck differed from early expectations; performance "has no endgame."
- **JavaScriptCore runs on watchOS** even though there's no browser there; **WebKit is now a
  SwiftUI view**; early iOS once planned a **web-apps-only model** (Stocks among the apps
  built that way).
- **The WebKit PNP (Power and Performance) team runs perf tests per commit** across loading,
  rendering, and power: the basis for the "fastest browser" claim.
- **The Web Extensions Working Group is brand-new**, after ~5 years as a bi-weekly community
  group; Safari **doesn't fail on unsupported manifest keys** to ease porting.
- **Cultural color:** "be lazy" as the explicit "lesson of the group lab"; "users first,
  developers second, ourselves third" (HTML5 priority of constituencies); "HTML is HTML is
  HTML"; the modem-handshake nostalgia about image load times; the deliberate "go slow on
  purpose, because hard-to-change means hard-to-change-back" stance on standards.
