---
title: "Coding Intelligence, Machine Learning & AI Group Lab — Full Digest"
session: WWDC26 · 8121
url: https://developer.apple.com/videos/play/wwdc2026/8121/
duration: 01:02:21
sources: transcript.md (whisper.cpp ggml-large-v3), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and host routing. whisper inserted hallucinated speaker labels ("FRANCESC CAMPOY," "MARK MANDEL," "FILIP HRACEK," "FELIPE HOFFA") that are NOT panelists; such lines are attributed by context or left unattributed. Garbled API/model names are flagged "(transcript unclear)".
---

# WWDC26 · 8121 — Coding Intelligence, Machine Learning & AI Group Lab

> A ~62-minute live developer Q&A with a six-person panel spanning Xcode, Foundation
> Models, the evaluations framework, Core AI, and MLX. It maps how the WWDC26 AI
> sessions fit together. The lab keeps returning to one recommendation: start at the
> top of the stack with Foundation Models and the on-device system model, use the
> **evaluations framework** to prove whether it covers your case, and only drop down to
> Core AI / MLX or out to PCC / a third-party model when you actually need to.

## TL;DR

- **The stack, top to bottom:** Foundation Models → Core AI (custom or downloaded models, with SLAs/guarantees) → MLX (research, distributed, on-device training). **Core ML is now "traditional ML" only** (decision trees and the like); everything new involving neural networks should move to **Core AI**.
- **On-device context window is 4096 tokens, shared between input and output, unchanged from last year.** Send 4,000 in and you have ~96 left for the response. **PCC is 32K** (also shared). Plug in MLX / Core AI / a server package to reach up to ~1M.
- **Bring-your-own-LLM is the headline.** The **language model protocol** lets any backend plug into Foundation Models. A **Google package went live**, and **Anthropic released theirs the morning of the lab**. Both those packages are open source, and **the Foundation Models framework itself is going open source "very soon."**
- **Dynamic Profiles** is a new declarative routing API for mixing system model + PCC + third-party model in one agentic flow. Two named handoff patterns: **baton pass** (full context shared, not private) and **phone a friend** (ephemeral, tool-call style, preserves privacy and buys an extra context window).
- **Xcode 27 adds ACP (Agent Client Protocol)** so you can plug local providers (LM Studio, Ollama) in as full agents, plus **agent control of the simulator** (tap/swipe/type, read back accessibility tree + screenshots) for UI testing.
- **`response.usage`** now reports input/output/cached/reasoning token counts (a heavily requested feature). New open-source **foundation-models-utilities** repo ships building blocks like a summarize-history modifier.
- **Models can't be shared across apps on iPhone** (sandboxing/security, plus use-case-specific quantization). You can share in-memory resources via Core AI model caching within an **app group**, and share weight files on disk only between apps from the **same developer**.

## Panel

Host **Shashank** ("Shank," evangelism) with **Kevin** (Xcode), **Eric** (Foundation Models framework), **Steven** (evaluations framework), **Raziel** (Core AI / Core ML), and **Angelos** (MLX; whisper also renders the name as "Angelo"/"Andres"). A larger team triaged questions off-camera. The four ALL-CAPS "names" scattered through the raw transcript are whisper artifacts, not people.

## Warm-up: one thing each panelist is most excited about (00:46–03:28)

- **Kevin (Xcode):** getting to build software he couldn't build before. Agents let him adopt new technologies efficiently and learn as he goes.
- **Eric (Foundation Models):** the **language model protocol** that plugs different inference backends into the framework. Call out to MLX, Core AI, the just-live Google package, the Anthropic package released that morning, "and developers like you guys are going to be able to add your own integrations."
- **Steven (evaluations):** the whole concept of evaluations, and specifically how easy the **model-judge evaluator** makes it to rate the quality of LLM responses.
- **Raziel (Core AI):** "selfishly," the launch of Core AI itself: he got into building AI frameworks to bring research to production, "and I think this is the best one so far." It powers Apple's own features (Foundation Models, Siri).
- **Angelos (MLX):** "the year of local AI." Local AI has crossed from gimmick to genuinely useful: local agents, real work, an FM chat app driving other things on-device. And, always, MLX.

