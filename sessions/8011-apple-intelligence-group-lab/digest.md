---
title: "Apple Intelligence Group Lab — Full Digest"
session: WWDC26 · 8011
url: https://developer.apple.com/videos/play/wwdc2026/8011/
duration: 01:03:49
sources: transcript.md (whisper.cpp ggml-large-v3, SD video audio), meta.md
compiled: 2026-06-13
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and host routing. Usernames are read aloud by the host and may be mistranscribed. Session numbers were never read on-air; "Related sessions" are matched by topic. API names are normalized where obvious and flagged where uncertain.
---

# WWDC26 · 8011 — Apple Intelligence Group Lab

> A 64-minute live developer Q&A with a host plus five-engineer panel spanning App
> Intents/Siri, Foundation Models, and Evaluations. Not a feature talk: it's the
> unscripted layer under the WWDC26 Apple Intelligence sessions. The dominant thread is
> **App Schemas** for the new "Siri AI," with a strong second thread on the **Foundation
> Models framework** (the on-device model, the new Private Cloud Compute server model,
> the language model protocol, dynamic profiles) and the **Evaluations framework**. The
> recurring ask: adopt schemas, lean on the recorded sessions, and file Feedback for
> enhancements.

## TL;DR

- **App Schemas are the way into the new Siri AI, and you mix and match them.** Schemas
  are Apple-designed, fine-tuned shapes for actions and content. You pick the ones that
  match your app, combine schemas from different domains, and fill gaps with custom App
  Intents and App Shortcuts. **You must adopt a schema to integrate with the new Siri AI
  integration.** Adopting a schema also lets you **delete code**, because Apple supplies
  the sample phrases, the Spotlight indexing keys, and (the panel strongly hinted)
  localization.
- **There is now a system orchestrator, but no third-party app drives another's intents
  directly.** Cross-app action routes through the system. Cross-app *data* sharing uses
  the new **Transferable** API (bidirectional import/export, highest-to-lowest fidelity).
  Semantic-index retrieval is **sandboxed to the donor app**, even for two apps from the
  same developer in the same app group.
- **Two Foundation Models, two context windows.** On-device system language model = **4K
  context**, **no rate limiting in the foreground**, background throttling under heavy
  load (catch the rate-limited error and retry). New **Private Cloud Compute server
  model = 32K context**, with **quota signals and a quota-exhaustion error** you handle.
  An image costs **about 200 tokens** against the 4K budget; the rest is yours for
  prompt/instructions.
- **The framework is extensible and open source this year.** The **language model
  protocol** lets you bring any model (including web models via Core ML) and declare
  capabilities like **vision**. **Custom segments** let you support modalities the
  framework doesn't know about (video, "or it can smell something"). For video today, the
  pragmatic path is a **tool that transcribes/describes the video** (Vision framework,
  audio) and feeds text to the on-device model.
- **Image Playground does photorealism now, on Private Cloud Compute.** Use
  `imagePlaygroundStyle.all`. Because it runs on PCC, it **requires an internet
  connection and does not fall back to an on-device model.**
- **Evaluations is for any stochastic system, not just LLMs.** Model-judge evaluators,
  safety rubrics, a **tool-call evaluator** (did the right tools fire, in the right order,
  with the right values?), **synthetic data**, **hill climbing**, Xcode + Instruments
  visualizations, exportable JSON reports. It even fits classical ML and academic
  benchmarking.

## Panel

Roles are from the opening self-introductions. With no diarization, later first names
are attributed by host hand-offs; surnames are unreliable.

