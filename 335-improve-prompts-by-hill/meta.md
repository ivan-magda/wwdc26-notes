# Session 335 — Improve your prompts by hill-climbing with Evaluations

- **URL:** https://developer.apple.com/videos/play/wwdc2026/335/
- **Duration:** 27m
- **Speakers:** Marcus (manager, Evaluations framework team)

## Description

How to use the Evaluations framework to iteratively improve an intelligence-powered
feature by hill-climbing — repeatedly running an evaluation, analyzing the scores, and
making one change at a time, while applying scientific thinking (control vs.
experimental groups, isolating variables). The session reworks Book Tracker's tag
generator: it diagnoses *drift* between a model judge and the developer's expert
ratings, measures alignment with Cohen's kappa, aligns the judge via prompt and
scoring-dimension changes, then goes beyond prompts by adding a tool to the on-device
tagging model. Assumes you already have an evaluation pipeline (see "Meet the
Evaluations framework").

## Key topics

- The hill-climbing loop: **develop → run → analyze**, treating each iteration as a science experiment (change one variable at a time).
- **Drift** — divergence between a model judge's ratings and an expert's; widens as the dataset grows, eroding trust in the evaluation.
- Why **accuracy** alone misleads on unevenly-distributed scores; **Cohen's kappa** subtracts the chance of random agreement and normalizes for a robust alignment metric (target 0.6 = meaningful agreement).
- Building a **judge-alignment evaluation**: extract summary/tag pairs from a prior run's attachment, add human ratings, reuse the same `ModelJudgeEvaluator` as the subject, aggregate Cohen's kappa + mean + standard deviation.
- **Comparative evaluations** in Xcode 27 — run a control (baseline) and experimental evaluation in one suite and compare results side by side; the new comparison view in the evaluation report.
- Aligning the judge through three levers: richer **judge prompt** (app context, good/bad tag examples), sharpened **`ScoreDimension`** descriptions, and a few **worked examples** (kept small to avoid overfitting the alignment score).
- **Going beyond prompts**: a `BookLookupTool` gives the on-device model book title/author; `BookTaggingService` gains a `tools` parameter (default `[]`); a with-tool vs. without-tool evaluation shows the tool wins.
- Caveats: a tiny **13-sample** dataset and unobserved tool calls point to "Create robust evaluations for agentic apps" (tool-call evaluators + Sample Generator API).

## Related sessions to fetch (referenced in this talk)

- [ ] Meet the Evaluations framework
- [ ] Create robust evaluations for agentic apps

## Chapter summary (Summary tab)

- **0:00 Introduction** — Hill-climbing: iteratively improving a feature using evaluation scores as a guide (develop, run, analyze), framed around bringing scientific thinking to the loop. Assumes an existing evaluation pipeline.
- **2:42 BookTracker's tagging problem** — The tag generator produces tags that miss key themes or reflect the reader's feelings rather than the book. The existing evaluation judges tag quality via score dimensions (Relevance, Usefulness) and a `ModelJudgeEvaluator`.
- **5:27 Analyzing the evaluation results** — Adds two reviews to the dataset, runs the evaluation (Swift Testing `#expect`), and uses the Xcode evaluation report + assistant editor to compare generated tags against expected ones; the human and model judge disagree on usefulness.
- **8:26 Drift between judge and human** — That disagreement is *drift*: the divergence between a model judge's ratings and an expert's. As the dataset grows, drift widens, so the judge must be aligned to expert opinion.
- **9:37 Measuring drift with Cohen's kappa** — Accuracy alone misleads on unevenly-distributed scores (a high-scoring judge looks aligned by luck). Cohen's kappa subtracts the chance of random agreement from accuracy and normalizes — a robust drift metric.
- **12:26 Building a judge alignment evaluation** — Compares the presenter's ratings to the judge's over a shared dataset: extract summary/tag pairs from the prior run's attachment, add human ratings, reuse the same `ModelJudgeEvaluator` as subject, aggregate Cohen's kappa (+ mean, std. dev), targeting alignment 0.6.
- **15:16 Analyzing alignment failures** — The alignment test fails. Drilling into the report (Frankenstein, The Ramakien) shows the judge rating overly-specific or off-theme tags too highly; the judge's prompt lacks context.
- **17:16 Comparative evaluation: control vs. experimental** — Xcode 27 compares two evaluations like a controlled experiment: baseline (control) prompt vs. an experimental prompt adding app context plus good/bad tag examples. Relevance improves while usefulness drops — a tradeoff.
- **19:12 Refining the scoring dimensions** — The side-by-side view reveals the judge grading usefulness too harshly. Applying the new prompt to the baseline to isolate one variable, the `ScoreDimension` descriptions are sharpened (emphasize genre tags; be critical of overly-specific ones), improving both scores.
- **21:23 Adding few-shot examples to the judge** — Still short of the goal, the judge prompt is grounded with the feature's purpose and a few worked examples of how the presenter rates (deliberately few to avoid overfitting). Scores finally exceed expectations, so the judge is trusted and the loop exits.
- **23:38 Going beyond prompts: adding a tool** — Hill-climbing isn't only prompts: a `BookLookupTool` supplies title and author to the on-device tag model. `BookTaggingService` gains a `tools` parameter (default empty), and a second evaluation compares the feature with vs. without the tool — the tool version scores better, though the 13-sample dataset and unobserved tool calls point to "Create robust evaluations for agentic apps."
- **27:17 Next steps** — Think like a scientist (one change at a time), invest the time (failed experiments still inform), be creative (instructions, tools, models, datasets, aggregations, and evaluators are all fair game), and watch for drift. Download the Book Tracker sample and review the documentation.

## Code

See `code.md` — 13 snippets extracted from the Code tab.