## Developer Q&A

### Framework map: Core AI vs Core ML vs MLX vs Foundation Models

**Jane Chow** asked the panel to explain Core AI, Core ML, and MLX in simple terms, and how a beginner should decide which to learn.

- **Raziel** gave the layered overview. Apple offers a comprehensive suite you can approach at different levels. **Start at the top with Foundation Models** if an LLM (theirs or one you plug in) serves your case. For something more custom (a model you train or download from a repository) try **Core AI**: it comes with SLAs and guarantees, especially for building an app. Below that is **MLX**, "extremely successful," powering things like Angelos's multi-machine distributed demos.
- On **Core ML specifically:** "moving forward, we're asking everybody that work with neural networks to use Core AI." Core ML stays, but stays focused on **traditional ML, decision trees, that type of stuff**; everything new should move to Core AI.
- **Eric** added the LLM-specific path: start with Foundation Models and the **system language model**; use **evaluations** to confirm it gets you there; if not, you have **PCC**; if you need something custom or open-source, plug it in through Core AI behind Foundation Models so you keep the same API ("a one- and two-line switch"). Non-language work (diffusion, image segmentation) → drop to Core AI; if needed, down to MLX.
- **Angelos** noted that **on-device training** is "way easier in MLX or maybe unique to MLX this year," which is a reason to use the **MLX adapter for Foundation Models**.
- The recurring why: "Why go to a remote model if the on-device model already covers your use case?" Use evaluations to know.

### On-device context window (and PCC)

**Abhi27** asked the on-device model's context window in iOS 27, and whether input and output share one token budget.

- **Eric:** context size is **the same as before, 4096, and it is a shared window**. Put in 4,000 tokens and the response can use the remaining ~96. **PCC is 32K**, also a shared budget. For huge contexts, plug in MLX / Core AI or a server language-model package to **go up to a million tokens**.
- The MLX and Core AI teams ship a **Swift package that lets you use Foundation Models by just switching the model**: bundle your own model asset with the app and inherit that model's context window. (This is the language-model-protocol payoff Eric keeps citing.)
- Plug: both those packages are open source, and **the Foundation Models framework will be open source "very soon"** — good source to learn from.

### Background execution

**Abhi27** also asked whether Foundation Models can run inside a background app-refresh / background-processing task, including while the phone is locked, asleep, or long-backgrounded.

- **Yes, it can run in a background task.** But if the OS is busy, **it may rate-limit you**: you can catch a **rate-limited error** from the system language model meaning "done for a little bit; wait and retry."
- On **macOS you're fine as long as you're in the foreground** (no rate limiting on the local foundation model there).
- With the **PCC** language model there are **two distinct rate-limit causes**: system-busy, and too-many-requests-in-a-short-period. They surface as **different error kinds** in the API, so you can tell them apart and show the right message.
- Quality is unaffected by rate limiting: **same response in foreground and background**, it just might take longer.

### Apple Intelligence waitlist / AFM (deferred)

**Dessa** asked what "waitlist" means for macOS 27 Apple Intelligence when local and PCC models already work, whether the waitlist serves different models, and whether the beta includes "AFM core advanced 20 billion."

- A panelist (closest to Apple Intelligence, but not on Siri) gave the answer while plugging the dedicated **Apple Intelligence group lab "later this week":** the **waitlist applies only to Siri.** It does not apply to the PCC language model or to the on-device things Siri does.
- Bonus: **yes, the beta includes AFM core advanced** ("(transcript unclear)" on the exact "20 billion" figure; the panel confirmed the model is present and used "for the voice features and stuff").

### Bring-your-own provider, Dynamic Profiles, and privacy boundaries

