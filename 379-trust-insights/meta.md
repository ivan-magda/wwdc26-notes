# Session 379 — Meet Trust Insights

- **URL:** https://developer.apple.com/videos/play/wwdc2026/379/
- **Duration:** 14m
- **Speakers:** Mike Armstrong (Engineering Manager, Apple)

## Description

Meet Trust Insights, a new framework introduced in iOS 27 that helps your app
detect and respond to social engineering threats — coercion, scams, and real-time
coaching attacks where a legitimately authenticated user is being manipulated.
Authentication confirms *who* is acting, but not whether they are acting *freely*;
Trust Insights adds privacy-preserving behavioral context so your app can
distinguish genuine intent from a coerced action and adjust its flow accordingly.

## Key topics

- The coercion threat model: tech-support scams, authority impersonation, family-emergency fraud, AI deepfakes, and real-time coaching that defeats MFA/biometrics because the victim acts themselves
- Client-side Swift API over a combined on-device + cloud architecture; requires a Trust Insights **entitlement** (Xcode capability)
- `IsLikelyBeingCoachedInsight` request built with a `schema` (required) and optional `modelVersion`, assembled into a parameter pack of requested evaluations
- `InsightEvaluator` + `InsightEvaluator.InsightContext` with an **operationCategory** (`payment`, `account`, `resourceUse`, `communication`, `other`) selecting which model logic applies
- Authorization check (`requestAuthorization`) — users can disable Trust Insights per app in Settings
- `requestEvaluation` is async, takes a couple seconds, needs Internet reachability; sandbox in development vs. production models on the App Store; launch-arg overrides via Xcode schemes
- Three-valued outcome: `unknown` / `medium` / `high` — never treat `unknown` or missing as low risk
- Two mandatory/recommended feedback loops: real-time **`reportConsumption`** (six values; mandatory or risk rate-limiting) and **offline fraud labels** via Apple Business Register server-to-server API
- Privacy architecture: data minimization, inputs discarded immediately, only a single output value leaves the device; analyzes interaction/timing/context/basic sensor data, never Photos/Messages/Mail content; cooldown after disabling
- Best practices: combine with existing risk logic (never sole determinant), sample model versions over time, handle evaluation- vs insight-level errors separately

## Related sessions to fetch (referenced in this talk)

- [ ] App Attest (verifying server requests come from legitimate app instances) — framework named, not a specific session ID
- (No other WWDC sessions are explicitly named; the talk points to the Trust Insights developer documentation and Apple Business Register / Partner Data Services.)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Mike Armstrong; the social-engineering / coercion threat model. Auth confirms *who*, not whether they act freely. Trust Insights (iOS 27) adds privacy-preserving behavioral context.
- **2:35 Generating insights** — entitlement + `import TrustInsights`; build a parameter pack of requested insights (`schema` required, `modelVersion` optional); `InsightEvaluator.InsightContext` with an `operationCategory` (five categories) that determines which model logic applies; `requestAuthorization`; async `requestEvaluation` (seconds, needs Internet); sandbox vs production; scheme override for testing; three-valued `IsLikelyBeingCoachedInsight` outcome; on-device processing with a single output value leaving the device.
- **6:50 Feedback requirements** — mandatory real-time `reportConsumption` (six values; omit → rate-limited) reporting how the app responded; offline fraud feedback via Apple Business Register server-to-server API, submitted days/weeks/months later, PII-free and fingerprint-hardened.
- **9:25 Privacy** — data minimization, inputs discarded immediately, device-sourced signals stay on device; analyzes interaction patterns/timing/context/basic sensor data, never Photos/Messages/Mail; user can disable in Settings with a cooldown.
- **10:34 Best practices** — example money-transfer flow reacting to `.medium`; where it adds most value (high-value/irreversible/permission/sensitive-sharing moments); combine with existing risk logic, sample model versions, handle errors per level, never treat unknown as low risk.
- **12:48 Next steps** — identify moments, adopt per docs/best-practices, register on Apple Business Register for Partner Data Services; see also App Attest; file Feedback Assistant reports.

## Code

See `code.md` — snippets extracted from the Code tab (the two unique snippets are the full integration flow and the outcome-handling switch).
