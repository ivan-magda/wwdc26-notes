# Session 319 — Build with the new Apple Foundation Model on Private Cloud Compute

- **URL:** https://developer.apple.com/videos/play/wwdc2026/319/
- **Duration:** 11m
- **Speakers:** Louis

## Description

Access a powerful new server LLM in your apps using Private Cloud Compute (PCC).
The on-device model also improves this year (image input, better instruction
following and tool calling), but PCC unlocks more complex features: reasoning over
large user input and many tool calls with large outputs — even from watchOS. PCC
delivers server-class capability without compromising privacy (data never stored,
used only for the request, independently verified), is integrated with the OS and
iCloud (no auth, no API keys, no developer token cost), and switching to it from the
on-device model takes a single line of code thanks to the unified Foundation Models
Swift API.

## Key topics

- `PrivateCloudComputeLanguageModel` — server LLM via PCC; one-line switch from `SystemLanguageModel`; same unified Swift API (Generable structured output + Tools work identically)
- Privacy model — data never stored, used only for the request, independently verified by researchers; integrated with OS + iCloud so no authentication / API keys
- Pricing & eligibility — no developer token cost; per-user daily limit (higher with iCloud+); apps under 2M downloads; apply on the developer website
- On-device vs PCC trade-offs — offline + no request limit + 4K context vs internet-required + daily limit + 32K context + reasoning
- Reasoning — three levels (`.light`, `.moderate`, `.deep`); generated as extra transcript text that consumes tokens against the context limit; set via `ContextOptions(reasoningLevel:)` on `respond`; observe the transcript to show progress
- `contextSize` property on `SystemLanguageModel` / `PrivateCloudComputeLanguageModel`
- Usage limits — `model.quotaUsage` with `isLimitReached`, `belowLimit(info)` / `isApproachingLimit`, `limitIncreaseSuggestion.show()`; persistent actionable UI (not an alert)
- Xcode "Simulate Apple Foundation Models Availability" debug option (Quota Usage Limit Reached / Nearing Usage Limit)
- Availability — PCC only on Apple Intelligence devices; check the availability / `isAvailable` API and fall back gracefully

## Related sessions to fetch (referenced in this talk)

- [ ] Meet the Evaluations framework
- [ ] Build agentic app experiences with Foundation Models
- [ ] What's new in the Foundation Models framework
- [ ] Debug and profile agentic app experiences with Instruments

## Chapter summary (Summary tab)

- **0:00 Introduction** — Louis; access a new server LLM via PCC. The on-device model also improves (image input, better instruction following + tool calling), but PCC enables more complex features: reasoning over large input, many tool calls with large outputs, even from watchOS.
- **1:23 What is Private Cloud Compute** — delivers a powerful server model without compromising privacy: data never stored, used only for the request, independently verified. Integrated with OS + iCloud, so no auth or API keys, no token cost to developers, a daily per-user limit (higher with iCloud+), and eligibility for apps under 2M downloads.
- **2:43 Integrating PCC with Foundation Models** — prompting the on-device model takes three lines; switching to PCC changes just one. The unified Swift API means Generable structured output and tool calling work identically, so you can switch models without rewriting code; check the availability API for non-Apple Intelligence devices.
- **4:00 Deciding between on-device and PCC** — both offer privacy, but on-device works offline with no request limits and a 4K context, while PCC needs a connection, has a daily limit, offers a 32K context, and supports reasoning.
- **4:32 Reasoning levels and context size** — reasoning lets the model think before responding by generating extra transcript text, at three levels (light, moderate, deep). Set it on `respond`, observe the transcript to show progress, and remember reasoning consumes tokens against the context limit — now readable via the `contextSize` property.
- **6:15 Evaluating and combining models** — choose models and reasoning levels based on data, not vibes; the updated on-device model may surprise you. Use the new Evaluations framework (see "Meet the Evaluations framework") and combine on-device and server models (see "Build agentic app experiences with Foundation Models").
- **7:10 Handling usage limits** — handle the per-user iCloud quota gracefully: check `isLimitReached` on `quotaUsage` and show persistent, actionable UI (e.g. a disabled button with an upgrade option) rather than an alert. Detect the approaching-limit case too, and use Xcode's Simulate Apple Foundation Models Availability debug option to test both states.
- **10:15 Next steps** — apply for the server model on the developer website; explore "What's new in the Foundation Models framework" for an overview and "Debug and profile agentic app experiences with Instruments" for runtime behavior.

## Code

See `code.md` — 7 snippets extracted from the Code tab.
