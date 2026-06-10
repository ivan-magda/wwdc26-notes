---
title: "Meet the Evaluations framework — Full Digest"
session: WWDC26 · 298
url: https://developer.apple.com/videos/play/wwdc2026/298/
duration: 26m
speakers: Rob Rhyne, Yada
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 298 — Meet the Evaluations framework

## TL;DR

The Evaluations framework is a new Swift framework for measuring the quality of
generative-AI features so you can ship them with confidence. The core argument:
language models break the "same input, same output" contract unit tests depend on, so
functional tests can't verify probabilistic behavior. You need a statistical form of
test. Three headline ideas:

1. **An `Evaluation` protocol that plugs into Swift Testing.** Five steps — subject,
   dataset, metrics/evaluators, aggregation, and a `@Test` with the `.evaluates` trait.
   You assert an **optimization target** (e.g. "correct tag count ≥ 80% of the time")
   with `#expect`, and a new Xcode **evaluation report** breaks down every sample.
2. **Quantitative metrics + hill-climbing.** Heuristics you can measure in code
   (`Metric` + `Evaluator`, pass/fail or scoring) feed a tight loop: failing target →
   read rationale → make one change → re-run. Centering development on that loop is
   what Apple calls **evaluation-driven development**.
3. **Model judges for qualitative metrics.** When "good" can only be described in words,
   a second, at-least-as-capable model (Private Cloud Compute) scores output the way a
   person would, consistently across the whole dataset. A `ModelJudgeEvaluator` is just
   another `Evaluator`, and you refine it with **score dimensions** and a
   **`ModelJudgePrompt`** when its judgement diverges from yours.

The running example is Book Tracker, a library app whose `BookTaggingService`
auto-tags books from freeform reader reviews.

---

## 1. Why unit tests aren't enough

Traditional software: a given input always produces a given output, so a unit test
verifies behavior and that guarantee holds on every device, including a customer's.
Generative AI breaks that — the same input can produce different outputs, so functional
consistency can't be used to verify behavior. The questions that matter become
statistical:

- How often does the feature produce unexpected results?
- How often does an agent take an unexpected path to an answer?
- Under what circumstances does it produce unsafe results?

The framework is a flexible system of types and protocols. The session focuses on
language-model features, but it's pitched at any stochastic system — classifiers,
linear regression, and so on.

## 2. The manual evaluation (Book Tracker)

Before writing any framework code, Rob runs `BookTaggingService` in a `#Playground`.
The "Pride & Prejudice" review returns **9 tags** (too many), includes the book's title
as a tag, and produces multi-word tags that would break the UI. "Dracula" returns 7
tags (in range) and identifies useful genres. That's a manual evaluation: a list of
human-authored expectations measured by human judgement — which doesn't scale.

Five expectations emerge: correct tag count, no title-as-tag, no multi-word tags,
genre identification, and browsing-useful categories.

## 3. Building your first evaluation

Five steps to the `Evaluation` protocol: define the **subject** (code under test), the
**dataset**, the **measurements** (and how), a **summary** of measurements, and a
**test** to run it.

```swift
// Evaluations
import Evaluations

struct BookTaggingEvaluation: Evaluation {

}
```

The subject is returned from a `subject(from:)` method (the generated tags). The dataset
wraps the same reviews as `ModelSample`s, each carrying an `expected` value — the ideal
tags:

```swift
var dataset = ArrayLoader(samples: [
    ModelSample(prompt: "okay I am OBSESSED and I need everyone to read this RIGHT NOW...",
                expected: BookTags(tags: ["classic", "romance", "wit", "regency"])),

    ModelSample(prompt: "Read this in one sitting between midnight and 4am and I cannot...",
                expected: BookTags(tags: ["classic", "gothic", "horror", "vampire", "suspense"])),
])
```

Measurements use `Metric` + `Evaluator`. An `Evaluator` is a closure that receives the
subject for one sample and returns a passing or failing `Metric`:

```swift
let tagCount = Metric("TagCount")

var evaluators: Evaluators {
    Evaluator { _, subject in
        let count = subject.value.tags.count
        if (count >= 3 && count <= 8) {
            return tagCount.passing(rationale: "\(count) tags")
        }
        return tagCount.failing(rationale: "Got \(count) tags, expected 3–8")
    }
}
```

Evaluators run one sample at a time. Trends across all samples are computed in
`aggregateMetrics(using:)`:

