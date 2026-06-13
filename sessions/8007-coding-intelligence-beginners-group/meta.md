# Session 8007 — Coding Intelligence for Beginners Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8007/
- **Duration:** 01:01:54 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + Xcode engineering panel answering upvoted developer questions live, aimed at developers new to coding intelligence / agentic workflows.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so attributions are inferred from self-introductions, host routing, and context.

## Panel

From the self-introductions (cues 2, 16–21). Names not stated in a clear self-introduction are treated as unreliable.

- **Angelica** — host; Worldwide Developer Relations. Runs the question queue.
- **Nathan** — manages one of the Xcode intelligence teams, specifically the work on interactions with the agents and models Xcode talks to.
- **Ken** — leads the Xcode team. Identified on-air as the "Xcode themes wardrobe" person from the Platform State of the Union (wanted the "neon noir" theme). Whisper renders him "Kent" in the closing credits; same person.
- **Jerome** — product manager for developer tools, self-described "pins collector." Whisper repeatedly mangles his name mid-transcript as "Drum" / "Jrum" / "Jerome"; treat those as the same speaker.
- **Kevin** — senior engineering manager for Xcode, focused on the AI tools.

(A playful "Monsieur Fantastique" nickname is thrown out during intros (cues 5–6); not attributable to a specific panelist and not used as a roster name.)

## Description

A ~62-minute live, on-demand coding-intelligence group lab pitched at beginners. After a warm-up round on how agents have changed app development, the panel answers roughly a dozen upvoted developer questions on Xcode 27's agentic coding: discovering slash commands and skills, agent vs. chat mode, local vs. cloud models, privacy of code sent to third-party LLMs, offline/on-a-train workflows, undo/rollback, common agent mistakes beginners can't catch, learning the code an agent wrote, and keeping agents current with newly announced APIs. Recurring refrains: **ask the agent**, **file feedback at feedbackassistant.apple.com**, **use Git**, **trust but verify**, and **just try it**.

## Key topics

- **Slash commands & skills** — `/plan` and others come from the agent provider plus Xcode-loaded skills; the agent itself knows nearly every command, so ask it.
- **Xcode's edge over external agents** — curated tool set, project understanding, SDK/API documentation tools, validation tools (full + incremental builds, code execution, UI rendering/interaction), preview system handed to the agent, plus Apple-ecosystem data (crash data, localization), good onboarding and planning mode.
- **Agent context & working directories** — workspace references are visible automatically; paste file paths or point the agent at a directory for out-of-workspace files; permission prompting by default, plus an early-preview "managed security" mode that gates at the file-system level.
- **Agent vs. chat mode** — chat exposes ~10–15 tools; agent mode exposes ~60 tools inside Xcode plus command-line tools, sub-agent spawning, context management. Time horizon: code completion (seconds) → chat (~30s) → agents (an hour-plus).
- **Local vs. cloud models** — mix and match agent + model; local agents via ACP get the same Xcode tool set; biggest gap is raw model capability on a MacBook vs. a hosted GPU cluster; lean harder on guardrails/tests.
- **Privacy** — Apple is not in the loop; requests go directly to the model provider, governed by that provider's terms/account settings (training opt-out). Exception: clicking the Feedback Assistant button shares the included context with Apple.
- **Offline / on-a-train** — MLX for the biggest local model; strong unit-test guardrails; plan with a big model before going offline, document assumptions locally, re-check with a big model when back online.
- **Undo / rollback** — ask the agent to revert; Xcode keeps per-turn history (requires an initialized Git repo) with a revert slider; Git is the larger safety net. "Please use Git."
- **Common beginner-invisible mistakes** — missed assumptions, unmaintainable output, incomplete discovery, and "cheating" (rationalizing a failing test). "If you wouldn't accept that answer from a co-worker, don't accept it from a machine."
- **Learning what the agent wrote** — ask why; `AGENTS.md` to auto-append explanations; parallel conversations to learn while building; plan mode to understand approach before code changes.
- **Keeping agents current with new APIs** — Xcode ships on-device documentation (human-readable + model-optimized) as a separate, auto-updating asset; documentation tool feeds any provider; Xcode also ships skills/specialists (e.g., the iOS 27 resizability feature). Foundation Models cited as having no specialist yet because the doc index alone works well.

## Related sessions (referenced on-air, by spoken title)

- [ ] "Xcode Agents and You" — agent workflow ideas, illustrative diagrams of what the agent is doing; exploring projects/features, sub-agent orchestration (e.g., translation). Session number not stated on-air.
- [ ] "Run Local Agentic AI on Mac Using MLX" — running larger local models for offline / privacy-first coding. Session number not stated on-air.

## Chapter summary

No published chapters (group lab). Rough arc: intros (00:00:03) → warm-up "how agents changed app development" (00:01:39) → developer Q&A (00:05:20) → final API-currency question pulled from an earlier lab (00:57:29) → wrap-up (01:01:00).

## Code

See `code.md` — no Code tab for this session. This lab is verbal Q&A; no code shown on screen. Feature/tool names are captured in `digest.md`.
