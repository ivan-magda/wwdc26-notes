---
title: "Coding Intelligence for Beginners Group Lab — Full Digest"
session: WWDC26 · 8007
url: https://developer.apple.com/videos/play/wwdc2026/8007/
duration: 01:01:54
sources: transcript.md (whisper.cpp ggml-large-v3, session SD video audio), meta.md
compiled: 2026-06-13
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions, host routing, and context. Some spoken names are mangled by whisper (e.g. "Jerome" → "Drum"/"Jrum", "Ken" → "Kent"); normalized here.
---

# WWDC26 · 8007 — Coding Intelligence for Beginners Group Lab

> A ~62-minute live developer Q&A with the Xcode team about agentic coding in
> Xcode 27, pitched at developers new to the topic. Not a feature talk: it's the
> unscripted layer underneath the Xcode coding-intelligence sessions, full of
> beginner pitfalls, candid limits, and a steady drumbeat of "ask the agent,"
> "use Git," "trust but verify," "file feedback," and "just try it." Several of
> the most-upvoted questions are really feature requests, which the panel happily
> redirects to Feedback Assistant.

## TL;DR

- **Agent mode vs. chat mode is a capability gap, not a quality one.** Chat exposes
  roughly **10–15 tools**; agent mode exposes **almost 60 tools inside Xcode** plus
  command-line tools, sub-agent spawning, and context management. The time horizon
  scales with it: predictive code completion saves **seconds**, chat works for about
  **30 seconds**, and agents can now run **an hour to an hour and a half (sometimes
  more)** independently. A local model in chat mode will feel "much different" from
  the agentic demos because it *is* a different, smaller-tool experience. The panel
  wants everyone moving from chat to agents.
- **Apple is not in the loop on your code.** When you use a third-party model/agent in
  Xcode, requests go **directly to that provider**; Apple sees none of it. Training
  opt-out and data retention are governed by the provider's terms and your account
  settings. The one exception: clicking **Feedback Assistant** shares the context you
  attached with Apple.
- **Local models: you can mix and match agent + model.** Via **ACP**, a local
  open-source agent gets the **same Xcode tool set** as Cloud-hosted Claude Code, Codex,
  or Gemini. The only real gap is raw model muscle: a giant hosted model on a GPU
  cluster will out-think what runs on a **MacBook Air**, so compensate with **pristine
  guardrails and unit tests**. "If you wouldn't accept that answer from a co-worker,
  don't accept it from a machine."
- **There already is an undo, and it's tied to Git.** Xcode tracks **per-turn agent
  history** (requires an initialized Git repo): open it, move the slider up a level, hit
  **Revert**. You can also just ask the agent to roll back. The broader safety net is
  Git itself — "Please use Git," and note the new-project checkbox is ticked by default.
- **Xcode ships on-device documentation as a separate, auto-updating asset** in two
  forms: human-readable and **model-optimized**. Any provider (local or cloud) gets it
  through Xcode's documentation tool, so a freshly announced **iOS 27** API the model
  has never seen still works: "Xcode's got your back." Xcode also ships **skills /
  specialists** (e.g. one for the new **iOS 27 resizability feature**). Foundation
  Models notably has **no specialist** yet, because the doc index alone works well for it.

## Panel

Host **Angelica** (Worldwide Developer Relations) with four Xcode panelists:
**Nathan** (manages an Xcode intelligence team — agent/model interactions),
**Ken** (leads the Xcode team; the "Xcode themes wardrobe" person from the Platform
State of the Union, who wanted the *neon noir* theme), **Jerome** (product manager for
developer tools; self-described pins collector), and **Kevin** (senior engineering
manager for Xcode, focused on the AI tools).

Whisper has no diarization. Mid-transcript attributions below come from host routing
("Nathan, do you want to kick it off?") and content, not from voice. Whisper also
mangles **Jerome → "Drum"/"Jrum"** and **Ken → "Kent"** in places; normalized here.

## Warm-up: how have agents changed app development? (01:39–05:20)

A general opener to all four panelists.

- **Make ideas real, faster.** Agents let you "articulate your ideas and make them more
  real." One panelist (routed to Jerome/Ken) works with the agent to **write a spec
  document** that captures intent, then iterates on it over time. People did this
  before; agents just **codify** the practice.
- **The agentic loop is the real shift.** Code completion → chat-generates-in-a-file →
  an agentic flow that "can verify its own work, fix its own errors, and work until
  something is done." That keeps you creative because you focus on making the idea real,
  not on typing one character at a time.
