---
title: "Platforms State of the Union — Full Digest"
session: WWDC26 · 102
url: https://developer.apple.com/videos/play/wwdc2026/102/
duration: 61m
speakers: Josh, Richard, Mary Beth, Lori, Cindy, Franck, Holly, Ken, Kevin
sources: transcript.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 102 — Platforms State of the Union

## TL;DR

The annual developer keynote, organized around three pillars and demoed through an
**Origami** sample app:

1. **Apple Intelligence.** The Foundation Models framework gains **image input** and
   **server models** (Claude, Gemini, and more) behind a single `LanguageModel`
   protocol, plus a no-cloud-cost path to Apple Foundation Models on **Private Cloud
   Compute** for small developers (< 2M first-time downloads). New **Dynamic Profiles**
   give a declarative way to swap model/tools/instructions mid-session. The framework
   **goes open source later this summer** (same Swift APIs on your server). A
   brand-new **Core AI** framework runs your own custom models on-device. **App
   Intents** schemas + a new **View Annotations API** plug your app into Siri and the
   Spotlight semantic index.
2. **Platform improvements.** **Liquid Glass** design refinements (auto-applied on
   rebuild, with a user clear→tinted slider), **iOS app resizability**, a big
   **SwiftUI** year (interactions, ~2× faster layout, lazy `@State`, new document
   infrastructure, Spatial Preview), and **Swift 6.4** (`anyAppleOS`, better
   type-check diagnostics, async in `defer`). macOS is now **Apple-silicon-only**, and
   the **old design opt-out is being removed** — recompiling with Xcode 27 forces
   Liquid Glass.
3. **Developer productivity.** **Xcode 27** leans hard into **agentic coding** —
   `/plan` flows, agents that drive the simulator, localize, and fix crashes from the
   Organizer — grounded in Swift/SwiftUI and extended via **plugins/skills**, **MCP**,
   and the new **Agent Client Protocol** (bring any agent). A revamped daily
   experience: **Device Hub** (replaces Simulator), themes, iCloud settings sync,
   zero-setup new projects. Plus game tools (Reality Composer Pro 3, Game Porting
   Toolkit AI skills, Metal CLI for agents).

---

## 1. Apple Intelligence

### Foundation models, built with Google [2:23]

Apple Foundation Models — the models behind Apple Intelligence — were built **working
with Google and leveraging the technologies behind their Gemini family of models**,
then adapted to run **on device** and on **Private Cloud Compute**. Your apps reach the
same models through the **Foundation Models framework**, which this year expands to
**image input** and **server models**.

A key accessibility move: developers with **fewer than 2 million first-time App Store
downloads** can use Apple Foundation Models running on **Private Cloud Compute with no
cloud API cost** — "frontier-level intelligence with unparalleled privacy," so that
exploring ideas isn't gated by infrastructure cost. The framework now offers **a single
API that supports any model you need**.

System-side, **App Intents** connects your app to Apple Intelligence, drawing on:

- the **Spotlight semantic index** (organizes personal context from supported apps),
- the **app toolbox** (identifies features across apps to serve a request), and
- the **system orchestrator** (coordinates it all while protecting privacy).

### Foundation Models framework, hands-on (Richard & Mary Beth) [4:46]

The framework is a native Swift API to the same on-device model that powers Apple
Intelligence. Cited adopters: **Wayfair** (shopping), **CellWalk** (education),
**CricHeroes** (local sports) — all on-device, no infra cost, no privacy trade-off.

The demo vehicle is an **Origami** app: a gallery of origami projects tailored to a
person's interests/materials with step-by-step feedback, a built-in chat, and a feature
that mixes inspiration photos (e.g. a sheet of paper + a photo of a dog) to brainstorm a
project. This year's framework additions:

- **Multimodal prompts** — text + images; "as simple as attaching an image to your
  prompt." Opens new image-understanding app categories.