```swift
func aggregateMetrics(using aggregator: inout MetricsAggregator) {
    aggregator.computeMean(of: tagCount)
    aggregator.group("Distribution of Tag Totals") { aggregator in
        aggregator.computeStandardDeviation(of: tagTotal)
        aggregator.computeMean(of: tagTotal)
        aggregator.computeVariance(of: tagTotal)
    }
}
```

## 4. Running it and reading the report

Evaluations integrate with **Swift Testing**, so they live in your test target. A `@Test`
with the new `.evaluates` trait runs the evaluation; you read the results bundle and
assert an **optimization target** with `#expect`:

```swift
@Test("Book Tag Evaluations", .evaluates(evaluation, info: evaluationInfo))
func evaluateBookTagging() async throws {
    let result = EvaluationContext.current.result

    let rangeMetric = BookTagEvaluationTests.evaluation.tagCount
    #expect(result.aggregateValue(.mean(of: rangeMetric)) >= 0.8)
}
```

Why 80%? Below it, Rob wants a failing test as a signal. The new **evaluation test
report** in Xcode goes further than pass/fail: double-clicking a row shows TagCount
passed only 50% of the time (Pride & Prejudice failed, Dracula passed), and a detail
panel surfaces the prompt, each per-sample measurement, and the full model response.
Notes attached to the run let you compare across runs later.

## 5. Hill-climbing and evaluation-driven development

The failing target prompts analysis. Rob's hunch: the `@Generable` `BookTags` type has a
`@Guide` on `tags`, so add a `.count` range to instruct the model directly:

```swift
@Generable
struct BookTags: Codable {
    @Guide(description: "Descriptive tags capturing themes, genres, moods, and topics from the summary", .count(3...8))
    var tags: [String]
}
```

Re-running, TagCount now passes 100%. The loop — failing target → analyze → change →
re-run — is **hill-climbing**, and centering your whole development process on it is
**evaluation-driven development**. But Rob notices a new oddity: the service now always
generates exactly eight tags. Good evaluations surface exactly this kind of regression.

## 6. Building robust datasets

Two samples give only two measurements. Good datasets have **thousands**, with deliberate
variety so the feature is exercised many ways:

