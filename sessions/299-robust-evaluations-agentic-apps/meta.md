# Session 299 — Create robust evaluations for agentic apps

- **URL:** https://developer.apple.com/videos/play/wwdc2026/299/
- **Duration:** 22m
- **Speakers:** Ada Wong, Kyle Murray (Evaluations team)
- **Platforms:** Evaluations framework new in Xcode 27; supports macOS, iOS, watchOS, visionOS.

## Description

Advanced features of the Evaluations framework (new in Xcode 27) for assessing
intelligence-powered Swift app features. The session focuses on the Develop-and-Evaluate
step of the hill-climbing process and covers two problems: growing a thin evaluation
dataset with **synthetic data**, and building **tool (trajectory) evaluations** for
agentic, tool-calling workflows so you can verify *how* a model reaches its answer, not
just *what* it produces. The running example is BookTracker, a personal library app that
auto-tags books from written reviews and answers library-assistant queries via custom tools.

## Key topics

- **Synthetic data generation** — `makeSamples(prompt:targetCount:)` on a dataset returns an async stream of new `ModelSample`s; `targetCount` is the full resulting size including your seeds.
- **Coverage over quantity** — ask "have I covered the meaningful variety of ways this feature is used?" not "how many samples do I need?"; iterative loop of generate → validate → analyze.
- **`SampleGenerator`** for full control: `sessionProvider` closure picks the model (e.g. `PrivateCloudComputeLanguageModel` for larger context) and instructions; `samplingStrategy` (`random` default vs `slidingWindow`); `validator` closure; `.run()` stream; `samples` / `invalidSamples` properties updated in real time.
- **Self-contained sessionProvider** — generator reuses one session across batches but re-calls the provider with a fresh session if the context window is exhausted mid-run; instructions must not assume single invocation.
- **Validation** — per-sample, in isolation (no cross-sample context); systematic checks only (length ≥ 100 chars, 3–8 tags, lowercase tags). Diversity/length-variation across samples need judgement, not a simple closure.
- **Comparing runs** — Xcode 27 Evaluations Report + Compare button; 13 → 100 samples made quality scores drop, exposing that the feature only looked good on the tiny dataset. A drop can signal prompt, feature, evaluation, or dataset problems.
- **Tool evaluations** — verify the *how*: correct tools, correct arguments, correct order, no surprise/disallowed calls. Challenges: instruction following, tool complexity, edge cases.
- **`TrajectoryExpectation`** — checks kind + order of tool calls in a session transcript; `unordered:` / `ordered:` arrays of `ToolExpectation`; `disallowed:` set for tools that must not appear.
- **Argument matchers** — `.exact`, `.naturalLanguage`, `.keyOnly`, `.contains`, `.oneOf`, `.pattern`, `.range`, and more.
- **`ToolCallEvaluator`** — combines a `LanguageModelSession` + tools, gets a response, captures the structured transcript, scores against expectations; results appear in the Xcode assistant alongside output evaluations.
- **Synthetic data for tool evals** — `ModelSample` and `TrajectoryExpectation` are `Generable`, so the same generator can synthesize tool-eval samples; describe available tools, order requirements, and matchers in the instructions since the model doesn't know your tools.
- **End-to-end** — run output evaluations (what) and tool evaluations (how) in one suite.

## Related sessions to fetch (referenced in this talk)

- [ ] Meet the Evaluations framework
- [ ] Improve your prompts by hill climbing with Evaluations
- [ ] Build agentic app experiences with Foundation Models

## Chapter summary (Summary tab)

- **0:00 Introduction** — Ada Wong & Kyle Murray introduce advanced Evaluations features (Xcode 27). Agenda: grow your dataset with synthetic data, then build robust evaluations for agentic tool-calling workflows; focus on the Develop-and-Evaluate step of hill climbing.
- **2:21 The dataset problem in BookTracker** — BookTracker auto-tags books from reviews, but its 13 hand-written `sampleBooks` give only a narrow view. Real reviews span countless books, genres, lengths, styles — too much to capture by hand.
- **3:46 Generating synthetic data with makeSamples** — `makeSamples` takes a prompt, a dataset (`ModelSample` review→tags), and a target count (full resulting size, including seeds). Returns an async stream; coverage of real usage matters more than raw quantity.
- **6:27 Customizing generation with SampleGenerator** — `SampleGenerator` exposes a `sessionProvider` closure to pick the model (e.g. Private Cloud Compute) and instructions. The session is reused across batches but can exhaust its context window mid-run; make instructions self-contained since the provider may be called again.
- **8:38 Sampling strategies** — `samplingStrategy` controls which seed samples are shown as in-context examples: `random` (varied subset, default) or `slidingWindow` (sequential, for ordered datasets).
- **10:11 Validating synthetic samples** — a `validator` closure accepts/rejects each sample in isolation against systematic rules: review ≥ 100 chars, 3–8 tags, lowercase. Valid samples collect in `samples`, rejects in `invalidSamples`, both updated in real time.
- **13:04 Comparing evaluation results** — Xcode 27 Evaluations Report compares the 13-sample run vs the 100-sample run. Scores drop; the feature only looked good on the small dataset, and a drop can signal prompt, feature, evaluation, or dataset issues.
- **15:09 Tool calling and tool evaluations** — features take multiple behind-the-scenes tool calls, and a plausible answer can come from the wrong path. Tool evaluations verify the *how*: correct tools, arguments, order, no surprises — illustrated with `searchBooks`, `getBookDetails`, `findSimilarBooks`.
- **18:54 Trajectory expectations** — a `TrajectoryExpectation` checks the kind and order of tool calls in a session transcript. Refine with argument matchers (`exact`, `naturalLanguage`, `contains`, `oneOf`, `pattern`, `range`), plus ordered expectations and a `disallowed` set.
- **21:26 Building a tool call evaluation** — bring the expectations together: a dataset of samples (each a prompt plus expectation) scored by `ToolCallEvaluator`, which combines a `LanguageModelSession` with the tools, captures the structured transcript, and reports in Xcode.
- **22:02 Synthetic data for tool evaluations** — because `ModelSample` and `TrajectoryExpectation` are `Generable`, you can synthesize tool-evaluation samples too — describe the available tools, order expectations, and context in the prompt, then validate that each sample has an expectation, ≥ 1 tool, and only real tools.
- **23:49 Next steps** — run `BookTaggingEvaluation` (what the model produces) and tool evaluations (how it gets there) in one suite for end-to-end confidence. Next: create your own synthetic data, evaluate your app's custom tools, explore the sample app and docs.

## Code

See `code.md` — 12 snippets extracted from the Code tab.
