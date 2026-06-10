# Code samples — Session 267

Extracted from the Code tab. Timestamps map to the transcript.

## 1:19 — A basic Swift Testing test (`@Test` + raw identifier + `#expect`)

```swift
import Testing

@testable import DemoApp

@Test func `Default climate: tropical`() async throws {
    let fruit = Fruit(name: "Coconut")
    #expect(fruit.climate == .tropical)
}
```

## 5:16 — Helper that wraps XCTFail (the "before" version)

```swift
func testUniqueFruitNames() async throws {
    assertUnique(Market.fruits + [Fruit.lychee])
}

// TestHelpers.swift

func assertUnique(_ fruits: [Fruit], file: StaticString = #filePath, line: UInt = #line) {
    var uniqueNames = Set<String>()
    for name in fruits.map(\.name) {
        if !uniqueNames.insert(name).inserted {
            XCTFail("Duplicate name: \(name)", file: file, line: line)
        }
    }
}
```

## 10:15 — Helper migrated to Issue.record + SourceLocation (the "after" version)

```swift
import Testing

func assertUnique(_ fruits: [Fruit], sourceLocation: SourceLocation = ...) {
    var uniqueNames = Set<String>()
    for name in fruits.map(\.name) {
        if !uniqueNames.insert(name).inserted {
            Issue.record("Duplicate name: \(name)", sourceLocation: sourceLocation)
        }
    }
}
```

## 12:16 — Overriding the interop mode for `swift test`

```bash
SWIFT_TESTING_XCTEST_INTEROP_MODE=strict swift test
```

## 13:09 — Skipping tests: XCTSkipIf → Test.cancel → trait

```swift
let isFall = false

// XCTest
func testSwallowFallMigration() async throws {
    try XCTSkipIf(!isFall, "Wrong season for migration")
    // ...
}

// Test.cancel interoperability from Swift Testing
func testSwallowFallMigration() async throws {
    if !isFall {
        try Test.cancel("Wrong season for migration")
    }
    // ...
}

// ✅ Prefer test trait in Swift Testing
@Test(.enabled(if: isFall, "Wrong season for migration"))
func `Swallow fall migration`() async throws {
   // ...
}
```

## 13:54 — Halting on failure: continueAfterFailure → #require

```swift
func testExample() async throws {
    #expect(Fruit.banana.climate == .temperate)

    try #require(Fruit.banana == Fruit.plantain)
    XCTFail("This is never reached")
}
```

## 16:10 — Loop-based test (the "before" parameterized version)

```swift
struct BirdTests {

    @Test func `Birds flap wings successfully`() async throws {
        for bird in Aviary.birds {
            for count in (40...100) {
                try await bird.flapWings(count: count)
            }
        }
    }

}
```

## 17:00 — Parameterized test with @Test(arguments:)

```swift
struct BirdTests {

    @Test(arguments: Aviary.birds, 40...100)
    func `Birds flap wings successfully`(bird: Bird, count: Int) async throws {
        try await bird.flapWings(count: count)
    }

}
```

## 18:22 — The code under test (a precondition crash path)

```swift
// In `Bird.init(...)`
if name.isEmpty {
    preconditionFailure("Bird name cannot be empty")
}
```

## 19:19 — Exit test covering the crash path

```swift
extension BirdTests {

    @Test func `Bird with empty name crashes`() async throws {
        await #expect(processExitsWith: .failure) {
            _ = Bird(name: "")
        }
    }

}
```

---

## Useful API facts surfaced by the code

- Raw identifiers (backticked names with spaces/punctuation) work as test function names.
- `Issue.record(_:sourceLocation:)` replaces `XCTFail(_:file:line:)`; `SourceLocation` replaces the `file`/`line` pair.
- `Test.cancel(_:)` skips a test case and works inside both XCTest and Swift Testing tests; the trait `.enabled(if:_:)` (and `.disabled`) is the preferred Swift Testing form.
- `#require` throws on failure, halting the test — the Swift Testing replacement for `continueAfterFailure = false`.
- `@Test(arguments: a, b)` produces the Cartesian pairing of `a` and `b`, each combination a separate parallel case.
- Exit tests: `#expect(processExitsWith: .failure) { ... }` is `await`ed and runs its body in a child process; `.failure` is the expected exit condition.
- Interop env var: `SWIFT_TESTING_XCTEST_INTEROP_MODE` takes a lowercase mode name (`limited`/`complete`/`strict`/`none`).