- **Ernie Sveala** — host; AI/machine learning evangelist at Apple. (Surname is whisper's guess.)
- **Matt** — Foundation Models framework.
- **James** — App Intents.
- **Rob / Robert** — Evaluations framework.
- **Louis** — Foundation Models framework.
- **Dan** — Siri and App Intents.

The host repeats two standing pointers throughout: code-specific issues belong on the
**developer forums**, and bugs / feature requests belong in **Feedback Assistant**
(feedbackassistant.apple.com). A running gag has the panel insisting their software has
"no bugs."

## Developer Q&A

Grouped by theme. Askers are the usernames the host reads aloud (often with apologies for
pronunciation); treat them as approximate.

### App Schemas, custom intents, and the new Siri

- **Learning resources for the iOS/"version 27" APIs (Mario Gravina).** WWDC videos, the
  dedicated **sample section** of the documentation page, technical articles, and a big
  documentation push this year for both App Intents and Foundation Models. The docs are
  framed as "not just here is the API, but how to use it and how it benefits your users."
  App Intents had **five sessions** this year, several built around sample apps you can
  inspect.
- **My time-tracking app doesn't fit any predefined schema (Luke's Sape).** New this year:
  **App Schemas**, Apple-designed and fine-tuned. Pick and choose the schemas that match
  your app (there are timer and calendar-event schemas). Anything outside the schemas, add
  as **custom App Intents**, which then surface in Shortcuts, Spotlight, and more.
  **Adopting a schema is required for the new Siri AI integration.** For broad cases there
  is a **system** domain: **system.search** deep-links Siri into your app's search (e-commerce,
  food ordering: "order me a bicycle on my favorite marketplace app"), and **system.open**
  opens a donated Spotlight entity directly in your app. If nothing fits, fall back to
  **App Shortcuts** (phrase-based; also works via Apple Pencil tap on iPad). (Dan, James)
- **My core entities don't map to any schema domain, what do I do for agentic Siri now?
  (Quasitoic, transcript unclear).** Largely the previous answer. Even if one intent in a
  domain doesn't fit, conforming an entity to a schema and donating to Spotlight still
  earns benefits: Siri AI can answer questions about the content or act on it via the
  **view annotation API**. (James)
- **Can my app act as its own orchestrator and drive other apps' intents directly?
  (Indigo J).** No direct third-party-to-third-party invocation. New this year is a
  **system orchestrator** that takes actions from App Intents across apps. Indexing content
  via the **semantic index / Spotlight** exposes it **to the system alone**, sandboxed, not
  to other apps. For deliberate cross-app data flow there's **Transferable**: a
  bidirectional API where you choose what to export and what to import, keeping ownership
  boundaries clear. (Dan)
- **Can a third-party app take part in a multi-turn Siri conversation, or am I limited to
  discrete actions/entities? (Taner NDT).** Yes, multi-turn is what schemas provide: take
  actions, ask questions about your entities, do follow-ups. At the heart is a **large
  language model** processing the input, so users phrase requests naturally and the model
  picks the right action, rather than matching rigid command shapes. (Dan, then panel)
- **Can I mix non-schema entities/intents with schemas from different domains in one app?
  (Zulfi Shah).** "Absolutely yes." Pick schemas from any domains (calendar plus reminders,
  for example); they compose. It is **not** schemas-vs-shortcuts-vs-in-app-search; they
  play together. Expect apps to use bits of schema plus bits of App Shortcuts. (Dan, James)
- **Do schemas give intents an "advantage" with Siri, or can any entity/intent be
  discovered the same way? (Notably no finance schema.) (Kyle BSHR).** "Advantage" is the
  wrong frame. **Schemas are how Siri AI reasons about your actions and entities.** The
  related question, how to *influence* Siri, has new APIs: a **new intent-donation system**
  builds a temporary transcript as the user taps around, so Siri learns that you message a
  given person in a specific app and won't default to iMessage. With schemas, Apple
  supplies the trained sample phrases, so messaging feels consistent across first- and
  third-party apps that adopt the same schema. (James, Dan)
- **My entities partly match a schema (contacts → `messages.person`). Acceptable for new
  Siri? (Zulfi Shah, again).** Yes. Pick the schema functionality that fits (there is a
  message-person schema; the **Unicorn Chat** sample uses it). If nothing fits, use the
  generic **system.search**, or **App Shortcuts** (where you must supply sample phrases).
  With schemas, Apple does that phrase work for you. (Dan)
