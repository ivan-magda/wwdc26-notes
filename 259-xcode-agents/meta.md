# Session 259 — Xcode, agents, and you

- **URL:** https://developer.apple.com/videos/play/wwdc2026/259/
- **Duration:** 24m
- **Speakers:** Devin, Maxwell (Xcode Intelligence team)

## Description

An overview of coding agents in Xcode 27. Two members of the Xcode Intelligence team
build a workout-tracking app together and walk through how agents support each phase
of development: exploring a codebase, planning and building a feature, refining the
visual design, and orchestrating multi-step tasks with sub-agents. The throughline is
keeping the developer in the driver's seat — you set the vision, Xcode helps you get
there faster.

## Key topics

- Coding agents in Xcode 27 (introduced in 26.3; tools expanded and the agent interaction redesigned this year)
- **Explore** — whole-project context (source, build settings, open files, active selection); ask for a walkthrough (data flow, key-area tables, source links); capture findings as architecture documents committed alongside source; Apple Document Search for current framework knowledge
- Conversation UI — Option+Shift+click toolbar button opens a conversation as a separate editor pane; left = transcript (progress, tool calls, sub-agents), right = artifacts (files, edits, previews)
- **Build** — `/plan` (plan mode) to design architecture before code; queued messages to add requirements mid-task; agent asks clarifying questions; diffs/new files surface as artifacts; build + preview + test tools validate (12 generated tests passed); architecture docs kept up to date as the knowledge base
- **Refine** — Swift Charts; previews on artificial data to compare chart types; image/sketch attachments (Freeform sketch → line chart); inline annotations to scope changes to exact source locations; preview self-verification loop
- **Orchestrate** — describe a high-level goal (localization, accessibility); the right tools are discovered automatically; main conversation breaks work into parallel pieces and deploys sub-agents; two conversations (Filipino localization + VoiceOver/accessibility) run at once
- Tools come from three sources: built into Xcode, provided by Apple framework teams, or added by you

## Related sessions to fetch

- [ ] Create UI Prototypes using Agents in Xcode
- [ ] Translate your app with agents in Xcode (referenced at the end as "Translate your app with agents in Xcode"; mid-talk it is named "Translate your app with agents in Xcode" — see Open Questions in digest re: exact title)

## Chapter summary (Summary tab)

- **0:00 Introduction** — overview of coding agents in Xcode 27 and what the session covers: explore, plan/build, refine with visual design, orchestrate multi-step tasks.
- **1:14 Meet the app** — Maxwell and Devin introduce the workout-tracking app they're building — the starting point for exploring how agents support each phase.
- **2:06 Explore** — agents help you get up to speed on a new project: walkthrough of data models and view hierarchy, Apple Document Search for accurate framework knowledge, capturing findings as reusable architecture documents.
- **7:38 Build** — plan mode to design an architecture before writing code, queued messages to communicate requirements in real time, and Xcode's build/preview/test tools to validate features as they're implemented.
- **13:44 Refine** — iterate on visual design with Swift Charts and realistic previews; image attachments to convey design intent; inline annotations to direct targeted changes from source — creative direction stays with you.
- **18:25 Orchestrate** — describe high-level goals (localization, accessibility); Xcode discovers the right tools and coordinates sub-agents to accomplish them in parallel.
- **22:09 Next steps** — key takeaways and links to related sessions on UI prototyping with agents and translating apps with agents in Xcode.

## Code

See `code.md` — there is no Code tab for this session; APIs/UI affordances named in the talk are listed there instead.