- **Vision framework integration** — purpose-built tools the model can call:
  **OCR** for precise text extraction and a **barcode reader**, all on-device.
- **Server models** — call larger models like **Claude, Gemini, and more** for complex
  workflows, keeping tool calling and guided generation. **Any provider can ship a
  Swift package conforming to the `LanguageModel` protocol**, and you pick the one you
  want; swapping models is easy.
- **PCC at no cloud cost** for getting-started developers (the < 2M-downloads tier),
  with end users getting daily access and **iCloud+ subscribers getting expanded
  access**.

Apple also ships a **new open-source Swift package** of pre-built tools for **skills**
and **context-management utilities**. Example: a task app like **Tiimo** pulls in a
skill that adapts tone/recommendations to the user's data for a personalized daily
brief. Because the space moves fast and "tomorrow's abstractions may be very different,"
those utilities are built on lower-level **Dynamic Profiles**.

### Dynamic Profiles [9:00]

New **declarative APIs** in the Foundation Models framework for adaptive AI experiences
with less code — orchestrate skills/sub-agents, swap tools, and update instructions on
the fly. As shown in the Origami app:

- Start from a `LanguageModelSession`, but instead of a fixed model/tools/instructions,
  you choose a **profile** and define profiles in a familiar **Swift result-builder
  body**.
- The body **always resolves to exactly one active `Profile`** driving the session at a
  time, but you can switch among as many profiles as the feature needs **within the
  same session**.
- Demo profiles: a **brainstorming** helper (PCC model, **temperature cranked up** for
  creativity), a **tutorial generation** profile (PCC again, **reasoning level set to
  deep** for the hardest task), and a **jargon-explainer** profile (on-device
  `SystemLanguageModel` to save server calls).
- **Everything shares the same continuous transcript** even as models are swapped →
  more contextual intelligence with less prompting.
- **Instructions and tools** can be swapped in/out too; the body **recomputes on every
  model turn**, so the session stays current (e.g. add feedback instructions/tools once
  the tutorial is generated).
- Mental model: the three Origami profiles "look a bit like three AI agents" — Dynamic
  Profiles are meant as **adaptable building blocks** for agents, skills, or any
  higher-level abstraction.

### Tooling + open source [11:52]

A complete build→test→ship set for intelligence features:

- **Evaluations framework** — test prompts and validate that intelligence-powered
  features work reliably.
- **Upgraded Foundation Models instrument** — visualize and debug model behavior.
- **`fm` command-line tool** — prompt the model from the terminal.
- Plus a **Python SDK**, **tool calling with images**, and a **RAG tool powered by Core
  Spotlight** that's **private to your app**.

And the big one: **later this summer the framework becomes open source**, so "the same
Swift APIs you use in your app can now run on your server too" — an end-to-end AI
workflow anywhere you deploy Swift.

### Core AI [13:08]

For bringing and running **your own custom models on-device**, a **brand-new framework
built into the platform**:

- **Memory-safe modern Swift API**, uncompromising performance, extensive tuning —
  fine-grained interest management, model specialization, **custom GPU kernels**.
- **Python-based tools** to convert/optimize **PyTorch** models for the Core AI runtime.
- A new **developer toolchain**: **ahead-of-time compilation**, dedicated **Core AI
  instruments**, and a **visual debugger that traces tensor values back to your
  original Python source**.
- **Scales with available compute** — a compact vision model on iPhone for real-time
  camera queries, up to a **multi-billion-parameter LLM** in a Mac app for an agentic
  assistant — all on-device, **zero server dependencies, zero token costs**.
- Optimized for Apple silicon; **powers Apple Intelligence across the system, including
  Siri**.

Guidance: use **Foundation Models framework** for general intelligence features,
**Core AI** when you're shipping a *specific custom model* on-device.

### App Intents & Siri (Lori) [14:38]

Apple Intelligence draws on personal context, understands what's on screen, and can take
actions — integrate via **App Intents**:

