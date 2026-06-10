---
title: "Xcode, agents, and you — Full Digest"
session: WWDC26 · 259
url: https://developer.apple.com/videos/play/wwdc2026/259/
duration: 24m
speakers: Devin, Maxwell (Xcode Intelligence team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 259 — Xcode, agents, and you

## TL;DR

Xcode 27 leans into coding agents. Agents first shipped in Xcode 26.3; this year Apple
expanded the toolset and redesigned how you interact with them. The whole session is a
single narrative — two engineers build a workout-tracking app — structured around four
phases, each a distinct way to work with agents:

1. **Explore** — agents see the whole project (source, build settings, open files, the
   active selection) and produce a *walkthrough* of architecture you can save as
   **architecture documents** alongside your code. **Apple Document Search** pulls
   current Apple framework docs so the agent isn't limited to stale base knowledge.
2. **Build** — **plan mode** (`/plan`) makes you the architect: the agent drafts a
   reviewable markdown plan before writing code. **Queued messages** and **agent
   clarifying questions** create a tight loop. Then build / preview / test tools
   validate the result (12 generated tests passed in the demo).
3. **Refine** — visual iteration with **Swift Charts**: render previews on fake data to
   compare chart types, attach a **Freeform sketch** to convey intent, and use **inline
   annotations** to scope edits to an exact source line. A preview self-verification
   loop lets the agent check its own output before you step in.
4. **Orchestrate** — describe a high-level goal (localize to Filipino, add
   accessibility) and Xcode **auto-discovers tools** and dispatches **sub-agents** that
   run in parallel. Two conversations run at once.

The recurring theme: you stay in the driver's seat. Agents accelerate the work; you
own the vision, the chart style, the plan, and the review.

The new agent UI splits into two panes — **transcript** (left: conversation, progress,
tool calls, sub-agents) and **artifacts** (right: files, edits, diffs, previews).

---

## 1. Meet the app (1:14)

Devin and Maxwell want a personalized workout tracker. The app is already off the
ground (built with an agent): a **workout page** to track exercises and a **history
section** for previous sessions. The goal for the session is an **insights view** to
visualize progress over time. Maxwell explores and builds the prototype; Devin refines
it with charts and then orchestrates localization + accessibility.

## 2. Explore (2:06)

Agents in Xcode can see the **whole project**: source code, build settings, open files,
and the active selection — "tuned into exactly what you're working on in the moment."

- **Start a conversation in a separate editor pane**: hold **Option+Shift** and click
  the toolbar button. The pane layout lets you read individual files while you explore.
- **Ask for a walkthrough**: Maxwell asks the agent to summarize the data models and
  view hierarchy. The agent gathers context with Xcode's tools and distills the
  architecture into something readable — **data-flow outlines, tables of key areas, and
  clickable source references** to jump into files.
- **Capture it as a knowledge base**: rather than losing the discovery effort in one
  conversation, Maxwell asks the agent to draft **two architecture documents** and place
  them in the project alongside the source. The knowledge base evolves with the codebase
  and gives teammates a map for future features. The documents appear as **artifacts**.

### The two information areas (4:58)

- **Transcript (left)** — the conversation: progress commentary, tool calls, running
  sub-agents.
- **Artifacts (right)** — things created: files, edits, previews. Focus on the latest
  message's output or view the whole conversation.

### Apple Document Search (5:36)

To research SwiftUI tables and SwiftData model relationships, Maxwell opens a new
conversation and references the two architecture docs (read immediately for context).
Because the prompt asks about table options, **Apple Document Search** is invoked
**automatically** to pull current SwiftUI table documentation.

- Why it matters: an agent's base knowledge "might not include the latest framework
  information." Document Search grounds answers in high-quality, current Apple docs so
  you adopt the right solution. It surfaces nuances Maxwell might have missed — correct
  SwiftData relationships, best presentation on smaller screens.

## 3. Build (7:38)

### Plan mode (7:44)

In the same conversation, Maxwell triggers **plan mode** with a slash command. "Plan
mode allows you to be the architect, outlining your approach before any code gets
written." The prompt specifies: a new **insights tab**, a high-level feature overview,
focus-keeping requirements, device-specific presentation details from the explore
phase, and a request for a preview.

- The argument Apple makes: because agents turn ideas into features so fast, *planning*
  becomes the higher-leverage step — get the plan right so refinement polishes a good
  feature rather than fixing a bad foundation. Planning becomes a **discussion**.

### Queued messages + clarifying questions (8:58)

Maxwell sends a follow-up asking for metric ideas while the agent works. This **queues**
the message — addressed once the current step finishes — so you express ideas in the
moment instead of waiting. The agent comes back with **options to choose from**; Maxwell
picks a **per-exercise view** and a **top-level summary**. Two-way: you queue
requirements, the agent asks for clarity → a stronger plan.

### Approve and implement (9:57)

The finished plan is shown as **editable markdown** to review and tweak directly.
Maxwell approves; implementation begins.

- Source-code modifications and new files appear as **artifacts** (with exact diffs) as
  they're made, so you stay in sync and review.

### Validation: build, preview, test (10:36)

- **Build** — the agent builds to check correctness; initial errors are communicated
  **directly to the agent**, which iterates and rebuilds.
- **Architecture docs** — the agent updates them with the new code to keep the knowledge
  base current.
- **Preview** — after resolving SwiftData issues, the agent renders a **preview on the
  current run destination**; previews are artifacts you can click. iPhone shows the
  concise top-level summary; running on an **iPad simulator** shows the new tables and an
  exercise breakdown.
- **Test** — Maxwell asks for unit tests on the SwiftData model changes. The existing
  test suite is picked up so new tests are grounded with the rest. The agent writes a
  suite and **runs all 12 new test cases — all pass.**

Takeaway: "Xcode provides agents with the same tools that you have" — build, preview,
test — so you can trust the code is correct while focusing on the high-level vision.

## 4. Refine (13:44)

Devin takes over to make it "look and feel exactly how we want," adding **Swift Charts**.
Visual design is subjective and changes as you work, which is why staying in the loop
for every change matters.

- **Explore chart styles** — Devin (new to Swift Charts) asks what options fit the
  insights data.
- **Preview each option on fake data** — generate previews for each chart type using
  artificial workout data, so you *look at* charts instead of imagining them. **Volume
  over time** wins.
- **Convey intent with a sketch** — Devin sketches a chart design in **Freeform on
  iPad**, then sends the sketch and asks for a line chart added to `InsightsView` matching
  the drawn style. The sketch is interpreted and translated into a matching chart.
- **Preview self-verification loop** — agents don't just generate code and stop:
  previews are **rendered incrementally to visually verify** the result matches the
  request, with adjustments made before you step in.
- **Inline annotations** — Devin leaves two annotations directly in the chart view: one
  to add a **fade-in animation**, another to **adjust the trend-line color** to match the
  app theme. An annotation carries the **exact location** plus the **surrounding code as
  context**, so changes are precise and scoped — something a plain conversation prompt
  can't convey.

Refinement toolkit: **rich previews** (realistic data), **inline annotations**
(source-grounded changes), **image attachments** (show what you mean). Pointer:
*"Create UI Prototypes using Agents in Xcode."*

## 5. Orchestrate (18:25)

Devin tackles localization and accessibility so a Filipino-speaking friend can use the
app. Xcode exposes a rich tool set from three sources: **built into Xcode**, **provided
by Apple framework teams**, and **your own**. The right tools are **discovered and used
automatically** based on the task.

- **Localization conversation** — Devin asks to translate every user-facing string into
  **Filipino** and configure the **strings catalog**. **Machine translation tools** are
  discovered. The high-level goal is broken into parallel pieces and **sub-agents** are
  deployed to locate and translate strings across the app. The translation tools are
  invoked by the sub-agents under the hood, not by Devin directly — the main conversation
  reads the tools for context, splits the work, and each sub-agent calls specific
  translation capabilities for its portion.
- **Accessibility conversation (in parallel)** — a second conversation adds **VoiceOver
  labels** and **accessibility identifiers** to all interactive elements while
  localization runs.

> "This is the power of orchestration: you describe the goal, and the right tools are
> used at the right level — some by the main workflow for planning, others by sub-agents
> for execution." You can check in on progress at any time.

### Review (20:55)

- App localized in Filipino: Start Workout button, History tab, Insights section — and
  the strings catalog has an entry for every user-facing string.
- VoiceOver demo confirms every primary element is labeled and navigable.

Hours of repetitive work done across **two parallel conversations**, with the developer
in control throughout.

## 6. Next steps (22:09)

- Download **Xcode 27** and use agents on your own project.
- Explore the agentic tools, **add your own**.
- Related sessions: **"Create UI Prototypes using Agents in Xcode"** and **"Translate
  your app with agents in Xcode."**

