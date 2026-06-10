# Session 267 — Migrate to Swift Testing

- **URL:** https://developer.apple.com/videos/play/wwdc2026/267/
- **Duration:** 22m
- **Speakers:** Jerry (engineer on the Swift Testing team)

## Description

How to fearlessly migrate from XCTest to Swift Testing using the new test framework
interoperability feature in Xcode 27. The session reviews Swift Testing's core building
blocks (`@Test`, `#expect`), lays out an incremental migration strategy, walks through
the four interoperability modes (Limited, Complete, Strict, None), covers common
migration patterns (`XCTSkip` → `Test.cancel`/traits, `continueAfterFailure` →
`#require`), and closes with two Swift Testing superpowers: parameterized tests and
exit tests.

## Key topics

- Swift Testing basics: `@Test` macro, raw identifiers (backticked test names), `#expect`; `XCTFail` → `Issue.record`
- When to keep XCTest: UI automation, performance testing, and Objective-C exception testing (must be Objective-C XCTests)
- Migration strategy: leave existing XCTests in place; write new tests in Swift Testing now (just not inside XCTest classes); migrate a few at a time, prioritizing frequently-edited tests
- Test framework interoperability: call API from one framework inside a test belonging to the other, producing "cross-framework issues"; two directions (XCTest API in a Swift Testing test, and vice versa)
- Four interoperability modes — Limited (XCTest issues are warnings), Complete (warnings become errors; default for new test plans / Xcode 27), Strict (fatal error that halts the test), None (opt out; temporary use only). Swift Testing → XCTest issues stay errors in all modes.
- Configuration: Test Plan Settings → Test Execution → filter "interoperability"; SwiftPM via `swift-tools-version` (6.3 → limited, bump to 6.4+ → complete); `SWIFT_TESTING_XCTEST_INTEROP_MODE` env var (lowercase mode name)
- Supported interop API surface: all XCTest assertions; `#expect` + `#require`; known-issue API; `Test.cancel`
- Common patterns: `XCTSkip`/`XCTSkipIf` → `Test.cancel` or `.enabled(if:)`/`.disabled` traits; `continueAfterFailure = false` → `#require`
- Parameterized tests: `@Test(arguments:)` replaces nested loops; cases run in parallel; per-case pass/fail in the Test navigator
- Exit tests: `#expect(processExitsWith:)` runs a crashing body in a child process to cover `precondition`/`preconditionFailure` paths (macOS, Linux, FreeBSD, Windows only)
- Open source: Swift Testing lives in the SwiftLang GitHub org; FreeBSD full support added this year; governed by the Testing Workgroup; features via Swift Evolution (interoperability was one); Xcode Coding Assistant has a migration skill

## Related sessions to fetch (referenced in this talk)

- [ ] Meet Swift Testing (WWDC24)
- [ ] Go further with Swift Testing (WWDC24)
- [ ] "Migrating a test from XCTest" — developer documentation (not a session)

## Chapter summary (Summary tab)

- **0:07 Introduction** — How to fearlessly migrate from XCTest to Swift Testing using the new interoperability feature.
- **1:08 Swift Testing basics** — A quick review of core Swift Testing building blocks: the `@Test` macro, `#expect`, and how they compare to XCTest assertions.
- **2:50 Migration strategy** — The recommended incremental approach: leave existing XCTests in place, and start writing new tests in Swift Testing right away.
- **5:48 Test framework interoperability** — The interoperability feature that lets you safely call XCTest or Swift Testing API from within a test belonging to the other framework.
- **7:43 Interoperability modes** — The four interoperability modes — Limited, Complete, Strict, and None — and how to configure them in Xcode Test Plans and Swift packages.
- **13:02 Common migration patterns** — Practical patterns you will encounter during migration, including replacing `XCTSkip` with `Test.cancel` or traits, and `continueAfterFailure` with `#require`.
- **15:34 Parameterized tests** — How to replace loop-based XCTest cases with Swift Testing parameterized tests for faster parallel execution and clearer failure reporting.
- **18:02 Exit tests** — Using Swift Testing exit tests to cover code paths that call `preconditionFailure` or crash, running them safely in a child process.
- **20:04 Next steps** — Recaps the migration path, highlights Swift Testing open-source availability and cross-platform support, and encourages community participation.

## Code

See `code.md` — 10 snippets extracted from the Code tab.