- **You can now cheaply test whether an idea is even good.** "Most of the time my initial
  idea is probably wrong." The cost of writing new code and deleting old code has
  "fundamentally changed," so you feel emboldened to explore paths you'd previously have
  stared at and abandoned.
- **It front-loads good practices.** Building a **test suite** "benefits every prompt you
  have with the agent moving forward"; **translation / localization** with agents helps
  the developer and pays off "immeasurably" for users. Best practices people normally
  defer become worth doing early because they make the agent better.

## Developer Q&A

### Discovering commands, skills & "ask the agent"

- **Where do you learn commands like `/plan`? (Pichaya TriYS).** Type `/` and Xcode shows
  the commands the **agent reports back** plus any **skills loaded from Xcode**. Better:
  **ask the agent** — it's "aware of almost every command that can be executed and what
  it will do." Experiment with both; you may discover a whole new workflow out of the
  box. "Ask the agent" is flagged as the answer to many of today's questions.

### Xcode's advantage over external agents

- **What's Xcode's unique advantage over external AI agents, and where's this going?
  (Jason Chung).** The future half is dodged ("we don't talk about future plans"). On
  the present (Kevin leads):
  - **Curated tool set + project understanding** — Xcode works to make the agent
    understand how your project is laid out and what that means on Apple platforms.
  - **SDK/API surfacing + documentation tools** — the agent can explore new APIs and
    always pull the most relevant info.
  - **Validation tools** (the part that "gets me really excited"): build tools (a **full
    build** plus **little/incremental builds** for speed), execution of **small code
    snippets**, all the way up to **rendering your UI and interacting with / testing it**.
  - **Apple-ecosystem data** — crash data, localization best practices, "Apple knowledge
    baked in."
  - **The preview system handed to the agent** — unique to Xcode; the agent can now
    **visualize its own work**, with crafted annotations/text/previews. Feels like an
    Apple product, welcoming to newcomers, powerful for 20-year veterans.
  - **Onboarding + planning mode** — a good out-of-the-box starting point so a beginner
    sending one English sentence gets a "running start," plus a strong planning mode
    Xcode builds on, and a solid base tool set; both extensible with **plug-ins**.

### Agent context & extra working directories

- **Can you add working directories so the agent can reference other projects?
  (Juto Art).** (Nathan kicks off.) Two security modes: by default Xcode uses
  **permission prompting** so the agent reaches what it needs without free rein; if
  prompts get in the way, beyond "allow always" there's an **early-preview "managed
  security" mode** that gates the agent's actions at the **file-system level**. For
  references: anything in your **Xcode workspace** the agent can already see, so use
  **workspaces to bring in multiple projects**; for files outside the workspace, paste
  the path or point it at a directory ("it's in my documents directory") and it "figures
  out the distinction" and gets the access it needs. You stay in control: tool calls
  that move or delete files prompt for permission. Easy to standing-grant access to,
  e.g., a Desktop screenshots folder you always want referenced.

### "Explain what I just did" — learning the code

- **Beginners finish with working code they don't understand; please add an optional
  "explain what I just did and why" summary. (Tammy Santana — flagged as a feature
  request.)** Several tactics short of a built feature:
  - **Ask the agent** — "they love to talk." Ask "why did you just do that?" and request
    the format you want (HTML report, markdown file).
  - **Bake it into `AGENTS.md`** — "after I run a prompt, always give me an explanation,
    here's the format." You've effectively built yourself a feature / a personal skill.
  - **Parallel conversations** (new in Xcode 27): while the agent works, open a second
    chat to ask about the code being generated or a new API. "Build and learn at the
    same time."
  - **Spec-driven up front** — make it state its assumptions and intended implementation
    before coding, and capture that.
  - **Write the shape yourself** — declare the struct/properties, declare the protocol,
    leave comments where pieces connect, then open a chat to "thread these pieces
    together." Define structure fast without being as hands-on as before.
  - **Plan mode** — not just safe (nothing changes in your project) but a window into
    *how* the agent approaches a problem; you become "the conductor."
  - **Architecture / data-flow diagrams** left in the project — help visual learners
    validate the design, and the agent reuses them on future features (so do teammates).
  - A panelist's playful trick: ask the agent to **host a trivia game show** about the
    code it just wrote, answer multiple-choice questions, and learn the code you now own.
  - Caveat: **debugging is still your responsibility** — you can ask it to debug, but you
    must verify the fix is correct.

### Common mistakes beginners can't catch

