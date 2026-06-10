---
title: "Build agentic app experiences with the Foundation Models framework — Full Digest"
session: WWDC26 · 242
url: https://developer.apple.com/videos/play/wwdc2026/242/
duration: 22m
speakers: Erik Hornberger, Oliver O'Neill
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 242 — Build agentic app experiences with the Foundation Models framework

## TL;DR

This is the deep-dive companion to session 241's quick `DynamicProfile` teaser. Where
241 said "here's a declarative agentic primitive," 242 spends 22 minutes on how to
actually build agents with it. Five headline themes:

1. **Dynamic Profiles are the agent primitive.** A `DynamicProfile` resolves, on every
   prompt, to a single active `Profile` — a bundle of instructions + tools + config
   modifiers (model, temperature, reasoning level). Switching modes swaps the session's
   "persona" while keeping one continuous `LanguageModelSession`. Think "swapping hats,"
   not spinning up new sessions.
2. **Context engineering is now first-class.** `historyTransform` (stateless,
   non-mutating, per-request), custom `DynamicProfileModifier`s, lifecycle hooks
   (`onResponse` / `onToolCall`), and `@SessionPropertyEntry` session properties give
   you principled tools to trim, redact, summarize, and share state across a long
   session — instead of last year's strictly append-only API.
3. **Two named orchestration patterns.** *Baton-pass* (shared transcript, a tool toggles
   which profile is active, the receiver answers) and *phone-a-friend* (a tool spawns an
   isolated short-lived child session, the parent always answers). Plus a *Skills*
   pattern for procedural context loading.
4. **Tool calling mode + transcript error handling.** New knobs: `.allowed` / `.disallowed`
   / `.required` tool calling mode (with the warning that "required" is a while-loop you
   must give an exit), and `transcriptErrorHandlingPolicy` (`.revertTranscript` vs
   `.preserveTranscript`) backed by a now-mutable `session.transcript`.
5. **"Taking the training wheels off" has a cost.** Mutating history can invalidate the
   KV cache (latency) and confuse the model (accuracy). Apple's guidance: append when you
   can, measure with the Foundation Models Instrument, and quantify with the Evaluations
   framework.

All of it is demoed on an **Origami** craft app with three phases — brainstorming,
planning, reviewing — that share context but want different models and priorities. And
all of these experimental building blocks ship in a new open-source **Foundation Models
framework utilities** package, updated between OS releases.

---

## 1. The problem and the philosophy

The talk opens by naming the two problems Dynamic Profiles solve:

- **Context management** — in long-running sessions, trim or summarize the transcript to
  stay inside the model's context window.
- **Model boundaries** — when you have multiple models to choose from (now including
  `PrivateCloudComputeLanguageModel` and any `LanguageModel`-conforming model), you want
  to design around capability and cost.

Erik frames the design philosophy explicitly: "this field is changing week-to-week," so
the primitives are deliberately flexible — meant to let you build *today's* abstractions
and *tomorrow's*. That's also the rationale for shipping the experimental pieces in a
separate **Foundation Models framework utilities** open-source Swift package that updates
between OS releases, rather than baking them into the SDK.

## 2. The Origami app and "agents as configurations"

The running example is a craft app, **Origami**, that produces both origami and crochet
tutorials. Three phases:

- **Brainstorming** — user uploads an image; the app brainstorms project ideas using it
  as inspiration.
- **Planning** — generates step-by-step directions for the chosen concept.
- **Reviewing** — user uploads in-progress photos and gets technique advice.

Each phase shares context but has a unique set of priorities, and each benefits from a
different model + instructions + generation options. Apple's framing: **these
configurations are agents** — they act on the app's behalf with a particular goal and
capability set. A `DynamicProfile` lets you declare each as an individual `Profile`.

## 3. Declaring a DynamicProfile and DynamicInstructions

A `Profile` is instructions + tools + modifiers (model, temperature, samplingMode, …).
`DynamicInstructions` is the reusable, composable unit: group related instructions and
tools together, then nest them — nesting concatenates the instructions and tools. The
`OrigamiExpert` bundle is reused anywhere the app prompts about origami.

```swift
struct BrainstormFacilitator: DynamicInstructions {
    var orchestrator: CraftOrchestrator
    var body: some DynamicInstructions {
        Instructions {
            "You are a warm and friendly expert crafting brainstorm facilitator."
        }
        // Tools
        GenerateProjectTitle()
        // Conditionally include Origami knowledge
        if orchestrator.techniques.contains(.origami) {
            OrigamiExpert()
        }
    }
}
```

The conditional inclusion is the point: `OrigamiExpert`'s extra instructions and tools
only join the profile when the user is actually working on an origami project.

## 4. Configuring models per phase

The full `CraftProfile` switches on an `@Observable CraftOrchestrator`'s `mode` and
applies different models and options per branch:

```swift
struct CraftProfile: LanguageModelSession.DynamicProfile {
    var orchestrator: CraftOrchestrator
    var body: some DynamicProfile {
        switch orchestrator.mode {
        case .brainstorming:
            Profile { BrainstormFacilitator(orchestrator: orchestrator) }
                .model(orchestrator.pccLanguageModel)
                .temperature(1)
        case .planning:
            Profile { TutorialAuthor(orchestrator: orchestrator) }
                .model(orchestrator.pccLanguageModel)
                .reasoningLevel(.deep)
        case .reviewing:
            Profile { CraftCoach() }
                .model(orchestrator.systemLanguageModel)
        }
    }
}

let session = LanguageModelSession(profile: CraftProfile(orchestrator: orchestrator))
```

- **Brainstorming** uses PCC + `temperature(1)` for more creative output (broad craft
  knowledge + creative thinking).
- **Planning** uses PCC + `reasoningLevel(.deep)` — `reasoningLevel` is "a capability
  available to most server models" that controls how much the model thinks before
  responding; tutorial generation is complex, so go deep.
- **Reviewing** drops to `SystemLanguageModel` to save on unnecessary server calls.

The crucial mechanic: **the body is re-evaluated each time the model is prompted**, so as
the app's `mode` changes, the session's persona swaps from brainstorming → planning →
reviewing without ever leaving the single session. (The PCC details are deferred to
"the talk from Louis on PCC in Foundation Models.")

## 5. Transcript management — history transforms

Different models have different context limits, so when you route between PCC and the
on-device model you may need to trim entries. But context size isn't the only reason: you
can also **improve focus** by removing irrelevant entries, or **protect privacy** by
redacting sensitive entries before moving to a less private model.

The transcript is the session's representation of the model's context. `DynamicInstructions`
modifies the *instructions* entry; for the rest, you operate on a window called
**history** via `historyTransform`:

```swift
case .reviewing:
    Profile { CraftCoach() }
        .model(orchestrator.systemLanguageModel)
        .historyTransform { history in
            guard let latestResponseIndex = lastResponseEntryIndex(history) else {
                return history
            }
            let filteredHistory = history[0..<latestResponseIndex].filter { entry in
                isToolCallsOrToolOutput(entry)
            }
            return filteredHistory + history[latestResponseIndex...]
        }
```

The important property: **transforms don't permanently mutate the session's transcript.**
They're local, applied just before prompting the model, so you never lose context that
might matter later.

## 6. Custom modifiers

When a `historyTransform` gets gnarly, hide it behind a `DynamicProfileModifier` and a
`DynamicProfile` extension so any profile can reuse it:

```swift
struct DroppingToolCallsProfileModifier: LanguageModelSession.DynamicProfileModifier {
    func body(content: Content) -> some DynamicProfile {
        content.historyTransform { history in /* …same filter… */ }
    }
}

extension LanguageModelSession.DynamicProfile {
    func droppingCompletedToolCalls() -> some DynamicProfile {
        self.modifier(DroppingToolCallsProfileModifier())
    }
}
```

Apple has shipped a number of these in the utilities package already, e.g. `.rollingWindow`:

```swift
import FoundationModelsUtilities

case .reviewing:
    Profile { CraftCoach() }
        .rollingWindow(size: .entries(10))   // keep the most recent 10 entries
        .droppingCompletedToolCalls()
```

## 7. Lifecycle modifiers and session properties

Transforms are stateless. For stateful operations — e.g. **summarizing earlier entries
to reclaim context** — use lifecycle modifiers, which run imperative code at session
boundaries. `onResponse` runs after each model response (a clean lifecycle boundary):

```swift
.onResponse {
    if history.count > 50, let responseIndex = lastResponseIndex(history) {
        history = history[responseIndex...]
    }
}
```

That `history` is a **session property** — built-in, accessed via `@SessionProperty(\.history)`,
and usable as an alternative to `historyTransform` for updating the transcript. Two
caveats Apple is explicit about: the history property is **lossy**, and its changes are
reflected **across all profiles** in the session. For lossless, profile-targeted edits,
prefer `historyTransform`.

You can also declare **custom** session properties — shared state readable/writable from
any Tool or Profile:

```swift
extension SessionPropertyValues {
    @SessionPropertyEntry var summary: String?   // mutable; must have an initial value
}
```

Putting it together — summarize-and-drop on the response boundary, and feed the summary
back into instructions so dropped context isn't lost:

```swift
@SessionProperty(\.history) var history
@SessionProperty(\.summary) var summary
// …
Profile {
    TutorialAuthor(orchestrator: orchestrator)
    if let summary { Instructions { "Summary: \(summary)" } }
}
.onResponse {
    if history.count > 50, let responseIndex = lastResponse(history.prefix(40)) {
        summary = try await summarize(history[0..<responseIndex])
        history = history[responseIndex...]
    }
}
```

Oliver's summary of the section: lifecycle modifiers run code at specific points; the
`history` property updates history for *all* profiles; custom session properties store
state shared by all session components.

