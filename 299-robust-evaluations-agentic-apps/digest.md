---
title: "Create robust evaluations for agentic apps — Full Digest"
session: WWDC26 · 299
url: https://developer.apple.com/videos/play/wwdc2026/299/
duration: 22m
speakers: Ada Wong, Kyle Murray
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 299 — Create robust evaluations for agentic apps

## TL;DR

A sequel to "Meet the Evaluations framework," focused on the **Develop-and-Evaluate**
step of hill climbing. Two problems, both solved with framework APIs that are pure Swift
and run from code or the command line:

1. **Your evaluation dataset is too small to trust.** A handful of hand-written samples
   makes a feature *look* good while hiding its real behavior. Generate **synthetic data**
   with `makeSamples` / `SampleGenerator`, validate it in code, and let the larger dataset
   expose the truth. In the demo, going from **13 → 100** BookTracker samples *dropped*
   the quality scores — which is the point.
2. **A plausible answer can come from the wrong path.** For agentic, tool-calling
   features you must evaluate the *how*, not just the *what*. **Tool evaluations** use a
   `TrajectoryExpectation` to assert which tools are called, with which arguments, in what
   order, and which tools must *not* be called — scored by `ToolCallEvaluator` and reported
   in Xcode alongside output evaluations.

Because `ModelSample` and `TrajectoryExpectation` are both `Generable`, the **same
synthetic-data machinery generates tool-evaluation samples too**. Running output evals
("what the model produces") and tool evals ("how it gets there") in one suite gives
end-to-end confidence.

Framework is new in **Xcode 27**; supports macOS, iOS, watchOS, visionOS. Running example:
**BookTracker**, a personal library app that auto-tags books from reviews and answers
library-assistant queries via custom tools.

---

## 1. Why a small dataset lies to you

BookTracker defines a `Book` (title, author, review, tags, rating) and ships **13**
hand-written `sampleBooks`. That feels reasonable but gives only a narrow window: there are
countless books, hundreds of genres, and endless ways a real user writes a review — often
vague or incomplete. "Our evaluation results could look great and still be completely
misleading." The quality of an evaluation is only as good as the data behind it, and good
eval data is hard to write by hand.

The fix is **synthetic data**, defined entirely in code so you can build a generation
pipeline, run it from the CLI, or fold it into existing workflows. It is text-based and
leans on the `@Generable` macro to produce *structured* samples.

## 2. The simple path — `makeSamples`

`makeSamples` needs three things: a **prompt**, a **dataset**, and a **target count** (the
size of the *full resulting* dataset, including your seeds).

```swift
let prompt = Prompt("""
    Generate diverse range of book reviews and corresponding tags.
    Cover a wide range of genres, time periods, cultures, and
    reader personas. Do not repeat books already in the dataset.
    """)

let dataset = Book.sampleBooks.map { book in
    ModelSample(prompt: book.review, expected: BookTags(tags: book.tags))
}

let targetCount = 100
var expandedDataset = dataset

for try await sample in dataset.makeSamples(prompt, targetCount: targetCount) {
    expandedDataset.append(sample)
    print("Generated \(expandedDataset.count) samples so far.")
}
```

- The new **`ModelSample`** packs the book's review as the prompt and its tags as the
  expected output.
- `targetCount: 100` with 13 seeds means the model generates **87 new** samples.
- `makeSamples` returns an **async stream**; you append each new sample as it arrives.
- By default generation runs on the **on-device model**.

**How much data is enough? "It depends."** Synthetic generation is iterative: define seeds
→ generate → validate → analyze representativeness → repeat until confident. The guiding
question is not *"how many samples do I need?"* but **"have I covered the meaningful variety
of ways this feature will actually be used?"** Coverage beats quantity.

## 3. The control path — `SampleGenerator`

For anything beyond prompt/dataset/target, `SampleGenerator` gives full control.

```swift
let generator = SampleGenerator<ModelSample<BookTags>>(
    prompt,
    samples: dataset,
    targetCount: targetCount,
    sessionProvider: {
        LanguageModelSession(
            model: PrivateCloudComputeLanguageModel(),
            instructions: """
                You are a synthetic data generator for a book-tracking app's evaluation suite.
                ...
                Rules:
                - Review must be at least 100 characters long.
                - Review should cover a mix of genre, mood/tone, and themes.
                - Reviews should vary in length.
                - Create between 3 and 8 tags.
                - Tags must be lowercase.
                """
        )
    }
)
```

### sessionProvider — pick the model and the instructions

The `sessionProvider` closure returns a `LanguageModelSession`, so you choose which model
drives generation and what system-level instructions frame the task. The demo uses
**`PrivateCloudComputeLanguageModel`** for its **larger context window** and adds rules that
focus generation on specific books, genres, and moods.