- **Why hard-coded schemas instead of a dynamic Markdown/skills approach like "GPT or
  Claude" take? (Gerald-A.)** (whisper rendered the rival as "Cloud"; normalized to Claude
  given the GPT pairing.) The goal is **consistency and privacy**: schemas guarantee a
  standardized, holistic experience and carry **built-in safety**. Schemas also enable the
  **entity ownership API**: a calendar event that's yours might just get deleted, but a
  shared one prompts a confirmation; sending money confirms first. These edge cases are
  handled because the system can reason about a known schema. The panel also tied schemas
  to **localization** scaling: Apple does the model training and natural-language strings,
  so adoption scales across locales. (Dan, James)

### Foundation Models: budgets, errors, modalities, context

- **Practical time/work budget for Foundation Models during one background wake; how to
  chunk safely? (abhi, transcript unclear).** Foreground: **no rate limiting, unlimited
  requests.** Background: the system **throttles under heavy load**; even though the
  request ran on device, you can get a **rate-limited error** signaling the system was
  busy. Catch it and retry later. (Matt or Louis — unattributed)
- **Which error types signal Foundation Model throttling (system busy, thermal, quota),
  and which are retriable vs terminal? (abhi, again).** This year's **language model
  protocol** brings a common **language model error** covering rate limiting, refusals,
  and similar across models. The **PCC model** specifically adds **quota usage** (a property
  telling you whether you're near the limit) and a **quota-exhaustion error** thrown during
  a prompt that you handle. (Matt)
- **How to avoid `LanguageModelSession.GenerationError.guardrailViolation` on prompts that
  look fine? (Thub, transcript unclear).** The panel hit this building their own samples.
  Use the **Evaluations** model-judge evaluator with a **safety rubric** (for example a 1-to-4
  scale); you can even use the **larger PCC model** to evaluate why an on-device prompt is
  tripping the guardrail. The guardrail exists to keep unsafe responses out of your app.
  (Rob, Ernie)
- **Can Foundation Models reason over video, or is multimodal input limited to still
  images? (Taner NDT).** Models declare **capabilities** through the language model
  protocol; **vision** is one. Image input is supported this year on the **updated
  on-device model** and the **new PCC server model**, passed as a prompt **attachment**.
  The PCC model also supports **reasoning levels: light, moderate, deep.** Video isn't a
  built-in modality, but **custom segments** let you extend the protocol to arbitrary
  modalities. The practical recommendation: build a **tool** that gets a transcript or
  description of the video (Vision framework for frames, audio for sound) and feed that
  text to the on-device model, which is great at summarization. A panelist plugged
  **"what's new in image understanding with the Vision framework."** (Matt, Louis, then
  James) Tie-in: the **Evaluations tool-call evaluator** can tell you which tool fired when.
- **Semantic entropy when passing context between on-device and server models?
  (ProtonStir).** Framed as a Foundation Models question. **On-device = 4K context, PCC =
  32K.** **Dynamic profiles** let you switch profiles within the same transcript (one for
  accounting, one for health: "it's like agents"). To manage context across a switch, there
  are **declarative modifiers** like a **history transform** (drop tool calls, rebalance),
  plus a new **Foundation Models utilities package** with summarization/compaction for
  shrinking a transcript before moving to a smaller window. Pair with Evaluations to see
  what works. (Matt)
- **Token budget once you add image input; constraints on prompt/instructions? (R-Grad
  Harkershran, transcript unclear).** On-device stays **4,096 tokens**. An **image costs
  about 200 tokens** (measurable in Instruments); the rest of the 4K is fully available for
  instructions, and a 4,000-token input with a tiny output works as well as the reverse.
  **PCC = 32K.** **26.4 shipped token-counting APIs** to experiment with. Guidance: **hill
  climb** with Evaluations, be **precise with instructions**, try **synonyms**, and **put
  examples in the prompt.** The on-device model is more capable than its size suggests if
  you massage the prompt. (Louis, Matt)

### Evaluations framework

- **Can the framework be used for academic research, not just product eval? (EasyFranca2015,
  who notes Ernie may have planted the question).** Yes. It's built to evaluate **any
  stochastic system**, including classical ML like linear regression or a classifier. The
  flow: iterate over input **samples**, feed each into the function generating the model's
  response, then run **evaluators** that compute measurements. If your metric is
  scenario-based and computable in an evaluator, research or product, it fits. The framing:
  model providers publish benchmarks; this lets **you build your own app- or
  scenario-specific benchmark.** Plugs: **three sessions** (intro, advanced, plus
  **synthetic data** and **hill climbing**), Xcode integration with visuals, and exportable
  **JSON** reports for downstream workflows. (Rob, Matt)
- **Can Evaluations show whether my tools are being called? (Nia Murphy).** Yes. Supply the
  **transcript** from your configured language model session and use the **tool-call
  evaluator**. An **expectation/assertion system** lets you assert tools were called, in the
  right order, with the right properties and values; the evaluator reads the transcript and
  reports. **Instruments** is also wired up for real-time tool-call visibility. The panel
  noted authentication ordering ("call this tool before that one") as a common failure the
  model won't infer on its own. (Rob)
- **Do I need Evaluations to use Foundation Models? (Freescott).** No. You can evaluate any
  stochastic system without Foundation Models. The exception is the **tool-call evaluator**,
  which needs the Foundation Models transcript today; Apple is working on a **generic
  transform** so other model providers get the same treatment. (Rob)

### Indexed entities, Spotlight, and Transferable

- **Indexed Entity vs schema-defined entities — different purposes? (Louis42).** Different
  and complementary. **The schema defines the shape** (a book has content, title, author).
  **Indexed Entity** is a separate API that **indexes that content into the semantic index**
  so Siri AI can retrieve it ("read a book by a certain author"). Conform an entity to a
  schema **and** to Indexed Entity, then index at runtime, for the best Siri AI experience.
  Indexed Entity also works for **custom** entities and donating to **Spotlight** (results
  show up, and **system.open** deep-links from a Spotlight tap). With schemas, Apple
  pre-defines the **Spotlight indexing keys** (title → display name), so you provide
  property values and skip the key/value guesswork. (Dan, James)
- **Can one of my apps' Foundation Model sessions retrieve another of my apps' donated
  content via Spotlight (same developer, shared app group)? (Drobodin, transcript
  unclear).** **No, retrieval is limited to the donor app's sandbox.** Same-developer apps
  in a shared **app group** can share files/data through that mechanism, but not through the
  semantic-index sandbox. The sanctioned cross-app path is **Transferable**, which moves
  data **highest-to-lowest fidelity** and has you **opt in** to exactly what's shared (a
  small projection, not the whole app). (Dan)
