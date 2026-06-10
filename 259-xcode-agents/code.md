# Code samples — Session 259

_No Code tab for this session._ This is a demo-driven session about Xcode's agent
UI and workflow, so there are no developer-authored snippets. What follows are the
concrete tools, affordances, and gestures named in the transcript.

## Spoken APIs / tools / affordances

- **Coding agents** in Xcode 27 (first shipped Xcode 26.3)
- **New conversation** — Option+Shift+click the toolbar button to open a conversation as a separate editor pane (so files stay visible); also via the coding assistant in the sidebar → "new conversation" → choose an agent
- **Transcript** (left pane) — conversation, progress commentary, tool calls, running sub-agents
- **Artifacts** (right pane) — created files, edits, diffs, and rendered previews; view latest message or whole conversation
- **Walkthrough** — agent-generated project overview: data-flow outlines, tables of key areas, source-code references (clickable links to jump into files)
- **Architecture documents** — agent writes findings as docs committed alongside source; kept up to date as code changes (a "dynamic knowledge base")
- **Apple Document Search** — agent tool that pulls current Apple framework documentation; invoked automatically when relevant (e.g. SwiftUI table support)
- **Plan mode** — entered with a `/plan` slash command; produces a reviewable/editable markdown plan you approve before implementation
- **Queued messages** — send a follow-up while the agent is working; it's addressed after the current step
- **Agent clarifying questions** — agent asks you to choose among options (e.g. per-exercise view vs. top-level summary)
- **Build tool** — agent builds; build errors are fed back so it can iterate
- **Preview rendering** — agent renders SwiftUI previews on the current run destination; previews appear as artifacts; used as a self-verification loop
- **Test tools** — agent writes new tests and runs existing suites; demo: 12 new test cases passed
- **Image / sketch attachments** — attach images, sketches (Freeform), documents to convey design intent
- **Inline annotations** — annotate a specific source line; the annotation carries the exact location plus surrounding code as context for scoped changes
- **Orchestration** — describe a high-level goal; tools are auto-discovered; work is split into parallel pieces and dispatched to **sub-agents**
- **Machine translation tools** — discovered for localization; invoked by sub-agents to translate user-facing strings and configure the **strings catalog**
- **Accessibility** — agent adds VoiceOver labels and accessibility identifiers to interactive elements
- Tool sources: built into Xcode, provided by Apple framework teams, or added by you

## Frameworks named (subject of the demo app, not API snippets)

- **SwiftUI** — tables, view hierarchy
- **SwiftData** — model relationships adjusted for insights
- **Swift Charts** — volume-over-time line chart for the insights view