- **Schemas** are structures Siri understands deeply (built on years of LM training).
  **Entity schemas** describe your content/concepts; **intent schemas** describe
  actions. Schemas cover categories like task management, photo editing, communication,
  with system-supported actions, and **benefit from future Siri/language updates with no
  code changes**.
- Contribute content to the **Spotlight semantic index** for fast, attributed personal-
  context search — "more understanding from less code," with indexing keys built in.
- **Demo:** Origami entities conform to `IndexedEntity` (indexed into Spotlight on app
  launch) and to entity schemas via `@AppEntity` (Message, Contact, Conversation). Siri
  then answers "who's coming to origami night?" and "what's Richard bringing?" **even
  when you're not in the app**. Adding the `sendMessage` schema via `@AppIntent` makes
  it actionable ("text Richard, '…'").
- The new **View Annotations API** maps your views to entities (a view modifier mapping
  each message row to its `MessageEntity`), so users can reference on-screen content
  conversationally ("send this photo to Kevin and say …").

### Intelligent platform recap + MLX (Josh) [19:21]

Vision: rich native experiences + natural-language interfaces working together. Three
technologies: **Siri** (via App Intents), **Foundation Models framework**, and **Core
AI**. For experimenters/researchers and local inference servers, **MLX** — Apple's
array framework — is **open source and faster than ever**, now with **Metal 4**, **GPU
Neural Accelerators**, and scaling training **across multiple Macs via RDMA over
Thunderbolt**.

---

## 2. Platform improvements

### Foundations & rebuild wins [20:53]

Rebuilding with the new SDK makes apps **launch faster and feel more responsive**, with
refinements across media, search, accessibility, Swift, SwiftUI — "and a lot more,
especially around design."

### Liquid Glass design (Cindy) [22:26]

A year of refinement to last year's design language:

- **Readability** — tuned so Liquid Glass **more effectively diffuses complex content**
  behind it; a **darkened edge** plus **brighter specular highlights** add depth.
- **Personalization** — a **new Settings slider** from **ultra clear to fully tinted**.
- **Automatic** — apps already using Liquid Glass get these improvements on this year's
  releases **without recompiling**; it adapts to accessibility settings (reduce
  transparency, increase contrast); macOS 27 gains the **`show borders`** environment
  value (like iOS).
- **Structure** — sidebars expand to the edges on Mac/iPad and **regain accent-color
  icons**; List/Label APIs apply this automatically with per-item tint; every macOS
  window now has the **same tighter corner radius**; a **uniform toolbar** appears as
  content scrolls under floating bars (customizable via existing scroll-edge-effect
  APIs); an API to **show icons for key actions** on macOS/iPadOS.
- **Icons** — sharper, more defined Liquid Glass rendering for all app icons, with
  optional **refraction**; **Icon Composer** now designs icons from **multiple Liquid
  Glass layers** with annotation features and a preview of how the icon looks on earlier
  releases.

### App adaptability / resizability [24:59]

iOS apps appear in more places (iPad as iPhone app, Mac via iPhone Mirroring). This year
adds **resizable iOS apps** in iPhone Mirroring and on iPad:

- **Auto-opt-in on rebuild** with the latest SDK. SwiftUI apps already benefit from
  scene lifecycle + basic resizability; using SwiftUI/Auto Layout/size-class responses
  puts you most of the way there. Custom views should move to **Auto Layout + trait
  collections**.
- New **resizable iOS simulator** and **Previews** to test sizes/aspect ratios in Xcode,
  plus a **coding-agent skill** to find/fix common resizability issues.

### SwiftUI (Franck) [27:05]

Three themes: interactions, speed, capabilities.

**Interactions**
- **Reorderable containers** — `.reorderable()` on a `ForEach` + `.reorderContainer()`
  on the parent; SwiftUI handles lift/drop animations, works with any container (grids,
  stacks), not just lists.