**Indigo J.** asked whether you can mix the on-device model, PCC, and a third-party provider in a single agentic flow, and what the data-privacy and attribution boundaries are once a third party is in the loop.

- **Eric** (pointing to the **"Building agentic experiences with Foundation Models"** session): the new **Dynamic Profiles** API routes to different models declaratively. The declarative nature makes it easy to reason about which model does each task. You design the boundaries and handoffs around **performance, privacy, and (for third-party models) cost.**
- Asked whether the whole context goes to each model (the core privacy concern), Eric described **two patterns:**
  - **Baton pass:** like a relay race. The next runner sees everyone before them; when you take the baton you have full context and you carry it across the finish line. Good when the full context is meant to be shared, or you're only using on-device + PCC. Bluntly, "It does not sound very private. It is not."
  - **Phone a friend:** like *Who Wants to Be a Millionaire*. You call a friend who has **no view into the earlier questions**, only what you ask right now; they answer, and **control returns to you** to give the final answer. Preserves privacy of everything before the call. The panel agreed it's **basically tool calling** a bigger (or different, or smaller) model with an **ephemeral session**: it also **gives you an extra context window**.
- **Managing different context sizes across models:** Foundation Models adds **profile modifiers** this year. You can hook handoffs, or declaratively say e.g. "keep only the last 10 transcript entries," or "drop a tool call from the transcript once its output has produced a response." These are **stateless transforms**: a small on-device model sees only the last few entries, but bouncing back to PCC restores the full context (everything before the suffix comes back into play).

### Speech personalization (out of panel scope)

**Claire Casey** (new developer) asked whether on-device speech-to-text auto-personalizes to each user (learning their words and pronunciations over time) or whether she must build and maintain that list herself.

- The panel was candid: **no one here represents the speech / Vision frameworks**, so they couldn't answer authoritatively. At a high level there are two sub-topics: OS-level online learning (what Siri might do) versus what you do inside your own app (your responsibility).
- **Raziel** drew on prior speech-recognition research: a recognizer is typically paired with a **personalization component** (e.g. a small language model you fine-tune on examples), common for contact lists and hard-to-pronounce names ("my name is really hard… Raziel"). Whether Apple's API exposes that, he wouldn't assert: "I will assume that we do. But we don't know."
- If the language is already supported by on-device speech, just use what's supported, it's "already pretty good." You **can** build your own via Core AI ("ODI," transcript unclear) if you need to. Recommendation: **ask on the developer forums**, where the actual framework engineers are watching all week.

### Teaching coding agents your code style; local models in Xcode

**Pichaya_TRYYS** uses a local LLM (**Qwen** in **LM Studio**, with Xcode/VSCode/Continue) to learn a complex codebase (visionOS, Metal, physics simulation, a macro that generates complex 3D resources) and it performs poorly.

- **Kevin** framed it as **search and learning**: give agents tools to search and find things, and have them write down what they learn so they don't re-discover it every time.
  - Agents are good **out of the box** at copying the style of source already in your project.
  - Use an **AGENTS.md / CLAUDE.md** file that's auto-included in every query, so keep it **short** (it eats your context window each turn); don't paste your whole style guide. Instead **reference** other on-disk markdown ("there's a style guide, here's where it is," or "when working in this area, check this file") so the agent fetches detail on demand.
  - As the agent explores (e.g. your network layer for the first time), **have it document the assumptions it sees**; correct and maintain that file; point it back at it next time.
  - Files can be **global or per-project**; the onus is on the developer to keep teaching the agent the team's preferred style.