- **Are schema-bound entities interchangeable — can a file entity become a photo entity and
  back? (John / Jay).** Yes, via **Transferable**: specify the data transformations your app
  supports, even dynamically by payload type. **Unicorn Chat** (downloadable today) adopts
  Transferable so a message can export as text, file, or photo; an exported photo can then
  import into Photos or any third-party photo app that adopts the photo schema. There's also
  a **file entity protocol** for all app entities when you need file-based formats. (Dan, James)

### Image Playground

- **Can I generate photorealistic images in my app with Image Playground? (Interferon).**
  Yes. Use **`imagePlaygroundStyle.all`**. It runs on **Private Cloud Compute** now, so you
  get the more powerful model. (Matt, Ernie)
- **Does Image Playground now require internet for PCC, or fall back gracefully? (Apple Gex,
  transcript unclear).** It **always requires an internet connection** to generate images
  because it uses the PCC server model, and **it will not fall back** to on-device. (Matt)

### Siri, on-screen context, and devices

- **Can Siri understand on-screen context on CarPlay, and act like "play song on row two,
  column one"? (Li Shuang Quan, transcript unclear).** On-screen context is **one of the
  three pillars** of the new Siri AI: Siri can read what's visible and act on it.
  Integrations: the long-standing **NSUserActivity** API, plus a new **ViewAnnotations API**
  to annotate on-screen content with your entity-conforming schemas. For **CarPlay**:
  **no** today, by design — the panel wants drivers focused on the road, not the screen —
  but file a Feedback enhancement request. (Dan)