## 8. Orchestration: baton-pass vs phone-a-friend

Erik covers two named patterns for multi-agent orchestration.

**Baton-pass (collaboration).** Two+ profiles, typically on different models; a variable
controls which is active; each profile has a tool that sets that variable. Ask "how do I
fold a crane" while brainstorming → the brainstorm profile calls a tool to pass the baton
to the tutorial profile → the tool output signals the handoff → the **tutorial profile
produces the final answer.** Key attributes: the **full transcript is visible to both
profiles**, and the **receiver gives the final response.**

```swift
case .brainstorm:
    Profile { BrainstormInstructions(); BatonPassTool() }
        .onToolCall { orchestrator.mode = .tutorial }
        .model(orchestrator.serverModel)
case .tutorial:
    Profile { TutorialInstructions(); BatonPassTool() }
        .onToolCall { orchestrator.mode = .brainstorm }
        .model(orchestrator.systemModel)
```

**Phone-a-friend (consultation).** Also tool-driven, but instead of toggling a variable,
the tool **spawns a short-lived child session with an isolated transcript**, prompts it,
and returns the response as tool output. The child disappears; the **parent always gives
the final answer.** Key attributes: **isolated transcripts** and **parent owns the final
response** — the inverse of baton-pass.

```swift
PhoneFriendTool(
    name: "generate_title",
    description: "Generate a creative project title",
    profile: TitleProfile()
)

struct PhoneFriendTool<P: LanguageModelSession.DynamicProfile>: Tool {
    func call(arguments: GeneratedContent) async throws -> String {
        let session = LanguageModelSession(profile: profile())
        let response = try await session.respond(to: arguments)
        return response.content
    }
}
```

And a third option mentioned in passing: the utilities package houses a **`Skills`** type
for procedural context loading.

```swift
Skills(activations: activations) {
    Skill(
        name: "origami_folds",
        description: "Details about specific types of folds",
        prompt: "Valley Fold: …\nMountain Fold: …"
    )
    // …
}
```

## 9. Tool calling mode

A new knob for *when* tools run, with three options:

- **`.allowed`** (default, existing behavior) — model may call a tool or answer directly;
  use it when you don't know whether tools are needed (the common case).
- **`.disallowed`** — prevents tool calls; handy when the user is in a part of the app
  where the session's tools are irrelevant.
- **`.required`** — model can *only* call tools; useful in agentic systems where all
  actions are tools.

Set it as a profile modifier (`.toolCallingMode(.required)`) or via
`GenerationOptions(toolCallingMode:)` on `respond(to:)`.

**The warning:** required mode puts the model in a while-loop, so you must provide an exit
condition. Two approaches:

```swift
// 1) Conditionalize the mode on a variable
.toolCallingMode(state.queriedDatabase ? .disallowed : .required)
.onToolCall { state.queriedDatabase = true }
```

```swift
// 2) A final-answer tool that throws to abort the loop and return control to you
func call(arguments: Arguments) async throws -> Never {
    output = arguments.answer
    throw CancellationError()
}
```

## 10. Transcript error handling

By default, throwing from a tool (or cancelling a response) **rolls the transcript back**
to its previous state. For advanced flows — cancel partway, then resume — you need to
keep the transcript after an error. New API:

```swift
.transcriptErrorHandlingPolicy(.preserveTranscript)   // on a profile
session.transcriptErrorHandlingPolicy = .preserveTranscript   // or on a session
```

Options: `.revertTranscript` (default) and `.preserveTranscript`. With preserve, **you**
own putting the transcript back into a good state — and to enable that, `session.transcript`
is now **mutable** (`get set`). Hard rule: you may only mutate it when `isResponding ==
false`; mutating during a response is a programmer error.

## 11. Performance and accuracy — the cost of mutation

The closing section is the cautionary one. Two consequences of rewriting history:

- **Performance (KV cache).** Key-value caches are a core LLM optimization and are
  invalidated by transcript mutations. **Appending** preserves the cache and minimizes
  time-to-first-token; **rewriting** (removing entries, changing attached tools, updating
  instructions) typically triggers invalidation and raises latency. Apple notes they
  intentionally shaped last year's API to be append-only for exactly this reason — this
  year they're "taking the training wheels off." Different models cache differently; the
  only way to know is to **measure with the upgraded Foundation Models Instrument** (see
  the debugging-and-profiling video).
- **Accuracy.** Rewriting history can confuse the model. Example: a session generates
  origami names without a tool; you then add a generate-title tool and prompt for more
  ideas — the model may notice it previously produced titles *without* a tool and do that
  again instead of using the tool. Mitigation: use the **Evaluations framework** to build
  eval sets and quantify the effect of context-engineering strategies. "Data-driven
  optimization is the only way to be confident."

## 12. Next steps

Play with the sample app, explore the Foundation Models framework utilities package, and
test PCC with the revamped Xcode instrument.