- **Eric's counterpoint on over-investing in skills/docs:** when a **new model** comes out, **try using none of it first.** Develop "the skill to learn which skills you actually need," because newer / more advanced models may already understand what you'd documented. **Angelos:** older local models may not know new APIs but **learn them**, so you may not need to document APIs explicitly.
- **Kevin** on Apple APIs specifically: **Xcode searches extensive documentation.** Even a model trained before the new APIs existed can pull in the new iOS/macOS APIs via documentation tool-calls, useful during betas.
- **Angelos's bigger point:** the recent leap is **not mainly in the models, it's in the rigging around them** — the tools we now give them that we didn't two years ago. Drop a model that's a few years old into the **new Xcode harness** and it's far better than even last year's Xcode experience: grounding, running code, debugging, agentic loops, the simulator, and remembering what it tried.
- On the asker's setup: a smaller local model running on a laptop has a **smaller context window**, so it may not read the whole codebase, but it should do well on **style replication**; deep-reasoning fixes are where small models fall down. The panel assumed they're running with MLX.
  - MLX session example: Xcode driving a **Qwen 3 model (~5B?)** (transcript: "QWEN 35B" / "Qwen 3.5B," exact size unclear) through normal user tools.
  - Key advice: the asker is using the **chat** provider. **New in Xcode 27 is ACP (Agent Client Protocol)**: plug in agents talking to locally hosted models (LM Studio, Ollama, "Alamo" = transcript-garbled Ollama) as **full agents**. Chat is a single transcript; **agents can have sub-agents, manage state, do file I/O, and run far longer loops.** It works either way, but agents are "way, way better."
- The reported crashes remain a mystery without more info → developer forums.

### Agentic UI testing and the simulator

**Brian KM** asked what practical steps teams can take to integrate automated UI testing on Apple platforms (interpreted as UI testing, possibly using AI tools).

- **Kevin's test pyramid:** lots of small, fast **unit tests** (agents are great at enumerating permutations and structuring code to be testable independently, without real backends); fewer, slower **integration tests** that pull in dependencies; and just **a handful of UI tests** as the last connect-the-pieces check. Agents help tease tangled code into testable submodules.
- **New in Xcode 27: the agent can drive the simulator** — tap, swipe, type — and reads back both the **accessibility tree and screenshots** to figure out what's on screen. It can learn the patterns and then **write durable UI tests** so you don't need the agent every run.
- Workflow color: "sometimes we'll just let the agent run for a couple of hours and look for different bugs," producing reports on which areas need attention; flaky systems get demoted to unit tests. Asked whether you could hand it screenshots of a finished UI and say "go build this," the answer was an enthusiastic **"give it a shot, see how it goes."**

### Vision vs Foundation Models (and Translate)

A question asked about updates to the natural language framework and Apple Vision Kit, and the preferred image-extraction method now that Foundation Models supports image attachments.

- The panel deferred on the natural language framework. For Vision: **"what's new in image understanding"** session (the speaker corrected himself from "what's new in Vision"), with cool segmentation models among other updates.
- **The dividing line:** Foundation Models gains **new tools powered by Vision** this year — a **barcode-reader tool** and an **OCR tool**. Use the **Vision framework** when the task is well-understood and roughly the same every time (detect a particular object/image): optimized, testable, efficient. Step up to **Foundation Models** for semantic understanding, natural-language nuance, or anything different every time.
- **Eric's analogies:** Foundation Models are **like a 3D printer** (do anything, great for lots of custom orders); a **baked, specialized model is like a production line** stamping out one thing efficiently once you've designed it. Rule of thumb: **if a structured API already fits the structure, use that API.**
- **Translate example:** the **Translate framework** supports **more languages** than Foundation Models' translation subset and is simpler when you know the input/output languages. But Foundation Models wins on **nuance**: "translate as if it were 1920" (style), or translating someone **speaking two languages at once**, things a classic model wouldn't attempt. Static/everyday translation → Translate API; dynamic/unknown/stylistic → a language model.

### Context-window management (the longest exchange)

**John Lee** asked best practices for managing prompt size, tool definitions, and context on token-limited on-device LLMs while keeping quality high.

