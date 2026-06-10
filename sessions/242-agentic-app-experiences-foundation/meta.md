# Session 242 — Build agentic app experiences with the Foundation Models framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/242/
- **Duration:** 22m
- **Speakers:** Erik Hornberger, Oliver O'Neill

## Description

A deep dive into Dynamic Profiles, the new declarative agentic API in the Foundation
Models framework. Learn how to switch models, instructions, and tools within a single
`LanguageModelSession`; manage long-running context with history transforms, custom
modifiers, lifecycle hooks, and session properties; orchestrate multiple agents with
the baton-pass and phone-a-friend patterns; control tool execution with tool calling
mode; handle transcript errors; and weigh the performance (KV cache) and accuracy
trade-offs of mutating history. Also introduces the open-source Foundation Models
framework utilities package.

## Key topics

- **`DynamicProfile`** — declare individual `Profile`s (each an agent: instructions + tools + modifiers); body re-evaluated on every prompt so the session's "persona" swaps by mode
- **`DynamicInstructions`** — reusable, composable bundles of instructions + tools (nesting concatenates them); e.g. an `OrigamiExpert` reused anywhere
- **Per-profile config modifiers** — `.model(_)`, `.temperature(_)`, `.reasoningLevel(_)`, `.samplingMode`, mixing PCC and on-device models per phase
- **Transcript management** — `historyTransform` (stateless, per-request, non-mutating window transforms, e.g. dropping completed tool calls); custom `DynamicProfileModifier` to hide transform complexity; ready-made modifiers like `.rollingWindow(size:)` in the utilities package
- **Lifecycle modifiers** — `onResponse` / `onToolCall` run imperative code at session boundaries (UI updates, mode changes, history mutation, summarization)
- **Session properties** — built-in `\.history` (lossy, shared across all profiles) + custom `@SessionPropertyEntry` values (e.g. a conversation `summary`) shared across tools and profiles
- **Orchestration patterns** — baton-pass (shared transcript, a tool toggles the active profile; receiver gives final answer) vs. phone-a-friend (isolated child session via a tool; parent always gives final answer)
- **Skills** — procedural-context-loading pattern in the utilities package
- **Tool calling mode** — `.allowed` (default) / `.disallowed` / `.required`; as a profile modifier or `GenerationOptions`; required = while-loop, so provide an exit condition (conditionalize the mode, or a final-answer tool that throws)
- **`transcriptErrorHandlingPolicy`** — `.revertTranscript` (default) vs `.preserveTranscript`; with preserve, `transcript` is now mutable but only when `isResponding == false`
- **Performance & accuracy** — appending preserves the KV cache; rewriting history invalidates it and can confuse the model; measure with the Foundation Models Instrument and quantify with the Evaluations framework
- **Foundation Models framework utilities** — open-source Swift package, updated between OS releases, housing the experimental/emerging patterns above

## Related sessions to fetch (referenced in this talk)

- [ ] PCC in Foundation Models (the "talk from Louis" on `PrivateCloudComputeLanguageModel`)
- [ ] What's new in the Foundation Models framework (session 241 — companion intro to `DynamicProfile`)
- [ ] Debugging and profiling (Foundation Models Instrument / detecting cache invalidations in Instruments)
- [ ] Evaluations framework (sessions)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Erik Hornberger & Oliver O'Neill introduce Dynamic Profiles and the problems they solve (context management, model boundaries) plus the new open-source Foundation Models framework utilities package. Agenda: dynamic profiles, orchestration patterns, performance and accuracy.
- **2:47 The example app and agents** — the Origami craft app with three phases (brainstorming, planning, reviewing) that share context but have different priorities. Each phase becomes an agent: a configuration with its own model, instructions, and tools.
- **3:47 Declaring a dynamic profile** — a `DynamicProfile` declares individual `Profile`s. Build the brainstorming profile from an Observable orchestrator, instructions, and tools, conditionally adding capabilities for origami projects.
- **4:45 Dynamic instructions** — `DynamicInstructions` groups related instructions and tools into a single reusable, composable component. Nesting concatenates instructions and tools, e.g. an `OrigamiExpert` reused wherever needed.
- **5:36 Configuring models per phase** — assign different models and options per profile: PCC with temperature and deep `reasoningLevel` for brainstorming and planning, `SystemLanguageModel` for reviewing. The profile body is re-evaluated on each prompt, swapping the persona by mode.
- **7:21 Transcript management and history transforms** — trim or redact the transcript to stay within context limits, keep the model focused, or protect privacy. `historyTransform` applies stateless, per-request transforms over the history window (such as dropping tool calls) without mutating the session.
- **8:50 Custom modifiers** — hide transform complexity behind a type conforming to `DynamicProfileModifier`, exposed through a `DynamicProfile` extension for reuse, alongside the ready-made history-management modifiers in the utilities package.
- **9:39 Lifecycle modifiers and session properties** — `onResponse` and other lifecycle modifiers run imperative code at session boundaries to update UI, profile state, or history. Session properties (built-in `history` plus custom `@SessionPropertyEntry` values) share state across tools and profiles, e.g. storing a conversation summary.
- **12:52 Orchestration: baton-pass** — a collaboration pattern: multiple profiles share the full transcript, and a tool toggles which profile is active. The profile that receives the baton produces the final response.
- **14:06 Orchestration: phone-a-friend and skills** — a consultation pattern: a tool spawns a short-lived child session with an isolated transcript, and the parent profile always gives the final answer. Also notes the Skills pattern in the utilities package for procedural context loading.
- **15:18 Tool calling mode** — control when tools run via `.allowed`, `.disallowed`, or `.required` (as a profile modifier or generation option). When required, the model loops, so ensure an exit condition by conditionalizing the mode or using a final-answer tool that throws to break out.
- **17:12 Transcript error handling** — by default a thrown tool error or cancellation reverts the transcript; the new `transcriptErrorHandlingPolicy` (`.revertTranscript` or `.preserveTranscript`) keeps it. With preserve, the now-mutable transcript is yours to fix, only when `isResponding` is false.
- **18:27 Performance, accuracy, and evaluations** — transcript mutations can invalidate key-value caches and raise latency; appending preserves them. Rewriting history can also confuse the model, so measure with the Foundation Models Instrument and quantify changes with the Evaluations framework.
- **21:24 Next steps** — try the sample app, explore the Foundation Models framework utilities, and measure performance with Private Cloud Compute and the revamped Xcode instrument.

## Code

See `code.md` — 17 snippets extracted from the Code tab.
