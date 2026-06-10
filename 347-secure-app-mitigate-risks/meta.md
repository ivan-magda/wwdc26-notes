# Session 347 — Secure your app: mitigate risks to agentic features

- **URL:** https://developer.apple.com/videos/play/wwdc2026/347/
- **Duration:** 25m
- **Speakers:** Willy (risks + threat modeling), Akshay (APIs + mitigations)

## Description

Agentic features introduce new security risks. This talk explains how to identify
those risks — chiefly indirect prompt injection and risky tool/action side effects —
and walks through a threat-modeling exercise for an example "Loose Leaf" tea social
app. It then shows concrete APIs to mitigate them: Foundation Models lifecycle event
modifiers (`.onToolCall`, `.historyTransform`) for confirmation, spotlighting, and PII
redaction; and App Intents guardrails (risk-based contextual confirmations and lock
screen `authenticationPolicy`).

Explicitly out of scope: model safety (whether the model's *output* is safe) and model
guardrail circumvention. The focus is an external attacker trying to compromise *your*
app.

## Key topics

- **Indirect prompt injection** — instructions embedded in untrusted context (calendar invites, friend feed, tool outputs) that redirect the agent's control flow
- **Data poisoning vs action poisoning** — attacker influences an action's *parameters* vs influences *which action* runs
- **Simon Willison's Lethal Trifecta** — private data access + untrusted content + external communication (generalized to any side-effecting action)
- **Threat modeling** — data-flow analysis to label untrusted prompt sources; catalog each action's side effects (financial, exfiltration, data loss, instruction-write-back)
- **Deterministic > probabilistic mitigations** — prefer auditable deterministic checks; spotlighting is probabilistic
- **Foundation Models lifecycle event modifiers** — `.onToolCall` (gate tool execution by throwing) and `.historyTransform` (spotlight/redact transcript tail before inference)
- **`@SessionProperty`** — persist expensive history transformations across iterations
- **App Intents risk-based confirmations** — static risk metadata (from adopted schema side effects) + dynamic system state → contextual confirmation
- **App Intents `authenticationPolicy`** — `.requiresAuthentication` to block risky intents on the lock screen; schema defaults can only be overridden stricter

## Related sessions to fetch (referenced in this talk)

- [ ] Model safety talk (referenced at [02:01], "what this talk is not covering")
- [ ] Prior threat-modeling / traditional-feature security talk (referenced at [03:04])
- [ ] Foundation Models framework intro / agents talk (referenced at [12:32])
- [ ] App Intents and App Schemas basics sessions (referenced at [18:11])

## Chapter summary (Summary tab)

- **0:00 Introduction** — Willy & Akshay; agentic features add security risk. Platform offers two routes: Foundation Models (build your own agent) and App Intents (work with Siri). Out of scope: model safety and guardrail circumvention.
- **2:06 Risks** — why attackers target your app (sensitive data, money, mic/camera, physical devices). Loose Leaf example app + new "Organize a tea party" feature. Indirect prompt injection; data poisoning vs action poisoning; Lethal Trifecta.
- **6:32 Threat modeling** — data-flow analysis of the prompt; mark calendar + friend feed as untrusted; enumerate actions (OrderTea, PostAndFetchPublicFeed, BrewingTimer, DeletePhoto) and their side effects.
- **11:56 Implementing mitigations** — concrete tools to secure the agentic app.
- **12:03 Foundation Models** — lifecycle event modifiers as security checkpoints: `.onToolCall` for confirmations, `.historyTransform` for spotlighting + PII redaction; `@SessionProperty` to persist transforms.
- **17:55 App Intents** — risk-based contextual confirmations (schema-derived risk metadata + dynamic state) and lock-screen `authenticationPolicy`.

## Code

See `code.md` — 8 snippets extracted from the Code tab.