- **Swipe actions anywhere** — `.swipeActions()` on a row + `.swipeActionsContainer()`
  on the scrollable container.
- **Text selection** — iOS gains full-fidelity selection (as in `TextField`/
  `TextEditor`); macOS adds custom text renderers, text vibrancy, vertical text.

**Speed (mostly free)**
- Ongoing **unification of SwiftUI/AppKit/UIKit** onto a common foundation across many
  controls (e.g. smoother large macOS menu pickers).
- Nested stack layouts **short-circuit redundant child measurements** → layouts resize
  **up to ~2× faster**.
- **`@State` is now lazy** (converted from a dynamic property to a **macro**) —
  state objects initialize only on first load, not on every reinitialization.
- **`AsyncImage`** now caches via **standard HTTP caching** — downloaded once, re-fetched
  only when needed.

**Capabilities**
- **Toolbars** — `visibilityPriority` keeps important items visible longer as space
  shrinks; a new **overflow menu container** groups less-prominent actions;
  **`topBarPinnedTrailing`** anchors items to the trailing edge regardless of reflow;
  new **prominent tab role** pins a tab to the trailing edge.
- **New document infrastructure** — first-class **file URL access** for custom
  read/write (read just the parts you need, write only what changed — the kind that
  powers Xcode/Pages), observable document configuration, deep modern-Swift integration
  (observation, concurrency).
- **Spatial Preview framework** — Mac apps can make a 3D model spatial when streaming to
  **Apple Vision Pro**, for real-time preview/edit/share.
- Also: better type-checking with content builders, a new **alert binding API**, and
  adjustable **cross-fade transitions**.

### Swift (Holly) [35:13]

Swift as the language for every layer — apps, internet-scale services, embedded
firmware — and beyond Apple platforms (Linux/Windows/Android/web via Swift.org).
Interop case studies: **Flighty** (shared Swift across app + backend), **GoodNotes**
(**Swift for WebAssembly**, 100k+ lines reused to web/Chrome OS/Android/Windows),
**Frameo** (**Swift-Java interop**). Inside Apple: Foundation, AppKit/UIKit moving to
Swift/SwiftUI under the hood; **WebKit** replacing C++ components via safe C++ interop;
the **QUIC** transport rewritten in Swift (open-sourced this month via SwiftNIO);
**TrueType** font engine replacing hand-optimized C; and **parts of the OS kernel now
written in Swift** for the 27 releases.

**Swift 6.4** highlights:
- **`anyAppleOS`** availability shorthand instead of listing every platform/version.
- **Scoped warning suppression** and **promote-warnings-to-errors** for migrations.
- **`async` calls allowed inside `defer`**.
- Big reduction in **"unable to type-check this expression in reasonable time"**
  failures — many now compile or give an actionable error.

### Transitions (Josh) [40:28]

- **Apple silicon only** — macOS Tahoe was the last Intel release; you can now ship
  **Apple-silicon-only Mac App Store binaries** (smaller downloads, single-arch
  testing).
- **Old design opt-out removed** — recompiling with **Xcode 27** automatically adopts
  the new Liquid Glass design.

---

## 3. Developer productivity

### Faster cadence + agents in Xcode [41:29]

AI coding assistance landed last year and was widely embraced; release cadence picked
up. Earlier this year Xcode got **coding agents** plus tools (grab a preview, search
docs, build/test) powered by **MCP**, which also connects Xcode to **Figma**, **GitHub**,
etc. Xcode has built-in agents from **Anthropic, OpenAI, and now Google**, and adds
support for the **Agent Client Protocol (ACP)** to bring any compatible agent.
**ACP + Gemini ship in an Xcode 26 update available today**; more in Xcode 27.

### Xcode experience (Ken) [42:48]

Two big stories: intelligence and the daily experience.

