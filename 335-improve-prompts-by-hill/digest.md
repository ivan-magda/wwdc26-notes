---
title: "Improve your prompts by hill-climbing with Evaluations — Full Digest"
session: WWDC26 · 335
url: https://developer.apple.com/videos/play/wwdc2026/335/
duration: 27m
speakers: Marcus (Evaluations framework team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 335 — Improve your prompts by hill-climbing with Evaluations

## TL;DR

This is the "now improve it" sequel to *Meet the Evaluations framework*. You already
have an evaluation pipeline; this session is about **hill-climbing** — iterating on a
feature using evaluation scores as your compass. Four headline ideas:

1. **The loop is develop → run → analyze, run like a science experiment.** Change one
   variable at a time so you can attribute each score change to a cause.
2. **Your model judge drifts.** A `ModelJudgeEvaluator` rates differently from a human
   expert, and that gap (**drift**) widens as the dataset grows until you can't trust
   the evaluation. The fix is to *evaluate your evaluator* and align it.
3. **Measure alignment with Cohen's kappa, not raw accuracy.** Accuracy is fooled by
   skewed score distributions (a judge that always rates high looks aligned by luck).
   Kappa subtracts random agreement and normalizes; **0.6** is the "meaningful
   agreement" target.
4. **Hill-climbing isn't only prompts.** Once the judge is trusted, the same loop
   improves the *feature* — here, adding a `BookLookupTool` so the on-device tag model
   knows the book's title and author. A with-tool vs. without-tool evaluation proves
   the change.

The running example is **Book Tracker**, whose tag generator emits tags that miss
themes ("Treasure Island" lacking *tense* / *morally grey*) or capture the reader's
feelings instead of the book ("Little Women" tagged *poignant*, *quiet-steadiness*).

---

## 1. The hill-climbing loop

Hill-climbing is a three-phase cycle:

- **Develop** — make a change you want to measure against the existing feature (to the
  feature itself *or* to the evaluation).
- **Run** — run the evaluation and check it against your expectations, expressed with
  Swift Testing's `#expect`. Tests pass = expectations met.
- **Analyze** — use Xcode's evaluation report to understand where to improve next.

The framing throughout: **bring scientific thinking** to the loop. Isolate variables,
keep a control, and accept that failed experiments are still informative.

## 2. The problem: a judge that disagrees with you

Book Tracker's evaluation already exists. Its quality bar lives in two
`ScoreDimension`s fed to a `ModelJudgeEvaluator`:

- **Relevance** — does each tag describe the book (plot, theme, tone) rather than the
  reader's reaction or incidental detail?
- **Usefulness** — are tags at the right granularity to be useful search terms?

The presenter adds his "Treasure Island" and "Little Women" reviews to the dataset,
runs the evaluation (all `#expect`s pass), then opens the **assistant editor** to
compare generated tags against his expected tags. He'd score both reviews
**Relevance 4 / Usefulness 2**; the judge says **4 / 4**. The judge and the human
disagree on usefulness.

```swift
// the evaluation's quality bar (excerpt — full struct in code.md)
let relevance = ScoreDimension("Relevance", description: """
    Whether each tag describes a quality, theme, or tone of the book itself
    rather than incidental details or the reader's personal reactions.
    """, scale: .numeric([ 4: "...", 3: "...", 2: "...", 1: "..." ]))

ModelJudgeEvaluator(
    judge: .default,
    dimensions: [relevance, usefulness],
    prompt: ModelJudgePrompt(
        instructions: "You are evaluating automatically generated tags for ...",
        evaluationTarget: { output in output.tags.joined(separator: ", ") },
        reference: { input, _ in ["Expected Tags": input.expected?.tags.joined(separator: ", ") ?? ""] }
    )
)
```

## 3. Drift, and why accuracy isn't enough

**Drift** is the divergence between a model judge's ratings and an expert's. Average
both sets of scores and, if they tend to disagree, the aggregates pull apart — and the
gap **widens as the dataset grows**, eroding trust in the whole evaluation.

How to quantify it? The naive metric is **accuracy**: line up the two raters and count
exact matches as a percentage. That works *only if every score on the scale is equally
likely*. In practice datasets skew toward high-quality examples, so a human rates high
— and a judge that also happens to rate high looks aligned **by luck**. Unleashed on a
larger, more varied dataset, that judge still drifts.

## 4. Cohen's kappa as the alignment metric

**Cohen's kappa** (Jacob Cohen, 1960) corrects accuracy for chance agreement:

```
alignment = (accuracy − chance_agreement) / (1 − chance_agreement)
```

- start from **accuracy** (how often the raters agreed),
- subtract **coincidence / chance agreement** (the odds the raters agreed at random,
  weighted by how likely each score is),
- divide by the inverse of random agreement (the chance they agreed *intentionally*).

The result is a robust alignment score. Statisticians treat **0.6** as meaningful
agreement, which becomes the target threshold.

## 5. Building a judge-alignment evaluation

Now hill-climb the *judge* itself. A judge-alignment evaluation has the usual four
parts — dataset, subject, evaluators, aggregation:

- **Dataset** — the judge and the human must rate the *exact same* tags. The previous
  run wrote an **Xcode attachment** with all evaluation data; extract the summary/tag
  pairs from it and add expert ratings.
- **Subject** — normally `subject(from:)` calls your feature's API, but here the tags
  are already generated, so it just returns them.
- **Evaluators** — the *same* `ModelJudgeEvaluator` as the book-tags evaluation; this
  is where the judge produces its rating to compare against the human's.
- **Aggregation** — a custom aggregation computes Cohen's kappa per dimension, plus
  mean and standard deviation (to see whether the judge's scores trend up or down).

```swift
func aggregateMetrics(using aggregator: inout MetricsAggregator) {
    let expertRelevance = Self.samples.map { Double($0.expected?.expertRelevanceScore ?? 0) }
    // ...
    aggregator.group("Relevance") { group in
        group.computeMean(of: relevance.metric)
        group.computeStandardDeviation(of: relevance.metric)
        group.custom(of: relevance.metric, label: "Relevance Alignment Score") { judge in
            cohensKappa(ratings1: expertRelevance, ratings2: judge) ?? 0
        }
    }
    // ... same for Usefulness
}
```

```swift
@Test("Judge Calibration", .evaluates(evaluation))
func evaluateJudgeCalibration() async throws {
    let result = EvaluationContext.current.result
    #expect(result.aggregateValue(.custom(label: "Relevance: Judge vs Expert")) > 0.6)
    #expect(result.aggregateValue(.custom(label: "Usefulness: Judge vs Expert")) > 0.6)
}
```

This baseline run **fails** — both alignment scores are low. Drilling into the report:
the judge rates *Frankenstein*'s off-theme tags (*self-help*, *self-improvement*) too
highly on relevance, and rates *The Ramakien*'s overly-specific tags
(*visual-dimension*, *quaint-dignity*) too highly on usefulness. The diagnosis: the
judge's prompt **lacks the context** to tell a good tag from a bad one.

## 6. Comparative evaluations (control vs. experimental)

**Xcode 27** can run and compare two evaluations like a controlled experiment:

- **Control** = the baseline judge prompt.
- **Experimental** = a richer prompt that gives the judge app context plus examples of
  good and bad tags.

```swift
struct BookTagJudgmentCalibrationExperimental: Evaluation {
    var evaluators: Evaluators {
        ModelJudgeEvaluator(
            judge: .default,
            dimensions: [relevance, usefulness],
            prompt: ModelJudgePrompt(
                instructions: """
                    You are an experienced reader and librarian ...
                    ## What a good tag looks like  — genre/form, theme, tone, setting/era
                    ## Common failure modes — reader reactions, meta-commentary, author facts, genre contradictions
                    """,   // full prompt ~40 lines
                evaluationTarget: { output in output.tags.joined(separator: ", ") },
                reference: { input, _ in
                    ["Book Review": input.promptDescription,
                     "Tags Generated for the Review": input.expected?.tags.joined(separator: ", ") ?? ""]
                }
            )
        )
    }
}
```

Both evaluations go in one **test suite**. Result: **relevance improved, usefulness
dropped** — a tradeoff to weigh, not a clean win. The presenter keeps the prompt change
and targets usefulness next.

## 7. Refining the scoring dimensions

The new **comparison view** in the evaluation report shows the two prompts side by side.
On the *Picture of Dorian Gray* review the experimental judge scores usefulness only
2s and 3s — **too harsh**. Time to sharpen the `ScoreDimension` descriptions.

Crucial scientific move: **before** changing the scoring dimensions, the presenter
**applies the experimental prompt back into the baseline** so the *only* differing
variable is the dimension wording. The sharpened descriptions emphasize the need for a
**genre tag** (relevance) and being **more critical of overly-specific tags**
(usefulness). Both alignment scores **improve greatly** — but still fall short of 0.6.

## 8. Adding few-shot examples to the judge

The last lever: ground the judge prompt in the feature's purpose, then add a few
**worked examples** showing exactly how the presenter rates, giving the model a pattern
to follow.

```swift
instructions: """
    You are calibrating with an expert librarian who scores ... tags for Book Tracker.
    Your goal is to match how the librarian scores. Use the worked examples to calibrate.

    ## Worked examples
    ### Example A — clean fit (Pride and Prejudice)
    Tags: romance, historical-fiction, love, redemption, passion
    Librarian: Relevance 4, Usefulness 4

    ### Example E — flat genre contradiction (Frankenstein)
    Tags: horror, science-fiction, ... self-help, self-improvement
    Librarian: Relevance 2, Usefulness 3
    ... (6 examples A–F; keep the set small to avoid overfitting)
    """   // full prompt ~60 lines
```

The examples are kept **deliberately few** — a longer list would **overfit the
alignment score**, making it impossible to tell whether the judge is genuinely aligned.
This run finally **exceeds expectations**: the judge is now trusted, and the
hill-climbing loop on the judge **exits**. The aligned judge can now be put to work
evaluating the actual Book Tagging Service.

## 9. Going beyond prompts: adding a tool

With a trusted judge, hill-climb the *feature*. Book Tracker generates tags with the
**on-device model** (so readers can tag books anywhere). To give it more context, add a
`BookLookupTool` that returns the book's title and author from the stored review data.

```swift
struct BookLookupTool: Tool {
    let name = "lookupBook"
    let description = "Looks up the title and author of a book given distinguishing details ..."
    @Generable struct Arguments { @Guide(description: "...") var details: String }
    @Generable struct Output {
        @Guide(description: "...") var title: String
        @Guide(description: "...") var author: String
    }
    func call(arguments: Arguments) async throws -> Output { /* keyword-overlap match over sampleBooks */ }
}
```

`BookTaggingService` gains a `tools` parameter that **defaults to `[]`**, so the
existing evaluation needs no changes:

```swift
static func generateTags(for review: String, tools: [any Tool] = []) async throws -> BookTags {
    let session = LanguageModelSession(
        model: SystemLanguageModel(guardrails: .permissiveContentTransformations),
        tools: tools,
        instructions: instructions
    )
    return try await session.respond(to: tagsPrompt(review: review), generating: BookTags.self).content
}
```

A second evaluation passes `[BookLookupTool()]`; both run in one suite as
**with-tool vs. without-tool**. The tool version scores better and passes its
expectations. Two caveats the presenter flags:

- the dataset is only **13 book/review pairs** — too small to cover real-world variety;
- the tool **isn't always called** where it should be, and there's no visibility into
  tool calls here.

Both point to **"Create robust evaluations for agentic apps"** (tool-call evaluators +
the Sample Generator API).

## 10. Recap / next steps

- **One change at a time** — treat each iteration as a science experiment so you can
  attribute score changes to causes.
- **Invest the time** — failed experiments inform you as much as successful ones.
- **Be creative** — instructions, tools, models, datasets, aggregations, *and*
  evaluators are all fair game.
- **Watch for drift** — it feels meta to evaluate your evaluators, but an aligned judge
  rates far faster than a human and keeps giving useful signal as the dataset grows.

Download the Book Tracker sample (and the judge-alignment evaluations) and review the
developer documentation.