- Different genres
- Different review lengths (don't assume a verbose review)
- Fiction vs non-fiction, browsed via different categories
- Different forms: novels, short stories, essays
- Sprinkled-in personal opinions, to measure how well the service ignores them

To teach the feature your style, put more of your own voice into the `expected` values.
The talk shows hand-authored examples ("The Secret Garden" as an avid gardener, a
parent reading "Treasure Island", a multi-paragraph "Romance of the Three Kingdoms", a
one-sentence Watson description) — but hand-authoring doesn't scale either.

The framework's **`SampleGenerator`** synthesizes more samples from a seed set using a
model of your choice:

```swift
for try await sample in samples.makeSamples(
    """
    Generate diverse sentence completions about the listed topics:
      - The Solar System
      - World Capitals
    """,
    targetCount: 1000) {
        samples.append(sample)
}
```

Deeper coverage of synthesis and advanced `ModelSample` use is in **"Create robust
evaluations for agentic apps."**

## 7. Refining metrics and evaluators

With the dataset expanded to the whole library, TagCount still averages 100% but the
eight-tag behavior persists — so the metrics need refining. A scoring (not pass/fail)
`TagTotal` metric records the actual length, letting `TagCount` + `TagTotal` together
measure range compliance *and* distribution. Two more heuristics follow the same shape:

```swift
let wordCount = Metric("WordCount")
Evaluator { _, subject in
    for tag in subject.value.tags {
        if tag.contains(" ") {
            return wordCount.failing(rationale: "Tag \(tag) contains multiple words")
        }
    }
    return wordCount.passing()
}

let hasGenreTag = Metric("HasGenreTag")
Evaluator { _, subject in
    let tags = subject.value.tags.map { $0.lowercased() }
    let knownGenres = await BookTaggingService.knownGenres
    for tag in tags {
        if knownGenres.contains(tag) {
            return hasGenreTag.passing(rationale: "Matched \(tag)")
        }
    }
    return hasGenreTag.failing()
}
```

That covers three of the five original expectations, tracked by five aggregate metrics.
Each instruction change to the service is tied to the expectation (and metric) added to
verify it — a clean audit trail across hill-climbing runs.

## 8. Model judges: qualitative metrics

Quantitative metrics can all pass while the tags are still wrong. The "Alice in
Wonderland" sample returns six well-formed, genre-bearing tags — every heuristic passes
— yet 'overrated' and 'pretentious' describe the reader's feelings, not the book, and
'whodunit' is the wrong genre (picked up from "riddles he never answers"). The model
latched onto the review's language without understanding the book.

A **Model Judge** is a language model used to score your feature's output: a subjective
rating, the kind a person would make, applied consistently across the whole dataset.
The judge should be **at least as capable** as the model being evaluated — here the
on-device `BookTaggingService` is judged by a more capable **Private Cloud Compute**
model. Its components: an instruction, the feature input (the review), the feature
output (the tags), and a **scoring guide**. The framework handles most of it; you focus
on the scoring guide.

```swift
ModelJudgeEvaluator(
    "TagQuality",
    scale: .numeric([
        4: "Tags are relevant and helpful for browsing",
        3: "Mostly relevant, one tag too vague or generic",
        2: "Several tags are wrong or generic",
        1: "Unhelpful or irrelevant"
    ]),
    judge: PrivateCloudComputeLanguageModel()
)
```

Design notes: an **even** number of levels stops the judge defaulting to a neutral
middle; four levels give enough distinction without diluting each rating. A
`ModelJudgeEvaluator` conforms to the same protocol as quantitative evaluators and
produces the same `Metric` type, so you can mix them freely in one evaluation.

## 9. Score dimensions and the ModelJudgePrompt

Running TagQuality, every sample scores 3 or 4. "Alice in Wonderland" gets a 3, and the
rationale flags 'whodunit' and 'detective-fiction' — but misses the opinion tags the
team expected it to catch. The lesson: **by the scale they wrote, the judge is right.**
Every tag does connect to something the user wrote; the judge faithfully followed the
guide. The team meant something more specific by "relevant" and "useful for browsing."

Rationales are essential — they're the window into why a score happened. When you
disagree with a score, the question is usually **too broad**, asking two things at once.
Split it into independent **`ScoreDimension`s**:

```swift
ScoreDimension(
    "Relevance",
    description: """
        Whether each tag describes a quality, theme, or tone
        of the book itself rather than incidental details or
        the reader's personal reactions.
        """,
    scale: .numeric([
        4: "Every tag describes the book itself",
        3: "Most tags describe the book",
        2: "Some tags describe personal reactions",
        1: "Tags don't meaningfully describe the book"
    ])
)
```

"Usefulness" is defined the same way, and both are added to the `ModelJudgeEvaluator`.
But dimensions tell the judge *what* to measure, not how to think about *your app* — so a
judge might treat a reader's criticism as a valid descriptor, not knowing Book Tracker
is a personal library rather than a review platform. The **`ModelJudgePrompt`** supplies
that context:

```swift
ModelJudgeEvaluator(
    judge: PrivateCloudComputeLanguageModel(),
    dimensions: [relevance, usefulness],
    prompt: ModelJudgePrompt(
        instructions: """
            You are evaluating tags generated for a personal book-tracking app where users
            organize their library by browsing and filtering tags.
            """,
        evaluationTarget: { value in
            "\(value.tags.count) Generated tags: " + value.tags.joined(separator: ", ")
        },
        reference: { input, _ in
            let expectedTags = input.expected?.tags.joined(separator: ", ")
            return ["Expected Tags": expectedTags ?? "No expected tags defined"]
        }
    )
)
```

Re-running replaces the single Quality score with separate Relevance and Usefulness
scores whose rationales split the diagnosis: Relevance says *what kind* of tag is wrong,
Usefulness says *how* the wrong tags fail at browsing — a clear path back into the
hill-climbing loop, now powered by qualitative metrics.

## 10. Best practices

- **Start small** — 20 to 30 focused samples is a great starting point.
- **Spec out your app** by thinking about how you want the model to behave.
- **Use heuristics for quantitative traits.** Rule of thumb: if you can measure it in
  code, it's quantitative; if you can only describe it in words, it's qualitative and
  needs a `ModelJudgeEvaluator`.
- **Start simple with the judge** — define a dimension, run it, read the rationales.
  You learn more from one run than from hours of planning.
- **Let rationales drive the next change.** If scores are all the same, the question is
  too broad. If you can't isolate the problem, split the dimensions. If the judge
  doesn't understand your app, add context.
