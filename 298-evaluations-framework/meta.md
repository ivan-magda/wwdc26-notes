# Session 298 — Meet the Evaluations framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/298/
- **Duration:** 26m
- **Speakers:** Rob Rhyne, Yada
- **Audio:** English.

## Description

A new Swift framework that measures the quality of your intelligent features so you
can ship with confidence. Generative-AI features break the "same input, same output"
contract that unit tests rely on, so functional tests can't verify probabilistic
behavior. The Evaluations framework gives you a flexible system of types and protocols
to quantify how often a feature produces unexpected or unsafe results — with
quantitative metrics (heuristics you can measure in code) and qualitative metrics
(model judges that score output the way a person would, applied consistently across a
dataset). The session builds an evaluation for a Book Tracker demo app whose
BookTaggingService auto-tags books from reader reviews.

## Key topics

- Why unit tests are insufficient for generative AI — same input can produce different outputs; need a more robust, statistical form of test
- The `Evaluation` protocol — five steps: subject (code under test), dataset of `ModelSample` inputs with expected values, `Metric` + `Evaluator` measurements, `aggregateMetrics`, and a Swift Testing `@Test`
- Swift Testing integration via the `.evaluates` trait; an **optimization target** asserted with `#expect` (e.g. mean ≥ 80%)
- The new **evaluation test report** in Xcode — per-sample results, prompts, measurements, full model response, compare across runs
- Building **robust datasets** — variety (genres, review lengths, fiction/non-fiction, forms, personal opinions); `SampleGenerator` / `makeSamples` to synthesize thousands from a seed set
- Quantitative metrics — pass/fail vs scoring evaluators; range compliance, distribution (mean/variance/standard deviation), word-count, known-genre checks
- **Hill-climbing** and **evaluation-driven development** — failing target → analyze rationale → change (e.g. add `.count(3...8)` to a `@Guide`) → re-run
- **Model judges** — a second, at-least-as-capable model (Private Cloud Compute) scores output; `ModelJudgeEvaluator` is just another `Evaluator` producing the same `Metric` type
- Scoring-guide design — even number of levels (1–4) avoids a neutral default; rationales are essential
- **Score dimensions** — split a too-broad question into `ScoreDimension`s (Relevance vs Usefulness); `ModelJudgePrompt` gives the judge context about your app
- Best practices — start small (20–30 samples), heuristics for quantitative traits, `ModelJudgeEvaluator` for qualitative, let rationales drive the next change

## Related sessions to fetch (referenced in this talk)

- [ ] Improve your prompts by hill climbing with Evaluations
- [ ] Create robust evaluations for agentic apps
- [ ] What's new in the Foundation Models framework (session 241 — parent talk, already processed)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Rob Rhyne and Yada introduce the Evaluations framework. Generative-AI features break the "same input, same output" contract unit tests rely on, so a more robust form of testing is needed to measure how often features produce unexpected or unsafe results.
- **3:10 Demo app Book Tracker: a manual evaluation** — Introduces Book Tracker and its BookTaggingService, which auto-tags books from reviews. Trying it in a `#Playground` surfaces issues (too many tags, book title as a tag, multi-word tags) and produces a first human-judged list of expectations.
- **4:31 Building your first evaluation** — Implement the `Evaluation` protocol in five steps: subject (code under test), dataset of `ModelSample` inputs with expected values, a `Metric` and `Evaluator` (pass/fail on tag count), and an `aggregateMetrics` summary.
- **8:06 Running the evaluation and reading the report** — Run through Swift Testing with the `.evaluates` trait and an optimization target (`#expect` average ≥ 80%). The new evaluation test report breaks down per-sample results, prompts, measurements, and the full model response.
- **10:57 Building robust datasets** — Two samples aren't enough; good datasets have thousands with variety. Hand-authoring doesn't scale, so the framework's `SampleGenerator` synthesizes more samples from a seed set.
- **14:20 Refining metrics and evaluators** — Add metrics for deeper insight: `TagTotal` with a scoring evaluator, range-compliance and distribution, word-count, and genre checks against `knownGenres`, covering three of the five original expectations.
- **15:41 Evaluation-driven development and hill-climbing** — Recap the loop: a failing target prompts analysis and a change (adding a count range to the `@Guide` on the `BookTags` `@Generable`). Re-running to check is hill-climbing; centering development on it is evaluation-driven development.
- **16:12 Model judges: qualitative metrics** — Quantitative metrics can pass while tags are still wrong (reader opinions, mis-inferred genres). A model judge uses a second, at-least-as-capable model (Private Cloud Compute) to score output the way a person would.
- **18:42 Building a model judge** — A `ModelJudgeEvaluator` is just another `Evaluator` producing the same `Metric` type. Define a `TagQuality` metric on a 1-to-4 scale, specify the judge model, run it, and read the rationales.
- **21:19 Refining with score dimensions** — When you disagree with a score, the question is often too broad. Split it into `ScoreDimension`s (Relevance vs Usefulness), each with its own description and scale, and add a `ModelJudgePrompt` to give the judge app context.
- **23:45 Reviewing dimension results** — Re-running yields separate relevance and usefulness scores whose rationales split the diagnosis, giving a clear path back into the hill-climbing loop.
- **24:20 Best practices** — Start small (20–30 focused samples), use heuristics for quantitative traits, use `ModelJudgeEvaluator` for qualitative ones, start simple with the judge, and let rationales drive the next change.
- **25:38 Next steps** — Pointers to the Evaluations docs, the Book Tracker sample code, and the companion sessions on hill-climbing and robust agentic evaluations.

## Code

See `code.md` — 13 snippets extracted from the Code tab.