- **Can we use App Intents with HomePod (no HomePod beta today)? (Chaz K).** The **new Siri
  AI is on iPhone, iPad, Mac, and visionOS — not HomePod.** But existing **App Shortcuts
  have worked on HomePod for a while**, so you can still reach your app there. (Dan)
- **Will watchOS App Intent responses match iOS one-to-one? (A. Harris-Crown).** Test across
  devices. The new Siri AI spans many platforms; remember **AirPods** too. App Intents can
  optionally provide **dialogue**, and that dialogue changes by **full vs supporting mode**
  (more verbose with no screen on AirPods, shorter when an iPad snippet is visible). The new
  **AppIntentsTesting** framework helps verify all of this. (Dan, James)

## "What are you most excited about?" round (39:03)

Asked by **MCRich23**. Highlights:

- **Phone-call intelligence** that surfaces, say, a flight confirmation number mid-call
  while you'd otherwise be digging through Mail.
- **App Intents Testing (AppIntentsTesting)** — unit-test App Intents in **CI pipelines**,
  plus UI and Spotlight integration tests. Tied to a session by **Venkatesh** (the
  "spacesuit" demo).
- **The Foundation Models extensibility / open source** story, and being able to **bring any
  web model in via Core ML** while still using Foundation Models as the central API.
- **Private Cloud Compute access for developers** — repeatedly called the thing developers
  asked for "since last year." Pitched as **one line of code, no API keys, no
  authentication** to connect to a server model. (A panelist mock-tossed a prop "book" to
  dramatize the simplicity, to staff alarm offstage: "No books were harmed.")
- **The new Siri AI ecosystem** — apps deeply integrated via the semantic index and intent
  schemas, Siri answering natural questions and taking actions across domains ("bicycle for
  the mind").

## Unconventional facts & takeaways (the live-Q&A gold)

- **You must adopt an App Schema to integrate with the new Siri AI.** App Shortcuts and
  custom intents still surface in Shortcuts/Spotlight, but schema adoption is the gate for
  the redesigned Siri AI integration.
- **Hard numbers that aren't on a slide:** on-device context **4K (4,096 tokens)**, PCC
  context **32K**; an **image ≈ 200 tokens** against the on-device budget; token-counting
  APIs shipped in **26.4**.
- **No rate limiting on-device in the foreground.** Throttling only kicks in for
  **background** work under heavy system load, surfaced as a retriable rate-limited error.
- **Image Playground is now PCC-only and online-only for photorealism** (`imagePlaygroundStyle.all`),
  with **no on-device fallback** — a real behavior change to plan around.
- **Semantic-index retrieval is strictly sandboxed to the donor app**, even for two apps
  from the same developer in the same app group. Cross-app sharing is an explicit opt-in via
  **Transferable**.
- **No third-party app drives another app's intents directly.** There's a **system
  orchestrator**, but third parties don't orchestrate each other.
- **PCC connection is pitched as "one line of code, no API keys, no auth"** — the headline
  developer-experience claim for the server model.
- **The Foundation Models framework is open source this year**, and the **language model
  protocol + custom segments** let you bring arbitrary models and modalities (the panel
  joked even a model that "can smell something").
- **CarPlay on-screen context is intentionally absent** for driver-safety reasons, not an
  oversight — file an enhancement request if you want it.
- **The rival-design framing:** Apple chose **fine-tuned schemas over a dynamic
  Markdown/skills approach** (the asker referenced "GPT or Claude") for consistency,
  privacy, built-in safety confirmations (entity ownership: shared vs personal), and
  scalable localization.
- **Evaluations is broader than LLMs** — it's positioned for any stochastic system,
  including classical ML and academic benchmarking, with synthetic data and hill climbing.
- **Cultural color:** the panel's running "we have no bugs" bit (walked back to "we have
  bugs" by the end); the book-toss stunt for PCC simplicity; repeated nudges to watch the
  recorded sessions and file Feedback; affectionate plugs for colleagues **Antonio**
  (advanced App Intents / dialogue / entity ownership) and **Venkatesh** (App Intents
  testing). Space-themed sample apps recur, with **Unicorn Chat** as the workhorse sample.
