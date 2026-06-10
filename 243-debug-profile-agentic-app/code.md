# Code samples — Session 243

_No Code tab for this session._ This is a tooling/demo session — the work happens in
Instruments and Xcode, not in published snippets.

## Spoken APIs and identifiers

Concrete names referenced in the demo (Craft companion app), useful for grounding any
reproduction:

- **`DynamicInstructions`** — specifies which instructions and tools the model can access; re-evaluated before every request.
- **`BrainstormDynamicInstructions`** — the demo's brainstorm-mode instruction definition. The bug: its `Instructions` block referenced `SwitchToTutorialMode` in the prompt text, but its toolset only listed `GenerateCraftIdeasTool`. Fix = add `SwitchToTutorialMode` to the toolset.
- **`GenerateCraftIdeaTool`** / **`GenerateCraftIdeasTool`** — brainstorm tool that produces craft suggestions. (Transcript uses both singular and plural spellings at [04:44] and [09:47].)
- **`SwitchToTutorialModeTool`** / **`switchToTutorialMode`** — tool that transitions from brainstorm mode to tutorial generation, passing the selected craft as an argument.
- Tutorial-generation instruction set — second `DynamicInstructions`, also backed by the **server model on Private Cloud Compute**.

## Instruments objects (tree-view hierarchy)

Logged objects exposed by the Foundation Models instrument, top to bottom:

```
Session
└── Request
    └── Model Inference
        ├── Instructions   (+ associated tools)
        ├── Prompt
        └── Response | Error
        └── Tool Call(s)
```

- **Timeline lanes:** Instructions lane (how long a set of instructions+tools was active; one set can span multiple requests) and Model Inference lane (yellow = input-prompt processing time, orange = response-generation time). Six lanes total.
- **Inspector** opens on a clicked bar/row; the Model Inference node shows instructions/prompt/response summary plus duration visualizations and token-usage metrics.
- **Info column** flags errors, long durations, and large token counts.

## Performance metrics named

- **Time to First Token** — time from prompt received to first response token. High value = blank-screen wait. Reduce by shortening the prompt.
- **Tokens per Second** — overall generation speed. Use to benchmark across prompt configurations and catch regressions.
- **Total Latency** — full send-to-final-response time; the number people feel. Reduce *perceived* latency with streaming.
