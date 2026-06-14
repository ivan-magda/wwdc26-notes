---
title: "Build with the new Apple Foundation Model on Private Cloud Compute — Full Digest"
session: WWDC26 · 319
url: https://developer.apple.com/videos/play/wwdc2026/319/
duration: 11m
speakers: Louis
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 319 — Build with the new Apple Foundation Model on Private Cloud Compute

## TL;DR

This is the focused, hands-on companion to session 241's overview: how to actually wire
the new **Private Cloud Compute (PCC) server model** into an app. Headline themes:

1. **One-line upgrade.** A working on-device app is three lines; switching to the
   server model is a single line — `model: PrivateCloudComputeLanguageModel()`. The
   unified Foundation Models Swift API means Generable structured output and Tools work
   identically across both models, so nothing downstream changes.
2. **Server power, no server hassle.** PCC gives you a much larger model (32K context)
   with reasoning, but there's **no account setup, no auth, no API keys, and no token
   cost to you** — it's integrated with the OS and iCloud. Privacy is the headline: data
   is never stored, used only for the request, and independently verified.
3. **The two real decisions:** *which model* (on-device vs PCC) and *how much reasoning*
   — and Apple's repeated message is to decide **based on data, not vibes** (use the new
   Evaluations framework).
4. **Usage limits are a first-class UI concern.** PCC has a per-user daily quota (counted
   against the user's iCloud account, higher with iCloud+). Handle `isLimitReached` and
   the approaching-limit case with persistent, actionable UI — not an alert — and test
   both with an Xcode debug toggle.

Eligibility: apps with **under 2M downloads**, apply on the developer website. PCC even
works from **watchOS**.

---

## 1. Why a server model (and what improved on-device)

The on-device LLM from last year got better this year: **image input**, better
instruction following, and better custom-tool calling. But some use cases need more
horsepower — assistants that **reason over large user input**, or features that make
**lots of tool calls with large outputs**. That's the gap the PCC server model fills,
and it's reachable even from watchOS.

## 2. What Private Cloud Compute is

PCC is the same infrastructure that powers Apple's own system features sending complex
tasks to Apple's servers — now exposed to your apps.

- **Privacy by design:** user data is **never stored**, used **only for the request**,
  and the whole thing has been **independently verified by researchers**.
- **Integrated with the OS + iCloud:** no authentication, no API keys. Users just need
  an Apple Intelligence device. "The easiest server LLM you'll ever use."
- **No token cost to the developer.** Each user gets a **daily limit**; users can
  upgrade to **iCloud+** for higher limits.
- **Eligibility:** apps with **less than 2M downloads**; apply on the developer website
  today.

## 3. Integrating PCC — the one-line switch

The on-device baseline is three lines:

```swift
import FoundationModels

let session = LanguageModelSession()
let response = try await session.respond(to: "Summarize this article: \(article)")
```

Switching to PCC changes exactly one line — the model passed to the session:

```swift
import FoundationModels

let session = LanguageModelSession(
    model: PrivateCloudComputeLanguageModel()
)
let response = try await session.respond(to: "Summarize this article: \(article)")
```

Because the framework offers a **unified Swift API regardless of model**, structured
output (`@Generable`) and `Tool` calling are identical across on-device and PCC:

```swift
import FoundationModels

@Generable
struct ArticleSummary {
    let oneLineSummary: String
    let keyPoints: [String]
}

struct FindRelatedArticlesTool: Tool {

}

let session = LanguageModelSession(
    model: PrivateCloudComputeLanguageModel(),
    tools: [FindRelatedArticlesTool.self]
)

let response = try await session.respond(
    to: "Summarize this article: \(article)",
    generating: ArticleSummary.self
)
```

PCC, like the on-device model, is **only available on Apple Intelligence devices**.
Check availability and fall back gracefully:

```swift
struct ArticleSummarizationView: View {
    private var model = PrivateCloudComputeLanguageModel()

    var body: some View {
        if model.isAvailable {
            // Show UI for making request
        } else {
            // Fall back
        }
    }
}
```

## 4. On-device vs PCC — the trade-off table

Both models offer privacy. The differences that drive the choice:

| | On-device (`SystemLanguageModel`) | PCC (`PrivateCloudComputeLanguageModel`) |
|---|---|---|
| Connectivity | Works **offline** | Requires an **internet connection** |
| Request limits | **None** | **Daily limit** per user |
| Context size | **4K** | **32K** |
| Reasoning | No | **Yes** |

## 5. Reasoning — what it is and what it costs

"Reasoning" means the model **thinks before it generates the response** — literally by
generating extra text in a **separate segment of the transcript**. PCC offers **three
levels**:

- **`.light`** — gather some extra context.
- **`.moderate`** — reason a little deeper.
- **`.deep`** — the reasoning segment may be **longer than the actual response**.

Set it on `respond` via `ContextOptions`:

```swift
let response = try await session.respond(
    to: prompt,
    contextOptions: ContextOptions(reasoningLevel: .light)
)
// Reasoning levels: .light, .moderate, .deep
```

Two consequences to design around:

- The reasoning segment is part of the session **transcript** — **observe the transcript
  to show progress**, which matters most at `.deep` (it can take a while).
- Reasoning is generated text, so it **consumes tokens against your context-size limit**.

A convenient API exposes the window programmatically:

```swift
SystemLanguageModel().contextSize
// 4096 on 26.0
// 8192 on 27.0 (newer devices)

PrivateCloudComputeLanguageModel().contextSize
// 32768
```

## 6. Choose by data, not vibes — and combine models

Decide between on-device vs PCC, and the reasoning level, by **measuring with the
Evaluations framework rather than guessing**. The updated on-device model "may surprise
you" at certain tasks — but the only way to know is to evaluate.

- The new **Evaluations framework** (Swift, Xcode-integrated) measures the quality of a
  specific Foundation Models feature → see **"Meet the Evaluations framework."**
- You can use **on-device and server models together** → see **"Build agentic app
  experiences with Foundation Models."**

## 7. Handling usage limits (the demo)

The demo app summarizes a Markdown article (text + images) through a `LanguageModelSession`
on PCC — a natural fit for the 32K context. The problem: when a user hits their limit,
`respond` **throws an error**, and surfacing a raw error in the UI is not actionable.

Better: read `model.quotaUsage` and drive custom UI from it.

```swift
struct ArticleSummarizationView: View {
    private var model = PrivateCloudComputeLanguageModel()

    var body: some View {
        if case .belowLimit(let info) = model.quotaUsage.status {
            if info.isApproachingLimit {
                Text("Nearing usage limit.")
                    .foregroundStyle(Color.orange)
            }
        }
        if model.quotaUsage.isLimitReached {
            Text("Usage limit exceeded.")
                .foregroundStyle(Color.red)
        }
        if let suggestion = model.quotaUsage.limitIncreaseSuggestion {
            Button("Show options") {
                suggestion.show()
            }
        }
    }
}
```

Design guidance from the talk:

- Requests are counted against the **user's iCloud account**.
- **Don't show an alert** for the usage limit — this UI should **persist**, not be
  dismissed. Instead, update UI state (e.g. **disable the request button**) and place a
  **subtle label with an actionable button** (upgrade to a higher limit) beneath it.
- Also handle the **approaching-limit** case (`belowLimit` + `isApproachingLimit`) so
  users can make informed decisions about which requests to spend on.

**Testing:** Xcode scheme → **Debug → Options → "Simulate Apple Foundation Models
Availability"** offers **Quota Usage Limit Reached** and **Nearing Usage Limit** so you
can exercise both branches without burning real quota.

## 8. Next steps

Apply for the server model on the developer website. Related content:

- **"What's new in the Foundation Models framework"** — overview (session 241).
- **"Debug and profile agentic app experiences with Instruments"** — runtime behavior.