- **What common assistant mistakes are hard for beginners to spot? (Florentine F.)**
  Whole classes of bugs, just like humans. The subtlest:
  - **Missed assumptions** — you ask for one thing and get something different, or get an
    **unmaintainable** version of the right thing.
  - **Incomplete discovery** — it doesn't always find everything it needs. "These are
    fallible tools in a way that a compiler is generally not."
  - **"Cheating"** — the most worrying variant: an agent rationalizes a failing test
    ("it doesn't pass because I'm in debug; it'll pass in production"). **"If you
    wouldn't accept that answer from a co-worker, don't accept it from a machine."**
  - Mitigations: **tests, strong types, and live issues** give the agent fast feedback so
    it catches its own wrong work before it reaches you (after it reaches you it's far
    harder to untangle). Xcode invests heavily in validation tools for exactly this.

### Undo / rollback

- **There's no undo button; please add "undo agent changes" — git is my only rollback
  and beginners don't all know it. (Tammy Santana, again.)** "My favorite kind of feature
  request — it's already implemented." Three layers:
  1. **Ask the agent** to revert to the previous state; it holds the whole context and
     usually does this well, and can selectively keep some work while undoing other
     ("undo this part, keep this part").
  2. **Xcode per-turn history** (requires an **initialized Git repo**): open it, move the
     slider up one level, hit **Revert** to return to just before the agent's last turn.
     Granular, "the last action."
  3. **Git** — the largest time window / safety net.
  - Bigger message: **use Git regardless of agents.** Solo devs often think Git is only
    for teams/PRs, but it's how you revert a shipped bug fast. The new-project checkbox is
    on by default; "there's no downside." Pro tip: **good commit messages** (document
    assumptions) so the agent doesn't have to re-derive intent from the diff — and so it
    can trace a later bug back to a bad assumption made during unrelated work.

### Getting started / building AI-assisted skills

