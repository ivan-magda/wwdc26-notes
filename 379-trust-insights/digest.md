---
title: "Meet Trust Insights — Full Digest"
session: WWDC26 · 379
url: https://developer.apple.com/videos/play/wwdc2026/379/
duration: 14m
speakers: Mike Armstrong
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 379 — Meet Trust Insights

## TL;DR

`TrustInsights` is a **new iOS 27 framework** that gives your app a privacy-preserving
*behavioral* signal for detecting social engineering — scams where a user is being
**coached or coerced** into performing a legitimate, authenticated action.

The core idea: **authentication confirms *who* is acting, but not whether they are
acting freely.** MFA and biometrics don't help when the victim is the one tapping
the buttons under a scammer's real-time guidance (tech-support scams, bank/government
impersonation, family-emergency fraud, AI deepfakes). Trust Insights adds the missing
"are they acting under duress?" dimension.

Headline themes:

1. **One insight today:** `IsLikelyBeingCoachedInsight`, with a three-valued outcome — `unknown` / `medium` / `high`.
2. **Client-side Swift API, hybrid backend.** Integration is entirely client-side, but the framework combines on-device processing with cloud infrastructure. Needs an **entitlement**, **Internet reachability**, and takes a couple of seconds per call.
3. **Privacy-first.** Inputs are discarded immediately; only a **single output value** leaves the device. It looks at interaction patterns, timing, context, and basic sensor data — **never Photos/Messages/Mail content**.
4. **A required feedback loop.** Real-time `reportConsumption` is **mandatory** (skip it and you get rate-limited); offline fraud labels via Apple Business Register are recommended.
5. **A decision input, not a verdict.** Apple is emphatic: never the sole determinant, never block on it alone, never treat `unknown` as "low risk."

---

## 1. The problem — coercion defeats authentication

Social engineering targets *people, not systems*. The user is pressured, frightened,
or deceived into performing a legitimate action, and the app can't tell genuine intent
from coerced intent.

Recurring patterns called out:

- **Tech-support scams** — fake alerts prompt remote access, handing over control.
- **Authority impersonation** — posing as banks, government, or law enforcement to collect sensitive info.
- **Family-emergency fraud** — urgent money requests exploiting emotional bonds, increasingly with AI deepfakes.

The hard case is **real-time coaching**: attackers walk victims through actions over a
call/chat, and the victim performs them — authenticated and legitimately. MFA and
biometrics are useless here because the legitimate user is the one acting. What's
needed is **behavioral context** that distinguishes free intent from a coerced action,
while preserving privacy.

## 2. Generating insights

Integration is entirely client-side via a Swift API, over a combined device + cloud
architecture.

**Configuration first:** generating Trust Insights requires an **entitlement**,
declared as a capability on the app target in Xcode.

Then build a parameter pack of requested insights and run them through an evaluator:

```swift
import TrustInsights

let request = IsLikelyBeingCoachedInsight.request(schema: .version1, modelVersion: .current)
let context = InsightEvaluator.InsightContext(operationCategory: .resourceUse,
                                              requestedEvaluations: request)

let evaluator = InsightEvaluator()
guard try await evaluator.requestAuthorization(for: context) == .authorized else { return }

let assessment = try await evaluator.requestEvaluation(context: context)
do {
    try handleAssessment(assessment)
} catch {
    // Handle error
}

assessment.reportConsumption(.usedIncreasedFriction)
```

Notes on each piece:

- **`schema` is required; `modelVersion` is optional.** Specifying both a *current* and a *prior* version of the same insight supports **model governance and validation** (sample a new model alongside the old before you trust it).
- **`operationCategory`** tells the system what kind of action the user is performing and **determines which model logic is applied**. Five categories:
  - **`payment`** — any exchange of assets, content, or money, including in-game purchases.
  - **`account`** — updating account details or security information.
  - **`resourceUse`** — requests to costly/constrained infrastructure, e.g. AI inference.
  - **`communication`** — sending messages, submitting forms, or signing documents.
  - **`other`** — fallback; if your use case lands here, file feedback via Feedback Assistant.
- **Authorization:** the user has full control, so check `requestAuthorization(for:)`. If not authorized, you may want to notify the user.
- **`requestEvaluation` is async, can take a couple of seconds, and requires Internet reachability.** Place the call where you already have animations or interstitial screens to hide the latency.
- **Sandbox vs production:** in development, requests hit a sandbox environment; once on the App Store they're evaluated by production models/servers. You can **override insight values and errors** by customizing Xcode build-scheme launch arguments to test decision logic and UX (see the Trust Insights docs for the available arguments).

