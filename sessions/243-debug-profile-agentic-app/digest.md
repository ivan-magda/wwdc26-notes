---
title: "Debug and profile agentic app experiences with Instruments — Full Digest"
session: WWDC26 · 243
url: https://developer.apple.com/videos/play/wwdc2026/243/
duration: 14m
speakers: Erik (AI Tools Engineer)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 243 — Debug and profile agentic app experiences with Instruments

## TL;DR

The Foundation Models framework lets features adapt to context via **Dynamic
Instructions** (re-evaluated before every request) — and that same flexibility makes
them hard to debug. Xcode 27 ships an improved **Foundation Models template in
Instruments** to give you visibility into framework behavior in context.

Three headline themes:

1. **The LLM debugging mindset.** LLM apps add three problems traditional code doesn't have: **probabilistic output** (string-assert unit tests break), **model-to-model communication**, and **observability**. Everything in the instrument is organized around the **tool-call loop**: prompt → reason → tool call → action → response → (loop).
2. **A live root-cause hunt.** A crafting app's brainstorm→tutorial handoff silently fails. The **Instructions lane** shows only one instruction set was ever active; the **tree view** + inspector reveal the prompt references a `SwitchToTutorialMode` tool that was never added to the instruction's toolset. No error was thrown — a *silent failure*. One-line fix, re-trace, confirmed.
3. **Three performance metrics.** **Time to First Token** (shorten the prompt), **Tokens per Second** (benchmark configs / catch regressions), **Total Latency** (mask with streaming).

The instrument works with **any model** you use with the framework — on-device, Private Cloud Compute, or third-party.

---

## 1. The LLM app development mindset

Traditional code is predictable: same input, same output. LLMs are **non-deterministic** — the same prompt can produce two completely different responses. That has practical consequences:

- **Probabilistic output** — you can't `assertEqual(output, "expected string")`. You evaluate *quality and intent* instead (which is what the companion Evaluations framework session is for).
- **Model-to-model communication** — powerful features chain models (e.g. a recipe app: one model identifies ingredients in a photo, a second writes the recipe). The complexity lives in getting data to flow reliably between them and recovering gracefully on failure.
- **Observability** — when a multi-model pipeline breaks, knowing *where* it went wrong is hard. You need to see, per step: what the model received, what it decided, and why.

### The tool-call loop

At its core an LLM app is: a person sends a prompt → the model reasons → the person gets a response. For summarization, writing assistance, or Q&A that's enough. When the model needs information it doesn't have (the current time, a database record, a search result), it makes a **tool call**:

```
prompt → model reasons → tool call → tool performs action
       → model takes result → final response → (can kick off the loop again)
```

Every extra step adds latency and is a new place for failure. This loop is the basis for everything the Foundation Models Instrument shows.

## 2. The demo: a Craft companion app

A journaling app for craft projects — record progress, ask questions about specific crafts, generate tutorials. The new feature is **interactive brainstorming**: the crafter talks with the model to refine ideas, and when ready, the app generates a detailed tutorial.

Design:

- **Two sets of Dynamic Instructions** — one for brainstorming ideas, one for tutorial generation.
- The **brainstorm** instructions carry **two tools**: `GenerateCraftIdeaTool` and `SwitchToTutorialModeTool`.
- Both instruction sets use the **server model on Private Cloud Compute** — one tuned for quick idea generation, the other for more detailed tutorials.

## 3. Recording a trace

- **Product ▸ Profile** — Xcode builds the app locally.
- In the template chooser, pick the **Foundation Models** template, click **Record**.
- Warning that matters: the instrument **captures prompt and response data from the device, which can include sensitive information**. Logging is **off in production** but **on for the duration of the trace** — keep trace files somewhere safe. Click **Record Anyway** to proceed.

Running the demo: the brainstorm screen suggests Yarn PomPom, Fabric Pouch, Paper Butterfly. Picking "Paper Butterfly" should kick off a tutorial — instead the model just offered more ideas. End the recording and dig in.

## 4. Navigating the Instruments UI

Layout:

- **Tracks** (top) show activity on the timeline; each track holds multiple **lanes** with charts showing levels or regions.
- **Detail view** (below the timeline) summarizes the range you're inspecting.
- Clicking a bar or row opens the **inspector** on the right.

The Foundation Models instrument has **6 lanes**. Two carry the story:

- **Instructions lane** — how long a given set of instructions+tools was active. One set can cover multiple requests. *In the buggy trace, only one instruction set was active for the entire session* — but the feature needs two, so the handoff failed.
- **Model Inference lane** — **yellow bars** = time spent processing the input prompt; **orange bars** = time generating the response.

### The tree view (where the real power is)

Everything logged during a recording is organized into a hierarchy:

```
sessions → requests → model inferences → instructions → prompts → responses
```

- Every **model inference** should have instructions, a prompt, and either a response or an error.
- The model-inference inspector shows a summary of the instructions / prompt / response; scroll down for **duration visualizations and token-usage metrics**.

## 5. Root-causing a silent failure

Walking the tree on the broken trace:

- **Session 1** had **two requests**. The first was kicked off by the prompt starting `"Please generate 3 craft ideas."`, made up of **two model inferences and a few tool calls**.
- The Instructions node's inspector shows this instruction had **only one tool** associated with it.
- The **prompt references the `switchToTutorialMode` tool — but that tool isn't configured with this instruction.** Without it, the app has no way to leave brainstorm mode, so the crafter is stuck in a loop.
- Critically: the model kept accepting input and making tool calls but **never threw an error**. A **silent failure** — no signal anything was wrong, which is exactly what makes it hard to catch.

### The fix

In `BrainstormDynamicInstructions`, the `Instructions` block mentions `SwitchToTutorialMode` in the prompt, but the toolset lists only `GenerateCraftIdeasTool`. Add the missing tool to the toolset, recompile, re-run with Instruments.

### Confirming the fix

On the new trace:

- The **Instructions lane now shows two distinct instructions**: brainstorming first, tutorial-generation second — matching the intended design.
- In the tree, the first instruction set now includes **both** `generateCraftIdea` and `switchToTutorialMode` tools.
- The instruction change happened **after the second model inference of Request 2** — that inference produced a tool call to `switchToTutorialMode`, passing the selected craft as an argument.
- In the **following request**, the instructions correctly switched to the tutorial generator, with the selected craft carried along as context.

The **info column** is the fast path to nodes worth inspecting: it flags **errors, long durations, and large token counts**.

## 6. Performance metrics

Three metrics, surfaced in the model-inference node's metrics/duration sections:

- **Time to First Token** — time from prompt received to the first response token. High value = people staring at a blank screen. **Reduce by shortening the prompt.**
- **Tokens per Second** — overall generation speed. **Benchmark across prompt configurations and catch regressions** after changes.
- **Total Latency** — full time from sending the request to the final response. The number people feel most directly. **Reduce *perceived* latency with streaming** (surface partial results sooner).

Running a trace is where optimization starts: the metrics tell you where time and resources go and point you toward the right fix.

## 7. Requirements & next steps

- **Xcode 27** + update the profiling device to the **latest OS releases**.
- The instrument supports **any model** used with the Foundation Models framework.
- Next: **"Meet the Evaluations framework"** for measuring/improving prompt quality with structured evaluation; plus the agentic app experiences and Evaluations sessions, and the full documentation.
