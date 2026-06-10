---
title: "Secure your app: mitigate risks to agentic features — Full Digest"
session: WWDC26 · 347
url: https://developer.apple.com/videos/play/wwdc2026/347/
duration: 25m
speakers: Willy, Akshay
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 347 — Secure your app: mitigate risks to agentic features

## TL;DR

Adding an LLM to your app adds a probabilistic engine that can be *tricked*. This talk
is split in two halves:

1. **Willy — the risks and how to think about them.** The headline threat is **indirect
   prompt injection**: malicious instructions hidden in untrusted context (a calendar
   invite, a friend-feed post, a tool result) that hijack the agent's control flow.
   Combined with side-effecting actions, this produces **data poisoning** (attacker bends
   an action's *parameters*) and **action poisoning** (attacker bends *which action*
   runs). Framed via **Simon Willison's Lethal Trifecta** (private data + untrusted
   content + external communication). The defensive process is a **threat-modeling
   exercise**: data-flow-analyze your prompt to label untrusted sources, then catalog
   each action's side effects. Indirect prompt injection is an **unsolved research
   problem** — the goal is to *reduce* risk, and to **prefer deterministic mitigations**
   over probabilistic ones.

2. **Akshay — the concrete APIs.** For **Foundation Models**, use **lifecycle event
   modifiers** as deterministic security checkpoints: `.onToolCall` to gate/confirm risky
   tool calls (throw to block), and `.historyTransform` to **spotlight** untrusted tool
   output and **redact PII** before inference. For **App Intents** (Siri integration),
   the system provides **risk-based contextual confirmations** (driven by schema-derived
   risk metadata + dynamic system state) and lock-screen **`authenticationPolicy`** to
   block risky intents on a locked device.