### The outcome — three values

The response contains one result per requested evaluation. For
`IsLikelyBeingCoachedInsight`:

```swift
func handleAssessment(_ assessment: InsightEvaluation<IsLikelyBeingCoachedInsight>) throws {
    switch try assessment.insight.outcome.get() {
        case .unknown:

        case .medium:

        case .high:

        @unknown default:

    }
}
```

- **`unknown`** — no evidence of scam risk, **but do not interpret this as low risk.**
- **`medium`** — some evidence of coaching risk. Consider introducing friction, additional verification, or adjusting risk scoring.
- **`high`** — significant evidence. The user should be informed of the determined risks before proceeding.

Handle **evaluation-level** and **insight-level** errors independently (they carry
different meaning). Behind these three values sits a sophisticated ML model:
device-sourced data is processed locally, inputs are discarded immediately after
evaluation, and **only a single output value leaves the device**. The final output may
incorporate **Apple Account signals** and **velocity checks** for added context.

## 3. Feedback requirements

Two feedback types complete the integration:

**Real-time consumption feedback** — call `reportConsumption(_:)` on the result.
**Mandatory for every evaluation request**; if omitted, your app may be rate-limited.
Six values report how your app responded:

- **`usedReducedFriction`** — the insight made the operation easier.
- **`usedUnchangedFriction`** — evaluated but didn't change the experience.
- **`usedIncreasedFriction`** — led to additional checks/friction. (Outright blocking on a trust insight alone is *not* recommended.)
- **`notUsedNotNeeded`** — the user cancelled; no decision required.
- **`notUsedError`** — a technical failure prevented use, e.g. the result arrived too late.
- **`usedEvaluationOnly`** — used for internal evaluation/benchmarking without affecting UX.

**Offline feedback (fraud labels)** — when an evaluated transaction *later* proves
fraudulent, that label helps the model learn its real-world performance. These reports
may arrive days, weeks, or months later, and are submitted through **Apple Business
Register** via a **server-to-server API** with a defined schema that includes the
**insight identifier** from the original evaluation. Don't include surplus info such as
PII, and apply privacy-preserving techniques to anything that could be used for
fingerprinting. Offline labels aren't required to benefit from Trust Insights but they
strengthen the ecosystem.

## 4. Privacy architecture

**Data minimization is central.** The framework processes only what's needed, discards
inputs immediately, and keeps all device-sourced data on the device.

- It analyzes **interaction patterns, timing, context, and basic sensor data** — **never** content within Photos, Messages, or Mail.
- None of these device-derived signals are shared with Apple or third parties.
- Users have **full control** and can disable Trust Insights in **Settings**. A **cooldown period** may apply after disabling — specifically to protect users who may have been *coached into turning it off*.
- Query authorization status to check whether the user has Trust Insights enabled for your app.

## 5. Best practices & an example

**Worked example:** a user sets up a large money transfer to "a doctor treating a
family member." The app has quietly requested a trust insight; a `.medium` result
prompts it to **display a warning and add a delay** to the transaction. Alternatives:
handle the result server-side, add a manual review step, or adjust risk without
disrupting the user — the right approach depends on your app, users, and product.

**Where it adds the most value** (call it at the moments that matter):

- **High-value financial transactions** — e.g. peer-to-peer payments.
- **Irreversible actions** — account deletion, personal data export.
- **Permission grants** — remote access, new device authorization.
- **Sensitive data sharing** — credentials, personal documents.

**Other best practices:**

- Integrate into your **existing** risk/decision logic; it should **not be the sole factor** in any decision.
- **Sample different model versions over time** to understand how newer models affect your decisioning before acting on them.
- **Handle errors at every level** — evaluation vs insight errors mean different things.
- **Never treat `unknown` or a missing value as low risk.**
- To avoid rate limits, **always submit real-time feedback in-app**, and contribute offline fraud labels via Apple Business Register where possible.

## 6. Next steps

Identify the moments where Trust Insights can work alongside your existing logic, adopt
it per the docs and best practices, and **register your business on Apple Business
Register** to learn about **Partner Data Services**. Related: **App Attest** (verifying
server requests come from legitimate instances of your app). Apple is actively
soliciting **Feedback Assistant** input on the framework, its capabilities, and
high-volume use cases.