- **Eric** on this year's APIs:
  - **iOS 26.4** added symbols for **context size** on the system language model and **token counting** — know programmatically how much context you have and how much a prompt will consume.
  - **`response.usage`** (new this year): exact **input / output / cached** token counts, and for a reasoning model, **how many output tokens were reasoning** vs the rest. Called out as a **highly requested** feature.
  - **Strategies:** after a tool call has produced a response, **drop the tool call and its output** ("often once you have the answer, that's all you need"); drop or **compress** older, less-relevant entries.
  - New open-source repo **`foundation-models-utilities`**: higher-order building blocks usable with Foundation Models *or* Core AI / MLX / any backend via the protocol. Ships a **summarize-history modifier** that, at the start of each prompt, checks transcript size and, past a threshold you set, **summarizes the whole thing into one entry** and continues from there. These utilities **apply to any backend** that conforms to the language model protocol, "you get that for free."
- **KV-cache tradeoff** (called "hotly debated"): let context fill up to avoid invalidating the KV cache (low latency, but one big expensive invalidation later, and the model may get **distracted** by stale content), versus **dropping tool calls after every response** (smaller, more frequent invalidations). Resolve it empirically: this is where **evaluations** come in — A/B the same eval data set across different dynamic profiles and use **`compare`** to see which configuration is best (does the model want more or less context; does it do better after summarizing).
  - Steven's framing: developers keep asking "can the model do A or B?" and "your use case is unique, we don't know the answer for you, but now you have the tools to go get the answer and be confident in it" — important as software moves into more **non-deterministic** behavior. Same tool answers "PCC or on-device?"
