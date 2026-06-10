# Code samples — Session 298

Extracted from the Code tab. Timestamps map loosely to the transcript.

## Conforming to the Evaluation protocol

```swift
// Evaluations
import Evaluations

struct BookTaggingEvaluation: Evaluation {

}
```

## TagCount metric + range evaluator

```swift
let tagCount = Metric("TagCount")

var evaluators: Evaluators {

    // Tag count is within the required 3–8 range
    Evaluator { _, subject in
        let count = subject.value.tags.count
        if (count >= 3 && count <= 8) {
            return tagCount.passing(rationale: "\(count) tags")
        }
        return tagCount.failing(rationale: "Got \(count) tags, expected 3–8")
    }
}
```

## Dataset of ModelSample inputs (inline and from the library)

```swift
// BookTaggingEvaluation
var dataset = ArrayLoader(samples: [
    ModelSample(prompt: "okay I am OBSESSED and I need everyone to read this RIGHT NOW...",
                expected: BookTags(tags: ["classic", "romance", "wit", "regency"])),

    ModelSample(prompt: "Read this in one sitting between midnight and 4am and I cannot...",
                expected: BookTags(tags: ["classic", "gothic", "horror", "vampire", "suspense"])),
])

// Or load your whole library:
var dataset = ArrayLoader(samples:
    Book.sampleBooks.map { book in
        ModelSample(prompt: book.review, expected: BookTags(tags: book.tags))
    }
)
```

## Aggregating metrics (mean, distribution)

```swift
let tagCount = Metric("TagCount")
let tagTotal = Metric("TagTotal")

func aggregateMetrics(using aggregator: inout MetricsAggregator) {
    aggregator.computeMean(of: tagCount)
    aggregator.group("Distribution of Tag Totals") { aggregator in
        aggregator.computeStandardDeviation(of: tagTotal)
        aggregator.computeMean(of: tagTotal)
        aggregator.computeVariance(of: tagTotal)
    }
}
```

## Optimization target in a Swift Testing test

```swift
// Optimization Target
@Test("Book Tag Evaluations", .evaluates(evaluation, info: evaluationInfo))
func evaluateBookTagging() async throws {
    let result = EvaluationContext.current.result

    let rangeMetric = BookTagEvaluationTests.evaluation.tagCount
    #expect(result.aggregateValue(.mean(of: rangeMetric)) >= 0.8)
}
```

## Constraining tag count on the @Generable type (the hill-climbing change)

```swift
// BookTags.swift
@Generable
struct BookTags: Codable {
    @Guide(description: "Descriptive tags capturing themes, genres, moods, and topics from the summary", .count(3...8))
    var tags: [String]
}
```

## Service instructions (Instructions builder)

```swift
// BookTaggingService.swift
let instructions = Instructions {
    """
    You are a librarian and literary analyst. Given a reader's
    freeform summary of a book they read — describing their
    thoughts, feelings, and what stood out — generate a set of
    descriptive tags reflected in the summary.

    Rules:
     - Return between 3 and 8 tags.
     - Tags should be lowercase, concise (single word or hyphenated), and descriptive.
     - Tags should include the book's genre, chosen from the included list of known genres.

    Known Genres:
     - \(Self.knownGenres.joined(separator: ", "))
    """
}
```

## Word-count and genre evaluators

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

## Synthesizing more samples with makeSamples

```swift
// Synthesizing more inputs
let samples: [ModelSample<String>] = [
    ModelSample(prompt: "The largest planet in our solar system...", expected: "Jupiter."),
    ModelSample(prompt: "The capital of Thailand...", expected: "Bangkok."),
    ModelSample(prompt: "Swift is...", expected: "a powerful programming language."),
    ModelSample(prompt: "All those moments will be lost in time...", expected: "Like tears in rain.")
]

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

## A simple model judge (TagQuality, 1–4 scale, PCC)

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

## Defining a ScoreDimension (Relevance)

```swift
// BookTaggingEvaluation.swift
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
// Define `usefulness` the same way as a second ScoreDimension.
```

## Mixing quantitative evaluators with a multi-dimension judge

```swift
// BookTaggingEvaluation.swift
var evaluators: Evaluators {

    Evaluator {  }

    Evaluator {  }

    Evaluator {  }

    ModelJudgeEvaluator(
        judge: PrivateCloudComputeLanguageModel(),
        dimensions: [relevance, usefulness]
    )
}
```

## Adding a ModelJudgePrompt for app context

```swift
// BookTaggingEvaluation.swift
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
