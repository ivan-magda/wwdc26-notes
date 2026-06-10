# Session 243 — Debug and profile agentic app experiences with Instruments

- **URL:** https://developer.apple.com/videos/play/wwdc2026/243/
- **Duration:** 14m
- **Speakers:** Erik (AI Tools Engineer)

## Description

How to use the **Foundation Models template in Instruments** (Xcode 27) to debug and
profile agentic features built with the Foundation Models framework. The session frames
the LLM-development mindset (probabilistic output, model-to-model communication,
observability), then walks a live demo: a crafting companion app whose brainstorm →
tutorial handoff silently fails because a Dynamic Instructions set is missing a tool.
The presenter uses the instrument's timeline lanes and tree view to find the root cause,
fixes it, re-traces to confirm, and closes on three performance metrics for optimizing
LLM experiences.

## Key topics

- Three challenges unique to LLM apps: **probabilistic output** (non-deterministic, breaks string-assert unit tests), **model-to-model communication**, **observability**.
- The core LLM **tool-call loop**: prompt → reason → tool call → action → final response → (loop). Each step adds latency and a new failure point.
- **Dynamic Instructions** — re-evaluated before every request; specifies which instructions and tools the model can access.
- The **Foundation Models Instrument**: profile via Product ▸ Profile, pick the Foundation Models template, "Record Anyway" (traces capture sensitive prompt/response data — store safely).
- Instruments UI anatomy: **tracks → lanes** (6 lanes incl. **Instructions lane** and **Model Inference lane** with yellow = input-prompt processing, orange = response generation), **detail view**, **inspector**, and the **tree view** (sessions → requests → model inferences → instructions → prompts → responses).
- Debugging a **silent failure**: instruction set never switched because `SwitchToTutorialMode` was named in the prompt but not added to the toolset; the model looped, no error thrown.
- The **info column** flags errors, long durations, and large token counts.
- Three performance metrics: **Time to First Token** (shorten prompt), **Tokens per Second** (benchmark / catch regressions), **Total Latency** (reduce perceived wait with streaming).
- Instrument supports **any model** used with the framework (on-device, PCC, third-party).

## Related sessions to fetch

- [ ] What's new in the Foundation Models framework (session 241 — already in workspace)
- [ ] Build agentic app experiences with the Foundation Models framework
- [ ] Meet the Evaluations framework

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of how the Foundation Models Instruments template helps debug and profile agentic experiences, including Dynamic Instructions and tool-call loops.
- **1:57 LLM app development mindset** — The three challenges: probabilistic output, model-to-model communication, observability. Recap of the prompt → reason → tool call → response loop.
- **3:59 Inspect and diagnose an agentic experience** — The Craft companion demo app: a journaling app with interactive brainstorming using two sets of Dynamic Instructions (idea generation + tutorial creation), both on the server model via Private Cloud Compute.
- **5:02 Recording a trace with Instruments** — Product ▸ Profile, pick the Foundation Models template, Record; note on sensitive prompt data in trace files ("Record Anyway").
- **6:04 Navigating the Instruments UI** — Tracks/lanes (Instructions lane, Model Inference lane with yellow/orange bars), detail view, inspector, and the tree view for inspecting sessions, requests, inferences, and tool calls.
- **12:07 Performance metrics** — Time-to-first-token (shorten prompts), tokens-per-second (benchmark across configs), total latency (reduce perceived wait with streaming).
- **13:04 Next steps** — Recap; requirements (Xcode 27 + latest OS); pointers to the Evaluations framework and Agentic App Experiences sessions.

## Code

See `code.md` — no Code tab for this session; APIs/identifiers named in the talk are listed there.
