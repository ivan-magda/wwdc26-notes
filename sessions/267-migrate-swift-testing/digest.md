---
title: "Migrate to Swift Testing — Full Digest"
session: WWDC26 · 267
url: https://developer.apple.com/videos/play/wwdc2026/267/
duration: 22m
speakers: Jerry (Swift Testing team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 267 — Migrate to Swift Testing

## TL;DR

The headline this year is **test framework interoperability**: in Xcode 27 you can call
XCTest API from inside a Swift Testing test (and Swift Testing API from inside an XCTest)
within the *same test target*, and the toolchain surfaces the resulting "cross-framework
issues" for you. That removes the all-or-nothing wall that used to make migration scary.

Four themes:

1. **Incremental migration, zero pressure** — leave existing XCTests where they are; write *new* tests in Swift Testing today (just not inside an `XCTestCase` class); migrate the rest a few at a time.
2. **Interoperability + its four modes** — Limited / Complete / Strict / None control how cross-framework issues from XCTest are reported (warning → error → fatal → silenced). Complete is the new default for Xcode 27 test plans. Issues going the *other* direction (Swift Testing API in an XCTest) are always errors.
3. **Common migration patterns** — `XCTFail` → `Issue.record`, `XCTSkip` → `Test.cancel` or `.enabled(if:)` traits, `continueAfterFailure = false` → `#require`.
4. **Swift Testing-only features** — parameterized tests (`@Test(arguments:)`, parallel, per-case results) and exit tests (`#expect(processExitsWith:)`, crash paths in a child process).

Demo app: a bird-training / fruit-delivery app whose `Fruit` and `Bird` types get migrated.

---

## 1. Swift Testing basics (and when to stay on XCTest)

A test is just a function annotated `@Test`, with `#expect` for assertions. Raw
identifiers (backticks) let test names contain spaces and punctuation for readability.
Tests don't need to live inside a suite type — you can drop a free function into an
existing file.

```swift
import Testing

@testable import DemoApp

@Test func `Default climate: tropical`() async throws {
    let fruit = Fruit(name: "Coconut")
    #expect(fruit.climate == .tropical)
}
```

`#expect` replaces most XCTest assertions; the unconditional `XCTFail` maps to
`Issue.record`.

**Keep XCTest for three cases:**
- UI automation APIs (XCTest only)
- Performance testing APIs (XCTest only)
- Code that throws **Objective-C exceptions** — those tests must be written in
  Objective-C XCTest, because Swift code (including Swift XCTests) can't safely handle
  ObjC exceptions.

New to the framework? The talk points at **"Meet Swift Testing"** (WWDC24).

## 2. Migration strategy

Modifying old tests introduces risk, so the recommendation is to **not** rewrite
everything:

- Leave most XCTests in place.
- Write all *new* tests in Swift Testing — a single test target can hold tests from both
  frameworks. (Caveat: Swift Testing tests can't live inside `XCTestCase` classes.)
- When you do migrate, go a few at a time and prioritize the tests you touch most often.

## 3. Test framework interoperability

The new feature lets you **safely call API from one framework inside the body of a test
belonging to the other**. Two directions:

- **XCTest API inside a Swift Testing test** — e.g. reusing an `assertUnique` helper that
  internally calls `XCTFail`.
- **Swift Testing API inside an XCTest** — e.g. `Issue.record`, `#expect`, `#require`,
  `Test.cancel`.

Either case produces a **cross-framework issue**: the issue-reporting API and the test it
runs in belong to different frameworks. Xcode enables interoperability by default to
handle these.

The worked example: a helper that detects duplicate fruit names via `XCTFail`.

```swift
func assertUnique(_ fruits: [Fruit], file: StaticString = #filePath, line: UInt = #line) {
    var uniqueNames = Set<String>()
    for name in fruits.map(\.name) {
        if !uniqueNames.insert(name).inserted {
            XCTFail("Duplicate name: \(name)", file: file, line: line)
        }
    }
}
```

Called from a *Swift Testing* test under the default Limited mode, the duplicate produces
**two purple-triangle warnings** (not errors, so the test still passes): one reporting the
duplicate, one instructing you to replace `XCTFail` with `Issue.record`.

## 4. The four interoperability modes

How **cross-framework issues from XCTest** are treated:

| Mode | XCTest → Swift Testing issue | Notes |
|------|------------------------------|-------|
| **Limited** | warning | Default for test plans created before Xcode 27; default in the Swift 6.4 toolchain / `swift-tools-version: 6.3`. |
| **Complete** | error | Default for new Xcode 27 test plans and `swift-tools-version: 6.4+`. A worthy step up — less likely to miss issues. |
| **Strict** | fatal error (halts the test where the XCTest API is called) | Use to hunt down every XCTest API call to replace. |
| **None** | silenced (both directions) | Opt out. Cross-framework issues can hide real app bugs, so use **temporarily** only; prefer Complete/Strict. |

**Important asymmetry:** issues in the *other* direction — Swift Testing API reporting
inside an XCTest — **remain errors in all modes**. So you're always free to call Swift
Testing API from either framework.

### Configuring the mode

- **Xcode:** Test Plan Settings → Test Execution section → filter "interoperability" → pick the mode.
- **SwiftPM:** controlled by `swift-tools-version` (6.3 = limited, bump to 6.4+ = complete).
- **Env var override (any time):** `SWIFT_TESTING_XCTEST_INTEROP_MODE`, value is the lowercase mode name:

```bash
SWIFT_TESTING_XCTEST_INTEROP_MODE=strict swift test
```

### Supported interop API surface

- **From XCTest:** `XCTFail` plus all other test assertions.
- **From Swift Testing:** `Issue.record`, both expectation macros (`#expect`, `#require`), the known-issue API (can mark XCTest assertion failures as known), and `Test.cancel` (can skip XCTest test cases).

### Migrating the helper

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

`Issue.record` replaces `XCTFail`; `SourceLocation` replaces the `file`/`line` parameter
pair. After this change, both the Swift Testing test and the original XCTest fail as
expected — the meaning is preserved, just expressed in Swift Testing API.

## 5. Common migration patterns

### Skipping tests: `XCTSkip` → `Test.cancel` → traits

```swift
let isFall = false

// XCTest
func testSwallowFallMigration() async throws {
    try XCTSkipIf(!isFall, "Wrong season for migration")
    // ...
}

// Test.cancel interoperability (works in both frameworks)
func testSwallowFallMigration() async throws {
    if !isFall {
        try Test.cancel("Wrong season for migration")
    }
    // ...
}

// ✅ Preferred: move enablement out of the body with a trait
@Test(.enabled(if: isFall, "Wrong season for migration"))
func `Swallow fall migration`() async throws {
   // ...
}
```

`Test.cancel` is the direct replacement and works in new Swift Testing tests too, but the
`.enabled(if:)` / `.disabled` **traits** are preferred because they lift enablement logic
out of the test body.

### Halting on failure: `continueAfterFailure = false` → `#require`

```swift
func testExample() async throws {
    #expect(Fruit.banana.climate == .temperate)

    try #require(Fruit.banana == Fruit.plantain)
    XCTFail("This is never reached")   // unreached: #require threw and halted the test
}
```

`#require` throws on failure, halting the test — no more `continueAfterFailure`. Bonus:
you choose *per expectation* whether it halts (`#require`) or merely records (`#expect`).

Reference for more scenarios: the **"Migrating a test from XCTest"** developer
documentation. Xcode's **Coding Assistant** knows this doc and has a **skill that can
automate parts of the migration**.

## 6. Parameterized tests

Tests that repeat with different arguments; each argument becomes a separate test case,
and all cases run **in parallel** by default.

Before — a nested loop hides which combination failed and runs serially:

```swift
@Test func `Birds flap wings successfully`() async throws {
    for bird in Aviary.birds {
        for count in (40...100) {
            try await bird.flapWings(count: count)
        }
    }
}
```

After — `@Test(arguments:)` pairs each bird with each count:

```swift
@Test(arguments: Aviary.birds, 40...100)
func `Birds flap wings successfully`(bird: Bird, count: Int) async throws {
    try await bird.flapWings(count: count)
}
```

Payoff in the demo: the parameterized run finishes "almost instantly" (parallel), each
combination shows under a disclosure arrow in the Test navigator, and filtering failures
pinpoints the exact input — *"the swallow can't flap its wings less than 43 times."* With
plain XCTest you'd have to catch the error or attach a debugger to learn that.

## 7. Exit tests

Cover code paths that **crash on purpose** — e.g. a `preconditionFailure` — by running the
crashing body in an isolated **child process**.

```swift
// Code under test, in Bird.init(...)
if name.isEmpty {
    preconditionFailure("Bird name cannot be empty")
}

// Exit test
extension BirdTests {
    @Test func `Bird with empty name crashes`() async throws {
        await #expect(processExitsWith: .failure) {
            _ = Bird(name: "")
        }
    }
}
```

`#expect(processExitsWith:)` takes an expected exit condition (`.failure`) and the body to
run; Swift Testing launches a child process, lets it crash without disrupting other tests,
waits, and checks the exit status. In the demo, the precondition line flips from **red to
green** in Code Coverage afterward.

**Platform limit:** exit tests are supported on **macOS, Linux, FreeBSD, and Windows**
only (not iOS).

## 8. Open source & next steps

- Swift Testing is open source under the **SwiftLang** GitHub org.
- **FreeBSD** gains full support this year (broadest platform coverage yet).
- Governed by the **Testing Workgroup** (open meetings); features go through **Swift
  Evolution** — interoperability itself was an evolution proposal.
- Recap of the fearless path: don't rewrite under pressure, write new tests in Swift
  Testing, lean on interoperability to reuse helpers, then upgrade to Complete/Strict mode
  to handle cross-framework issues. Closing pointer: **"Go further with Swift Testing"**
  (WWDC24).