- **Quality** — faster project loading, fixed top crashes/spins, more reliable debug
  (faster expression evaluation, console handles heavier logging). **Xcode 27 is 30%
  smaller**, **Apple-silicon-only**, with agents/docs/components downloading in the
  background.
- **Settings sync** — Xcode settings (and Git config) **sync via iCloud**; import onto a
  new Mac with your Apple ID.
- **Zero-setup new project** — pick app and you're in the editor; no filename/bundle ID
  required up front. Good for exploring an idea/API/prototype.
- **Customization** — rearrange the toolbar; activity view tucked into the document
  title; **themes** with color flowing through the whole app (background, syntax,
  keyword tints), light/dark per theme, and **per-project themes**.
- **Xcode Cloud** — set up from inside Xcode (grant repo access, no App Store Connect
  setup); builds **up to 2× faster**, now supporting **Apple Vision Pro** and **Metal on
  Apple silicon**.
- **Previews** — show **variations for any property** (e.g. pass a four-value
  `CraftState` enum → grid of all states at once).
- **Device Hub** — **replaces Simulator** and does more: rotate/screenshot/home as
  before, but extend the view to change device properties (dark mode, font size),
  high-fidelity pinch-zoom/two-finger-scroll/dynamic resize, and **manage + interact
  with physical devices** from the same place (launch the app on a real iPhone from the
  Mac).

### Agentic coding (Kevin) [49:08]

Agents are woven into every layer of Xcode 27 and, crucially, **grounded in Swift,
SwiftUI, and Apple frameworks** — "when building for Apple platforms, Xcode is the best
place to code with agents." Tools include understanding the project, searching docs,
building, testing, and new ones: **rendering Previews with variants**, **interacting with
the simulator**, **localizing**, **debugging**. Demoed across the lifecycle:

- **Plan first** — start a conversation (opens in the editor like a file), describe the
  feature, add **`/plan`** (and ask for a diagram). The agent explores the project,
  asks clarifying questions, and produces a reviewable, refinable markdown plan.
- **Implement** — Xcode + agent implement the plan, showing code + Previews changing
  live; refine as it runs (e.g. add an image filter), then run the result.
- **Validate** — agents run **tests**, try ideas in **playgrounds**, check **Previews**
  (light/dark, orientations, text sizes, localizations), and now **interact with the app
  in the simulator** (tap/swipe/type), returning a test summary + screenshots.
- **Improve** — **localization** (add French → strings catalog + context-aware
  translation across the project), and **crash fixing** via the **Organizer** (pull top
  crashes, the agent reads the symbolicated log, locates + reproduces + fixes +
  validates).
- **Specialists / plugins** — Xcode 27 ships Apple's engineering expertise as a corpus
  of **skills, documentation, and MCP tools** ("specialists" for SwiftUI, accessibility,
  universal sizing, testing, performance). **Bring your own** via **plugins** (a widely
  adopted format): skills are just **markdown files**, tools use **MCP**, and a plugin
  can now **bring an agent via ACP**. Install via CLI or paste a git URL; Figma/GitHub
  offer one-click setup. End-to-end example: implement a Figma design in SwiftUI, refine
  variants, make it resizable with a skill, and post a PR to GitHub.

### Other tools & games (Josh) [57:52]

- **Reality Composer Pro 3** — rebuilt for production-ready 3D with **RealityKit**:
  character animations, more realistic lighting, **live previews via Mac Virtual
  Display**.
- **Game Porting Toolkit** — major update that adds **AI skills for coding agents** to
  cut porting time.
- **Metal command-line tools** — give agents direct control during development and
  debugging.

### Wrap [58:46]

Recap of the three pillars. **100+ sessions** on the Apple Developer app, website,
YouTube, and — new this year — **Bilibili**. Group Labs, online panels/Q&A, Developer
Forums, and Developer Centers (Cupertino, Shanghai, Singapore, Bengaluru, and a fifth
opening in **Berlin** this fall).