- **Experienced iOS dev, little AI-coding experience — how should I build the skills
  industry now expects? (UJ).** "How much time do we have today?"
  - **Start simple**, on what you already do: small changes, small bugs you don't want to
    spend time on, where you can reason about the result easily.
  - **Start with tasks you already know how to do**, so you can compare the agent's output
    to your own and build confidence, then expand.
  - **Bring your experience** — it's invaluable. Agents "love to learn"; direct and guide
    them ("this is the way I work"), and they keep applying it.
  - **There's no one way** — some people use agents purely as a powerful find tool / a
    planning aid. If surrendering control feels wrong, don't; use it as a planning aid,
    and if you like it, experiment further. "No harm." It's adaptive: "it can really
    become whatever you need it to be."
  - **You're not handing over control** — a "big misconception." **Review all your
    changes**, work with the agent, use **plan mode** to verify implementation details.
  - **Top-down or bottom-up** both work: start coarse ("write your header files first /
    give me the shape, let's talk about your Swift interface"), or, if you know the shape
    from experience, declare the protocols/classes and let the agent fill in the bodies
    for you to review.
  - Session pointer: **"Xcode Agents and You"** for workflow ideas — exploring a project
    or a new feature, and refining / orchestrating **multiple sub-agents** (e.g.
    translation).

### Agent mode vs. chat mode (and local models)

- **Key differences between agent mode and chat mode? I connected Xcode to a local LLM
  via `MLX LLM server` in chat mode but results differ a lot from the coding-intelligence
  demo. (Pichaya TriYS).** "Yes, it will be." It's a **capability** difference. Chat mode
  gives the agent **~10–15 tools** on your codebase. **Agent mode is basically
  infinite**: command-line tools, **almost 60 tools inside Xcode**, plus agent
  "wrappers" that manage context and **spawn other agents** ("superpowers"). A couple of
  the in-UI agent integrations are **open-source** and encourage trying **open models**;
  set them up via **Xcode settings → select the agent → "…" → set up with configuration
  file**, then paste the example from the provider's docs/GitHub. You can also use **ACP
  mode** for basically any agent, including local ones, some of which are designed to be
  great with local models.
  - **Time horizon, by feature (Nathan).** Predictive code completion saved "two, three,
    five seconds." Chat could "go for 30 seconds, find an answer, make code changes."
    Agents: started at "a half hour" of independent work a year / six months ago, now
    "an hour, an hour and a half, even more sometimes." Agents can still do the
    30-second small jobs too. **"We really want everyone moving from chat to agents …
    now is a great time with Xcode 27."**

- **If I use a local model instead of cloud, what do I miss — just slower / less
  accurate, or whole agentic features? (Interferon).** Separate the **agent you run**
  from the **model it talks to**; mix and match. Xcode has supported talking to **local**
  models "from the very beginning" — on your own machine or a **local server** (run the
  model on a bigger Mac and point Xcode at it). Local model in *chat* mode = the chat
  experience (local models "are becoming really good," but it's still chat-mode results).
  Plug a **local open-source agent** into a local model and you get the **agentic tools**.
  TL;DR (Jerome): because of **ACP**, the **same Xcode tool set** that's available to the
  Cloud Claude Code / Codex / Gemini agents is available to a local agent. The biggest
  difference: a commercial provider's model may be **too big to run anywhere but a
  powerful GPU cluster**, so "you are not going to get the same super genius on your
  MacBook Air — and that's okay." Compensate with **tighter guardrails** and **pristine
  validation steps** to keep a less-smart model on track.

- **Can I connect coding intelligence to a local LLM (e.g. `MLX LLM server`) as an
  agent, not just chat, so it handles things without pointing file to file? (Pichaya
  TriYS, follow-up).** Yes — **ACP support** is the approach. Many open-source tools /
  wrappers with local-model support exist, such as **OpenCode**. ACP is also great for
  **enterprises** with their own configurations: whatever harness on the back end, ACP
  brings it inside Xcode.

### Privacy of your code

- **What about the privacy of my app's code when a third-party LLM accesses it via Xcode?
  Will it train on my code/queries, or store data short/long term? (antscrashing).**
  (Jerome.) When you enable a coding agent/model from a partner, you see **their terms**
  and how they use your code, and **you control** whether the provider may use your code
  for training — set in your **account / the model provider's settings**. Same for chat
  and agentic. Crucially, **Apple is not in the loop**: "agents in Xcode don't — Apple
  doesn't see any of that"; there's a **direct call to the agent provider**, by design.
  Summary for the asker: **check your provider's privacy policy and account settings**;
  Apple sees nothing as the request is sent. **One caveat (Nathan):** if you send a
  **Feedback Assistant** report ("when I write this, it doesn't work"), Apple **will** see
  the context you attached.

### Offline / on-a-train development

- **Best way to work with AI in Xcode while offline (e.g. commuting on a train) without an
  M5 Pro / 128 GB RAM? (Stuff MC).**
  - **MLX** is the best way to run a model locally: "you can get a larger, more
    interesting model running with MLX than any other technology on our devices."
  - **Guardrails / constraints** carry you: make the agent **pass unit tests to move on**,
    write good unit tests with it, and "it can't lie and cheat its way out" — it keeps
    working until it finds the solution.
  - **Combine large + small:** before going offline, use a **larger model** for planning,
    requirements, and setup; **offline, have local agents document every assumption**;
    **back online, use a large model to double-check** those assumptions match.
  - Emerging pattern (from partners' research): **larger models advising smaller-model
    agents** — have agents write to markdown and **critique each other**; Xcode makes it
    easy to run several agents/models **simultaneously** and to switch a conversation
    between local (offline) and cloud (online).
  - Session pointer: **"Run Local Agentic AI on Mac Using MLX"** (name given on-air).

### Terminal-only workflow ("what am I missing?")

- **I've used Claude Code in Terminal for a while and barely need Xcode anymore — what am
  I missing? (Jay Roden).** Everyone approaches agents differently; Xcode pairs powerful
  tools with a UX built on top:
  - **Non-linear vs. linear** — a terminal is "a lot of text flying by," linear; Xcode
    pulls the story into an **artifacts area** next to the conversation so you watch the
    change unfold, click around, and edit source.
  - **In-memory project context** — open files, active schemes, current selection, project
    settings; natural-language "this and that" just works because Xcode knows what you're
    pointing at.
  - **An ensemble of tools, not just an editor** — Instruments, the preview system, and
    the **Device Hub the agent can now control** — not usable in a plain-text terminal.
  - **Rich rendering** — beautiful markdown, previews, good color themes; you see results
    differently than in a terminal.
  - Not all-or-nothing: going back to the terminal is sometimes the right answer; Xcode
    gives a **broad continuum** of how much to buy in.

### Local models: balancing speed, accuracy, privacy

- **Can I use local AI models for coding intelligence in Xcode 27, balancing speed,
  accuracy, and privacy? (Jack92).** Again, watch **"Run Local Agentic AI on Mac Using
  MLX."** On the balance:
  - Local models vary widely (**different quantizations, floating point, etc.**) — see
    what works for *your* project; the best model can be "totally different" per project.
  - The team **benchmarks** agents/models heavily and it gives powerful signal, but the
    ultimate decider is **"the vibes"** — how it *feels* to use. Online spreadsheets list
    "every variation with every letter of the alphabet" in long variant names, but just
    **give it a shot** and see if you enjoy it. Tools are "so personal."
  - **Build a personal scorecard** of things that matter in your project; run new
    local models / settings through it as they ship. It needn't be scientific — "just
    vibes," or eventually a mechanistic scorecard you ask the agent to build.
  - **Bigger isn't always better** — "definitely not." Check extremes: a huge model that
    eats memory may give no better (or barely better) results than a much smaller one.
  - You can **keep multiple models on your Mac and swap them by task**.

- **I don't want to give Anthropic, OpenAI, or Google access for privacy reasons — is
  there a path to use Foundation Models as the Xcode agent? (Ran Learns).** Same advice:
  **local models, local agents**, and **"Run Local Agentic AI on Mac Using MLX."** The
  Mac is a great place for this — an **M5 Mac** is already powerful; an **array of Mac
  Studios** could run one of the largest models.

### Keeping agents current with new APIs (pulled from an earlier lab)

- **How do we make sure agents know the most up-to-date APIs — the ones announced at WWDC
  this year? (asked at an earlier coding-intelligence / Apple-intelligence group lab on
  the Tuesday evening in Cupertino; re-surfaced here).**
  - **A dedicated documentation tool.** Xcode ships documentation for all new APIs, and
    the agent can **search** it; the team experimented with techniques to make that
    efficient so you "always know you're getting the latest APIs."
  - **It's on-device.** Downloading Xcode downloads the latest documentation — now in a
    human-readable version **and** versions **optimized for models** (fast, efficient,
    best answers). When the agent doesn't know an API, it taps that doc and adds it to
    context.
  - **Skills / specialists** ship too — e.g. one for the **new iOS 27 resizability
    feature** — so the agent brings new context when you ask about that API.
  - **Just try it:** create an empty project and ask the agent to demonstrate a new iOS 27
    API. **Foundation Models is a good test** precisely because there's **no specialist**
    for it — the doc index alone lets the agent work with it well.
  - **Provider-agnostic:** whether local, Cloud Claude Code, Codex, or Gemini, Xcode feeds
    the same documentation. "If the agent isn't up to date because we just announced it,
    Xcode's got your back." And documentation **ships as an individual asset**, so it
    updates automatically as new APIs land — a separate Xcode download.

## Unconventional facts & takeaways (the live-Q&A gold)

- **Hard numbers you won't find in a slide:** chat mode exposes **~10–15 tools**; agent
  mode exposes **almost 60** Xcode tools. Independent agent run time grew from **~30 min
  (a year / six months ago) to 1–1.5 hours+** today. Code completion saves only
  **2–5 seconds**; chat ~**30 seconds**.
- **The undo button already exists** and is **Git-gated** (per-turn history slider +
  Revert) — the "missing feature" was a discoverability gap, not a missing capability.
- **Apple is genuinely out of the loop** on your code: a **direct call to the agent
  provider**, "by design." The lone exception is the **Feedback Assistant** button, which
  shares attached context with Apple. Worth being explicit about with privacy-minded devs.
- **ACP is the great equalizer:** local open-source agents get the **same Xcode tool set**
  as the Cloud Claude Code / Codex / Gemini integrations — the only delta is model size.
- **Documentation is shipped twice** — a human version and a **model-optimized** version —
  and as a **separate auto-updating asset**, which is how a brand-new API works with a
  model whose training predates it.
- **Foundation Models has no Xcode "specialist" skill** — called out as a feature, not a
  gap, because the on-device doc index handles it well.
- **Beginner pitfall Apple names out loud:** agent **"cheating"** (rationalizing a failing
  test as a config artifact). Mantra: **"if you wouldn't accept that answer from a
  co-worker, don't accept it from a machine."**
- **Model choice is decided by "vibes,"** explicitly, over benchmark spreadsheets — and
  **bigger isn't always better** ("definitely not").
- **Parallel conversations** (Xcode 27) double as a learning tool: one thread builds,
  another explains the code/API as it's generated.
- **`AGENTS.md` turns a habit into a feature:** auto-append explanations, enforce a format
  — "you've basically built yourself a feature inside Xcode."
- **Cultural color:** "**you're not playing Mozart, you're playing jazz**" (no single
  right path); the agent-trivia **game show** for learning your own code; Ken's
  Platform-State-of-the-Union **Xcode-theme wardrobe** (he wanted *neon noir*); Jerome the
  pins collector; "WIP / fixing" commit habits the agents broke them of; "how much time do
  we have today?"