- **Summarization is configurable:** the summary prompt is **overwritable** (panel principle: "if there is a prompt involved, that prompt has to be overwritable"). Ask for a verbose or a very concise summary; it's "a model… you have to tune it for your use case, and you have to measure your use case."
- **Angelos** on architecture: newer models are **better-behaved on context** thanks to attention variants — **sliding-window attention** (context doesn't grow) and **linear attention** (context fixed from the start). Also, **reasoning is the first thing you drop** (it costs latency even on simple tasks); newer models have **configurable reasoning** (PCC supports **low / medium / high**) and can choose not to reason, leaving more room for useful tokens. Some models **fake a large context** but with caveats.
- A simple, naive tactic that works: **don't make one session do everything.** Split independent tasks into **separate sessions**, each with the full context window to itself, or use the same model as a tool to gain an extra window.

### Guardrails and refusals

**Evo** noted Foundation Models guardrails sometimes refuse emotionally intense but legitimate journal entries (grief, venting). Can refusals on first-person emotional writing be prevented, and how do you distinguish a guardrail refusal from other errors to fall back gracefully?

- **Eric:** answer is "both yes and no."
  - For **input**, there's a setting on the system language model when initializing guardrails: **`permissibleContentTransformations`** (transcript-approx). Turn it on and the model **won't error out on emotionally charged input.**
  - But the model **may still refuse in natural language** to elaborate or expand in that style.
  - With **structured output / guided generation**, the model can throw a **refusal error**, which is **distinct from a guardrail error**:
    - **Refusal error** = the model's own response ("I'm sorry, I can't help with this"), driven by its **alignment training**, not the guardrail.
    - **Guardrail error** = a **separate moderation model** inspecting input and output and flagging problematic content; catch it separately.
  - **None of this applies if you bring your own model** — guardrails are Apple's models only. Options exist to ship a Qwen model or similar.
- Plug: **guardrails were retrained hard this year** with lots of data sets; **false positives should be way down.** Try the newest system language model; file feedback if it still misbehaves.

### Evaluation philosophy

A developer noted Apple's distinct perspective on design and privacy and asked the panel's guiding philosophy on AI evaluation.

- **Steven** (who "used to teach for a while"): don't build the feature and evaluate later — **start with evaluation, because the evaluation set is the living specification of the feature.** It encodes everything it should do well, **headroom** to grow into, and **edge cases**. This is an **evaluation-driven development life cycle**, analogous to **formative assessment** in education ("assessment *for* learning," not "what did you already learn"): the quizzes feed back so you find weak spots and improve.
- The framework supports starting from a **small curated data set** of core use cases, **synthetic expansion**, then running it to see where the model/configuration shines or falls down, and **comparing** changes to **hill-climb** the feature. The team built their own product-evaluation experience into the fabric of the framework. Panel framing: evaluation is "the bread and butter of AI," and the **verification-and-validation** principle is fundamental across engineering disciplines — just **harder with language models, which is why you need a framework.**

### Sharing models across apps

A frequently-asked question for **Raziel:** can models used by different apps on iPhone be shared across apps to save storage?

- **No, not on iPhone.** It sounds appealing (don't duplicate constrained resources), but it becomes **hard to control who's using the resource** when multiple apps want it at once.
- What **is** possible: **Core AI model caching** keeps resources in memory and can **share within an app group** (if you have one).
- **Quantization specificity:** the same model (e.g. a "Qwen 6.6 billion," transcript unclear) might be floating-point for one app and **4-bit** for another depending on each app's evaluation-driven quality/performance tradeoff, so a single shared instance rarely fits everyone.
- **Sandboxing/security:** the desktop trick of downloading weights into a **shared CAS** (content-addressable store) so another process reuses them is "fundamentally something you can't do on the iPhone for security reasons."
- Clarified distinction: **weight files** can be placed in a shared app-container group and the **download** shared, **but only between apps owned by the same developer**; the **in-memory runtime loading is never shared**. Cross-developer sharing "needs to be a service or something bigger than just a download" with integrity guarantees.
- The panel tied it back to the opening: **the on-device Foundation Model lives in the OS, not your app** — try it first, it doesn't add to app size, and there's PCC; only bring your own model (Core AI / MLX) when you must, test with evaluations, and lean on agentic coding in Xcode.

## Unconventional facts & takeaways (the live-Q&A gold)

- **Core ML is being narrowed on purpose.** "Everybody that work with neural networks" should use **Core AI**; Core ML stays for **traditional ML / decision trees**. A clear, rarely-spelled-out positioning statement.
- **Anthropic's language-model-protocol package shipped the morning of the lab**, alongside a live **Google package** — concrete evidence the bring-your-own-backend story is real on day one.
- **The Foundation Models framework is going open source "very soon."** Stated on-air as a near-term plan, alongside the already-open MLX and Core AI packages and the new `foundation-models-utilities` repo.
- **Hard numbers not in a slide:** on-device context **4096 tokens** (shared, unchanged YoY); **PCC 32K** (shared); plug-ins reach **~1M**; PCC reasoning has **low / medium / high** levels.
- **`response.usage` exists because developers asked for it** — and now separates **reasoning tokens** from the rest of the output.
- **"The rigging around the model" improved more than the models did.** Drop a multi-year-old model into the new Xcode harness (doc search, run/debug loops, simulator control, memory) and it beats last year's Xcode experience. A candid statement that tooling, not raw model quality, drove the recent leap.
- **Counter-intuitive advice:** when a new model lands, **delete your skills/AGENTS.md and try with nothing first**; the model may already know what you documented. "Develop the skill to learn which skills you actually need."
- **`permissibleContentTransformations`** lets emotionally charged *input* through, but the model can still refuse in natural language — and a **refusal error (alignment)** is a different thing from a **guardrail error (separate moderation model on input+output).** A genuinely confusing distinction clarified on-air. Guardrail **false positives are down** this year after intensive retraining.
- **Models genuinely can't be shared across third-party apps on iPhone** (sandboxing + use-case-specific quantization). Only **same-developer** apps can share weight files on disk; runtime memory is never shared.
- **Candid limits:** the panel openly punted the speech-personalization and Apple Intelligence/Siri-waitlist questions to the right teams/labs and the developer forums rather than guess.
- **Cultural color:** Eric's analogies do a lot of work — **3D printer vs production line**, **relay-race baton pass**, and **phone-a-friend / Statue of Liberty** for privacy boundaries. Steven's **formative-assessment / "living specification"** framing for evaluation. Raziel using **his own hard-to-pronounce name** as the personalization example.
