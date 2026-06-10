# Code samples — Session 379

Extracted from the Code tab. The Code tab contained three blocks; the first and
third are identical, so there are two unique snippets below.

## Full integration flow — request, authorize, evaluate, report

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

## Handling the outcome — switch over the insight result

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

---

## Useful API facts surfaced by the code + transcript

- Module: `import TrustInsights`. Requires a Trust Insights **entitlement** declared as an Xcode capability on the app target.
- Insight request: `IsLikelyBeingCoachedInsight.request(schema:modelVersion:)` — `schema` required (e.g. `.version1`), `modelVersion` optional (e.g. `.current`; pairing current + prior supports model governance/validation).
- `InsightEvaluator.InsightContext(operationCategory:requestedEvaluations:)`. `operationCategory` is one of: `.payment`, `.account`, `.resourceUse`, `.communication`, `.other` — it selects which model logic is applied.
- `InsightEvaluator()` → `requestAuthorization(for:)` returns an authorization status (`.authorized`); `requestEvaluation(context:)` is async, returns an `InsightEvaluation<…>`.
- Result access: `assessment.insight.outcome.get()` is throwing and yields `.unknown` / `.medium` / `.high` (plus `@unknown default`). Handle evaluation-level and insight-level errors independently.
- Feedback: `assessment.reportConsumption(_:)` is **mandatory per evaluation** (omit → rate-limiting). Six values named in the talk: `.usedReducedFriction`, `.usedUnchangedFriction`, `.usedIncreasedFriction`, `.notUsedNotNeeded`, `.notUsedError`, `.usedEvaluationOnly`.
- The generic result type shown is `InsightEvaluation<IsLikelyBeingCoachedInsight>`.