**Important lifecycle gotcha:** the framework auto-manages **batch size**. It calls your
`sessionProvider` **once** at the start of a run and **reuses** that session across batches
so the model keeps context. But a session has a size limit — heavy requests, large prompts,
or large outputs can **exhaust the context window mid-run and throw**. When that happens the
generator calls `sessionProvider` **again** for a fresh session (with **no** prior context).
So make your instructions **self-contained**; never assume the provider is called only once.
(See "Build agentic app experiences with Foundation Models" for more context-limit mitigations.)

### samplingStrategy — which seeds become in-context examples

Controls which of your initial samples are shown to the model as in-context examples:

- **`random`** (default) — a varied, de-duplicated subset; good when seed order is meaningless.
- **`slidingWindow`** — steps through seeds sequentially, skipping duplicates; use when your
  dataset has a **meaningful order**.

BookTracker uses `random` (and since it's the default, it isn't specified explicitly).

### Running it

```swift
for try await sample in generator.run() {
    expandedDataset.append(sample)
}
```

## 4. Validating synthetic samples

Instructions tell the model the rules; they don't *guarantee* compliance. The **`validator`**
closure accepts or rejects every generated sample with your own logic.

Two things to know before writing one:

- It validates **per sample, in isolation** — no access to the other samples.
- So only check what's **systematically checkable on one sample**. From BookTracker's five
  rules, three are mechanical (review ≥ 100 chars, 3–8 tags, lowercase tags); the other two
  — *diversity of reviews* (needs judgement) and *length variation* (needs assessment across
  all samples) — are **not** good fits for the closure.

```swift
validator: { sample in
    guard let book = sample.expected else { return false }
    guard sample.promptDescription.count >= 100 else { return false }   // ≥ 100 chars
    guard (3...8).contains(book.tags.count) else { return false }       // 3–8 tags
    guard book.tags.allSatisfy({ $0 == $0.lowercased() }) else { return false } // lowercase
    return true
}
```

Where results land: valid samples collect in `generator.samples`, rejects in
`generator.invalidSamples`. **Both update in real time** — read them during iteration to
watch progress, or after the loop. Then use the dataset in your app or save it locally.

```swift
let allSamples = await generator.samples
let invalidSamples = await generator.invalidSamples
```

## 5. Comparing runs — the score drop is a feature

Xcode 27 introduces a new **Evaluations Report**. Running `BookTaggingEvaluation` on the
original 13 samples gives high tag-quality scores (relevance + usefulness). Re-running on
the 100-sample dataset and hitting **Compare** shows the scores **drop** — exactly as
expected. The feature only *looked* good because it was never tested against a comprehensive
dataset.

A drop on a larger dataset is a **signal**, not a verdict, and could mean any of:

- a problem with your **prompt / instructions** (refine them),
- a gap in your **intelligence feature**,
- a mismatch in your **evaluation** (you're measuring the wrong thing), or
- a **dataset** that's still not representative (add samples / edge cases).

## 6. Tool evaluations — verify the *how*

Intelligence features often take many behind-the-scenes steps, each a tool call, to produce
output. **Tools** add structure to model workflows, operate on real data with your custom
business logic, and can mirror user-invokable functionality, new intelligence-only logic, or
both. The trap: **a model can return a reasonable-sounding answer without ever calling the
right tool** — the destination looks correct while the journey was wrong.

Three challenges tool evals address:

- **Instruction following** — you describe each tool; detail matters. (Try following your own
  instructions word-by-word to spot a missing step.)
- **Tool complexity** — from simple to fine-tuned parameter ranges.
- **Edge cases** — fine on common inputs, surprising on rare ones.

Tool evaluations verify the model calls the **correct tools, with the correct arguments, in
the order you expect**, and that no **unexpected** calls sneak in.

### BookTracker's library assistant

Three chained tools:

- **`searchBooks`** — find books with similar tags (filters: `query`, `tag`, `mood`, `genre`, `limit`).
- **`getBookDetails`** — extract metadata (e.g. publication date) for a found book.
- **`findSimilarBooks`** — semantic search for similar books.

```swift
@Generable
struct SearchBooksArguments {
    @Guide(description: "A freeform search term to match against titles, reviews, or tags")
    var query: String?
    @Guide(description: "Filter results to books with this specific tag")
    var tag: String?
    @Guide(description: "Filter results by mood")
    var mood: String?
    @Guide(description: "Filter results by genre")
    var genre: String?
    @Guide(description: "Maximum number of results to return. Defaults to 5.")
    var limit: Int?
}
```

All arguments are **optional** — the model decides which filters to populate from the user's
phrasing ("find gothic books" → `tag`; "show me something cheerful" → `mood`). Those choices
are exactly what we want to evaluate.

## 7. `TrajectoryExpectation` — the core of a tool eval

A session transcript interleaves prompts, responses, and tool calls. A `TrajectoryExpectation`
checks the **kind and order** of each tool call. (Mental model: reviewing the route decisions
of a trip — cars, bikes, buses each have a time and place, and you evaluate each leg.)

**Unordered, single tool:**

```swift
// "Find books tagged gothic" — we don't care *when*, just *that* it happens
TrajectoryExpectation(
    unordered: [
        ToolExpectation("searchBooks",
            arguments: [.exact(argumentName: "tag", value: .string("gothic"))])
    ]
)
```

**Fuzzy arguments with `.naturalLanguage`** — exact isn't always right; "cheerful" could be
`uplifting`/`happy`/`cheerful`:

```swift
TrajectoryExpectation(
    "searchBooks",
    arguments: [
        .naturalLanguage(argumentName: "mood",
            criteria: "Should relate to uplifting, hopeful, or positive feelings")
    ]
)
// Other matchers: .exact, .keyOnly, .contains, .oneOf, .pattern, .range, and more.
```

**Ordered (multistep)** — `searchBooks` must precede `getBookDetails`, otherwise there's no
`bookId` yet; that's a bug a trajectory check catches because it inspects the journey:

```swift
TrajectoryExpectation(
    ordered: [
        ToolExpectation("searchBooks",
            arguments: [.exact(argumentName: "tag", value: .string("gothic"))]),
        ToolExpectation("getBookDetails",
            arguments: [.keyOnly(argumentName: "bookId")])
    ]
)
```

**Disallowed tools** — what an agent *shouldn't* do matters too. "Don't look for similar
books" means `findSimilarBooks` appearing is a failure:

```swift
TrajectoryExpectation(
    unordered: [
        ToolExpectation("searchBooks",
            arguments: [.naturalLanguage(argumentName: "genre",
                criteria: "Should refer to science fiction")])
    ],
    disallowed: [ ToolExpectation("findSimilarBooks") ]
)
```

## 8. `ToolCallEvaluator` — the full evaluation

Bring expectations together into a dataset of samples (prompt + expectation), scored by
`ToolCallEvaluator`, which combines a `LanguageModelSession` with the tools, gets a response,
and captures the structured transcript. Results surface in the Xcode assistant alongside
your output evaluations.

```swift
let samples = SampleArrayLoader(samples: [
    ModelSample(
        prompt: "Find all the books tagged with 'gothic'.",
        instructions: "Help the user explore their book collection.",
        expectations: TrajectoryExpectation(  )
    )
])

struct BookLibraryToolCallEval: Evaluation {
    var dataset = samples
    let pass = Metric("All Passed")
    let percent = Metric("Percentage Passed")
    var evaluators: Evaluators {
        ToolCallEvaluator(allPass: pass, percentagePass: percent)
    }
}
```

## 9. Synthetic data for tool evaluations

Because `ModelSample` **and** `TrajectoryExpectation` are `Generable`, the same
`SampleGenerator` can synthesize *tool-evaluation* samples — useful because hand-expanding a
tool-eval dataset is laborious. The catch: **the model doesn't know your tools or their
ordering rules**, so you must spell them out in the instructions.

```swift
let prompt = Prompt("""
    Generate diverse user queries for a personal book library assistant.
    Each sample needs a prompt (what the user says), and a trajectory
    expectation describing which tools should be called and in what order.
    """)

let instructions = """
    AVAILABLE TOOLS:
    - searchBooks(query?, tag?, mood?, genre?, limit?): search the library
    - getBookDetails(bookId): full details for one book
    - findSimilarBooks(bookId, maxResults?): find books sharing tags
    ORDER REQUIREMENTS:
    - searchBooks must come before getBookDetails or findSimilarBooks
    - Use TrajectoryExpectation(ordered:) when sequence matters, else (unordered:)
    USE THESE ARGUMENT MATCHERS:
    - .exact for precise values, .naturalLanguage for fuzzy matching
    - .keyOnly when any value is acceptable, .range for numeric constraints
    - .contains/.hasPrefix/.hasSuffix for partial string matching
    """
```

Then reuse the existing dataset as seeds with `targetCount: 100`, and validate that each
synthetic sample has an expectation, references at least one tool, and uses only real tools:

```swift
validator: { sample in
    guard sample.output.expectations != nil else { return false }
    let expectations = sample.output.expectations!
    let totalExpectations = expectations.ordered.count + expectations.unordered.count
    guard totalExpectations > 0 else { return false }

    let validTools: Set<String> = ["searchBooks", "getBookDetails", "findSimilarBooks"]
    let allExpectations = expectations.ordered + expectations.unordered + expectations.disallowed
    for expectation in allExpectations {
        guard validTools.contains(expectation.name) else { return false }
    }
    return true
}
```

## 10. Bringing it together

- **Book tagging evaluation** checks **what** the model produces — tag count, genre
  coverage, quality scores.
- **Tool evaluations** check **how** it gets there — right tools, right arguments, right order.
- Run **both in one suite** for end-to-end confidence.

Next steps Apple suggests: make your own synthetic data, evaluate your app's custom tools,
and explore the BookTracker sample app and the developer documentation.
