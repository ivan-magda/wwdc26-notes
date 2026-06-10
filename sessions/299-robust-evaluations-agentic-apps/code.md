# Code samples — Session 299

Extracted from the Code tab of the session page. Snippet 1 in the raw extraction
concatenated the simple `makeSamples` call with the start of the custom
`SampleGenerator` config; they are split below. Slide-number hints from the
extraction are preserved where present.

## Generating synthetic data with makeSamples

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

## Configuring a custom SampleGenerator (sessionProvider) — slides 30–43

```swift
// Define your own configuration
let generator = SampleGenerator<ModelSample<BookTags>>(
    prompt,
    samples: dataset,
    targetCount: targetCount,
    sessionProvider: {
        LanguageModelSession(
            model: PrivateCloudComputeLanguageModel(),
            instructions: """
                You are a synthetic data generator for a book-tracking app's evaluation suite.
                Your job is to produce realistic, diverse book entries that will stress-test
                a tagging system.

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

## Validating generated samples (validator closure)

```swift
// Define validation metrics
validator: { sample in
    guard let book = sample.expected else { return false }

    // Review must be at least 100 characters
    guard sample.promptDescription.count >= 100 else { return false }

    // Must have between 3 and 8 tags
    guard (3...8).contains(book.tags.count) else { return false }

    // All tags must be lowercase
    guard book.tags.allSatisfy({ $0 == $0.lowercased() }) else { return false }

    return true
}
```

## Accessing results (run, samples, invalidSamples)

```swift
for try await sample in generator.run() {
    // During iteration
    expandedDataset.append(sample)
}

// After iteration
let allSamples = await generator.samples
let invalidSamples = await generator.invalidSamples

print("Generated \(allSamples.count) new samples. Total: \(expandedDataset.count)")
```

## SearchBooks tool — Generable arguments

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

## TrajectoryExpectation — exact argument match

```swift
// "Find books tagged gothic"
TrajectoryExpectation(
    unordered: [
        ToolExpectation(
            "searchBooks",
            arguments: [
                .exact(argumentName: "tag", value: .string("gothic"))
            ]
        )
    ]
)
```

## TrajectoryExpectation — naturalLanguage matcher

```swift
// "Find something cheerful"
TrajectoryExpectation(
    "searchBooks",
    arguments: [
        .naturalLanguage(
            argumentName: "mood",
            criteria: "Should relate to uplifting, hopeful, or positive feelings"
        )
    ]
)
// Other matchers available: .contains, .oneOf, .pattern, .range, and more.
```

## TrajectoryExpectation — ordered (multistep) expectations

```swift
// "Find gothic books and show details on the first"
TrajectoryExpectation(
    ordered: [
        ToolExpectation(
            "searchBooks",
            arguments: [
                .exact(argumentName: "tag", value: .string("gothic"))
            ]
        ),
        ToolExpectation(
            "getBookDetails",
            arguments: [
                .keyOnly(argumentName: "bookId")
            ]
        )
    ]
)
```

## TrajectoryExpectation — disallowed tools

```swift
// "Show only sci-fi books. Don't look for similar ones."
TrajectoryExpectation(
    unordered: [
        ToolExpectation(
            "searchBooks",
            arguments: [
                .naturalLanguage(
                    argumentName: "genre",
                    criteria: "Should refer to science fiction")
            ]
        )
    ],
    disallowed: [
        ToolExpectation("findSimilarBooks")
    ]
)
```

## ToolCallEvaluator — the full tool evaluation

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

## Synthetic data for tool evals — prompt + instructions

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
    - searchBooks must comes before getBookDetails or findSimilarBooks
    - Use TrajectoryExpectation(ordered:) when sequence matters, else (unordered:)
    USE THESE ARGUMENT MATCHERS:
    - .exact for precise values, .naturalLanguage for fuzzy matching
    - .keyOnly when any value is acceptable, .range for numeric constraints
    - .contains/.hasPrefix/.hasSuffix for partial string matching
    """
```

## Synthetic data for tool evals — validator

```swift
validator: { sample in
    // Must have expectations defined
    guard sample.output.expectations != nil else { return false }

    let expectations = sample.output.expectations!

    // Must reference at least one tool
    let totalExpectations = expectations.ordered.count + expectations.unordered.count
    guard totalExpectations > 0 else { return false }

    // All tool names must be from the valid set
    let validTools: Set<String> = ["searchBooks", "getBookDetails", "findSimilarBooks"]
    let allExpectations = expectations.ordered + expectations.unordered + expectations.disallowed
    for expectation in allExpectations {
        guard validTools.contains(expectation.name) else { return false }
    }

    return true
}
```