Out of scope, stated explicitly: **model safety** (is the model's output safe) and
**guardrail circumvention**. The threat actor here is an external attacker compromising
*your* app.

Running example throughout: **Loose Leaf**, a tea social network, adding an "Organize a
tea party" feature that reads your calendar + friends, then orders teas — i.e. it pulls
untrusted context *and* takes side-effecting actions.

---

## 1. Why an attacker targets your agentic app

Apps are worth attacking because they host sensitive data, move money, access system
resources (mic/camera), or control physical devices. An LLM-driven feature is attractive
because the model is a **probabilistic decision-maker** sitting between untrusted input
and powerful actions.

The platform gives you two ways to build agentic experiences, and the talk covers both:

- **Foundation Models framework** — design your own agent.
- **App Intents framework** — let your app work with Siri / Apple Intelligence.

## 2. Indirect prompt injection

**Definition:** instructions embedded in *extra context* provided to the model with the
intent to **redirect control flow**. The injected instructions can live in:

- the initial extra context appended to the prompt, or
- a **tool result** returned mid-loop.

Loose Leaf example: the user asks to start a tea party and the app appends their
calendar; an attacker-created **calendar event** contains text telling the model to,
say, delete sensitive user data.

### Two effects of a successful injection

- **Data poisoning** — attacker influences the **parameters** of an action that *was*
  going to run. (User wants to message Mom; injection rewrites the recipient to the
  attacker.)
- **Action poisoning** — attacker influences **which action** runs. (User asks to
  summarize an email; injection makes the model open a malicious URL with the email
  appended as a query string → exfiltration.)

### The Lethal Trifecta

Apple cites **Simon Willison's Lethal Trifecta**: maximum danger when an agentic system
combines **(1) access to private data**, **(2) exposure to untrusted content**, and
**(3) the ability to externally communicate**. Apple generalizes the third leg to *any
action with a side effect*.

Important honesty note from the talk: **solving indirect prompt injection is an active
research area.** There is no complete fix — understand your exposure and mitigate it.

## 3. Threat-modeling exercise

Two passes: analyze the **prompt inputs**, then analyze the **actions**.

### 3a. Data-flow analysis of the prompt → find untrusted sources

Loose Leaf's prompt is built from:

| Source | Trusted? | Why |
| --- | --- | --- |
| Instructions (role/purpose) | trusted | authored by you |
| User prompt (the goal) | trusted-ish | the user's own request |
| Past tea orders | trusted (but may hold **PII**) | user's own data |
| Stored tea recipes | trusted | user's own data |
| **Calendar events** | **untrusted** | anyone can send the user an invite |
| **Friend feed** | **untrusted** | a "friend" can post arbitrary content |

Rule of thumb: **any input from an external entity is attack surface.** Calendar and
friend feed are the injection vectors here.

### 3b. Catalog actions → find risky side effects

| Action | Side effect / risk |
| --- | --- |
| `OrderTeaTool()` | **financial** — user loses money if called wrongly |
| `PostAndFetchPublicFeedTool()` | **data exfiltration** — model could leak info via a public post |
| `BrewingTimerIntent()` | low on its own, but a **label** field lets an injection *write instructions* for a later attack |
| Delete Photo | **data loss**, especially with no undo |

The `BrewingTimerIntent` case is the subtle one: a seemingly harmless action becomes a
vector because a free-text argument can persist attacker-controlled text that *poisons a
later context* when the data is read back.

## 4. Choosing mitigations: deterministic first

Apple's stated preference: **deterministic mitigations as the baseline**, because their
guarantees are auditable and easy to reason about. Probabilistic mitigations (anything
that relies on the model "choosing" to comply, like spotlighting) are a useful *add-on*
but not a foundation. Mitigations apply at two stages:

- **Prompt level** — redact PII; spotlight untrusted content.
- **Action-execution level** — require user confirmation; require device authentication.

Apple notes it used some of these techniques when designing Siri AI.

## 5. Foundation Models — lifecycle event modifiers as checkpoints

You build the agent normally: conform tools to the `Tool` protocol, assemble a `Profile`
(instructions + tools + model), and instantiate a `LanguageModelSession`.

```swift
struct OrderTeaTool: Tool {
  let name = "orderTeaTool"
  let description: String = "Orders a particular quantity of a tea from the store."
  // Arguments
  // Implementation
}

struct PostAndFetchPublicFeedTool: Tool {
  let name = "postAndFetchPublicFeedTool"
  let description: String = "Posts a message to the public feed."
  // Arguments
  // Implementation
}
```

```swift
class LooseLeafAgent {
  struct DefaultProfile: LanguageModelSession.DynamicProfile {
    var body: some DynamicProfile {
      Profile {
        Instructions("You are a helpful, tea-loving assistant ... ")
        OrderTeaTool()
        PostAndFetchPublicFeedTool()
      }
      .model(SystemLanguageModel())
    }
  }

  let session: LanguageModelSession

  public init() {
    self.session = LanguageModelSession(profile: DefaultProfile())
  }
}
```

**Lifecycle event modifiers** are callbacks that fire **deterministically** at specific
points in session execution — the perfect place to inject policy. The talk covers two.

### 5a. `.onToolCall` — gate the model's *output* (confirmations)

Fires when the LLM emits a tool call, **before** the executor runs the tool. **Throwing
from this callback prevents the tool from running** and returns control to the loop.
Adding the check in one place gives full coverage across all tool calls.

```swift
.onToolCall { call in
  guard call.toolName == "orderTeaTool" else {
    return
  }
  guard ConfirmationAction.confirmWithUser() else {
    throw LooseLeafError.userConfirmationDenied
  }
}
```

Here only `OrderTeaTool` (financial impact) requires confirmation; everything else
passes through. `confirmWithUser()` is your own UI.

### 5b. `.historyTransform` — rewrite the *input* (spotlighting + redaction)

Fires **before the transcript is rendered to the model for inference** — both on a new
user request and on every loop iteration. It rewrites the **tail** of the transcript.

**Spotlighting** wraps untrusted tool output in delimiter tags so the model is told "this
is untrusted." `PostAndFetchPublicFeedTool` returns public-feed posts an attacker can
write to, so its output gets delimited:

```swift
.historyTransform { entries in
  entries.map { entry in
    guard case .toolOutput(var toolOutput) = entry,
      toolOutput.toolName == "postAndFetchPublicFeedTool"
    else {
      return entry
    }
  }
  toolOutput.segments = toolOutput.segments.map { segment in
    delimit(segment: segment,
            startDelimiter: "<<UNTRUSTED>>",
            endDelimiter: "<</UNTRUSTED>>")
  }
  return .toolOutput(toolOutput)
}
```

**Redaction** uses the identical shape, swapping `delimit` for a `redactPII` that
replaces sensitive data with a placeholder so PII never reaches the model and so can't be
exfiltrated:

```swift
toolOutput.segments = toolOutput.segments.map { segment in
  redactPII(segment: segment, placeHolder: "[REDACTED]")
}
```

Both operate on `Transcript.Segment` values. **Spotlighting is probabilistic** — a
cleverly crafted injection can negate the delimiters — so it's a layer, not a guarantee.

**Scope caveat:** `.historyTransform` edits are scoped to the **current inference
iteration only**; they are *not* visible to the next call and must be re-applied. To
**persist** an expensive transform across iterations, use the **`@SessionProperty`**
annotation for stateful session-history transformations (see docs).

The framework ships **more lifecycle modifiers** at other points in the loop, and you can
**author and package your own** reusable profile modifiers.

## 6. App Intents — system guardrails for Siri integration

When an `@AppIntent` adopts an **intent schema**, it becomes a **tool in the Siri
toolbox** that the Siri model can choose to invoke. Because the *model* chooses, a prompt
injection can drive your intent toward exfiltration or destruction. Example: a
`DeletePhotoIntent` adopting `.photos.deleteAssets`. App Intents provides two guardrails.

### 6a. Risk-based, contextual confirmations

Before executing a chosen intent, the system runs a **Risk Evaluation** that combines:

- **Static risk metadata** — derived from the intent's **side effects**, and
- **Dynamic system state** — the current context.

If overall risk is **high**, the user is asked to confirm; **decline blocks execution**.

**Where risk metadata comes from:** it's attached to the **schemas**, not your code. When
your intent adopts a schema, it **inherits** that schema's side effects automatically —
no extra work. `.photos.deleteAssets` carries a **destructive** side effect, so
`DeletePhotoIntent` inherits it. Riskier categories: **delete device state**,
**exfiltrate data**, **update shared content**.

**The subtle `createTimer` case:** creating a timer looks harmless, so you might skip
confirmation. But `createTimer`'s optional **label** is a free String the *model* fills —
a prompt injection can stuff attacker-controlled text there, and a later "list timers"
query pulls that text back into context, **poisoning the new context**. This is exactly
why confirmation is **contextual**: dynamic system state decides whether a confirmation is
needed in the moment, capturing dynamic risk rather than a fixed per-action rule.

### 6b. Lock-screen authentication policy

Siri is reachable on the **lock screen**, so an attacker with physical possession of a
locked device could invoke your intent. Mitigation: require unlock before risky actions.

Custom intent — set it explicitly:

```swift
struct DeletePhotoIntent: DeleteIntent {
    var entities: [LooseLeafPhoto]

    static var authenticationPolicy: IntentAuthenticationPolicy = .requiresAuthentication

    func perform() async throws -> some IntentResult {
        // Implementation
    }
}
```

Schema-adopting intent — the policy is **inherited from the schema default** (set
internally per schema sensitivity), and you may **override only to make it stricter**:

```swift
@AppIntent(schema: .photos.deleteAssets)
struct DeletePhotoIntent {
    var entities: [LooseLeafPhoto]

    // Schema default authentication policy is .requiresAuthentication
    func perform() async throws -> some IntentResult {
        // Implementation
    }
}
```

Try to set a **weaker** policy than the schema default and you get a **build error** that
names the minimum allowed policy. Action: review every intent's lock-screen behavior.

## 7. The takeaway workflow

1. **Threat model:** data-flow-analyze the prompt to find untrusted context; rate each
   action by side effect.
2. **Mitigate**, deterministic-first, at the **prompt** stage (redact, spotlight) and the
   **action** stage (confirm, authenticate).
3. In **Foundation Models**, implement these with lifecycle event modifiers
   (`.onToolCall`, `.historyTransform`, `@SessionProperty`).
4. In **App Intents**, lean on system risk-based confirmations (schema side-effect
   metadata) and set `authenticationPolicy` for lock-screen safety.
