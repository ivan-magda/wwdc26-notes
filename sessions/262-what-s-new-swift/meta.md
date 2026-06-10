# Session 262 — What's new in Swift

- **URL:** https://developer.apple.com/videos/play/wwdc2026/262/
- **Duration:** 32m
- **Speakers:** Becca (language + performance), Evan (libraries + cross-platform + future)

## Description

A tour of what landed during the development of Swift 6.3 and 6.4, in four parts:
everyday language improvements that remove small annoyances, updates to key libraries
(standard library, Swift Testing, Subprocess, Foundation), expanded reach beyond Xcode
and Apple platforms (C/Java interop, WebAssembly, embedded), and advanced performance
tuning (optimizer control plus extensions to the ownership system) — all without
compromising on safety.

## Key topics

- **Everyday language:** drop parentheses around `some`/`any` on optionals; warning on
  ignored errors thrown from a Task; `async` calls now allowed in `defer`; `weak let`;
  `~Sendable`; second (internal) memberwise initializer for mixed-visibility structs.
- **`anyAppleOS`** — one availability condition that condenses iOS/macOS/watchOS/tvOS/
  visionOS, also usable in `#if os(...)`, with per-platform carve-outs layered on top.
- **`@diagnose`** — per-declaration control to ignore, enable, or promote-to-error a
  named warning group (e.g. `DeprecatedDeclaration`, `StrictMemorySafety`).
- **Module selectors (`::`)** — `Module::Type` unambiguously names a module member when
  `Module.Type` is ambiguous (Swift 6.3).
- **Standard library:** `withTaskCancellationShield`, `mapKeyedValues`, cross-platform
  `FilePath` (from Swift System).
- **Swift Testing:** issue severity (`.warning`), `Test.cancel`, repeat-until-pass/fail
  for flaky tests, two-way XCTest interop.
- **Subprocess 1.0:** refined execution type, `AsyncBufferSequence`, `strings()` line
  streaming, better cross-platform semantics.
- **Foundation:** `ProgressManager`; continued Swift migration of Data, NSURL/CFURL.
- **Cross-platform:** `@C` attribute (export Swift to C), Swift-Java (async/throws,
  constrained extensions, conform Java classes to Swift protocols), official Android
  SDK on swift.org, VSCode extension + Swiftly + OpenVSX, WebAssembly + JavaScriptKit
  (35–40x faster safe bridging), Embedded Swift (existentials, untyped throws, DWARF).
- **Performance:** `@inline(always)`, `@specialized`; ownership for Equatable/Comparable/
  Hashable on noncopyable/non-escapable types; `Iterable` protocol; `borrow`/`mutate`
  accessors; new types `UniqueBox`, `UniqueArray`, `Continuation`, `Ref`/`MutableRef`.

## Related sessions to fetch (referenced in this talk)

- [ ] Migrate to Swift Testing

## Chapter summary (Summary tab)

- **0:07 Introduction** — Becca & Evan preview four topics: language improvements,
  library updates, cross-platform support, and performance tuning in Swift 6.3/6.4.
- **0:44 Everyday Language Improvements** — optional-paren removal, ignored-Task-error
  warning, `async` in `defer`, `weak let`, `~Sendable`, second memberwise initializer.
- **1:55 anyAppleOS Availability** — condense multi-platform availability attributes
  into a single `anyAppleOS` condition (also `#if os(anyAppleOS)`).
- **3:02 @diagnose Attribute** — fine-grained per-declaration control to suppress,
  enable, or promote warnings to errors.
- **3:52 Module Selectors (::)** — double-colon syntax to reference a type/member from a
  specific module when names conflict (Swift 6.3).
- **5:59 Library Updates** — standard library, Swift Testing, Subprocess, Foundation.
- **6:16 Standard Library** — task cancellation shield, `mapKeyedValues`, `FilePath`.
- **7:31 Swift Testing Updates** — issue severity, dynamic cancellation, flaky-test
  repetition, two-way XCTest interop.
- **9:29 Subprocess 1.0** — refined API, error handling, line-by-line streaming,
  expanded cross-platform support.
- **10:14 Foundation** — `ProgressManager`; Data/NSURL/CFURL Swift migration + perf.
- **11:59 Beyond Apple Platforms** — interop + web/Android/embedded reach.
- **12:35 Swift–C Interoperability (@C)** — expose Swift functions to C for incremental
  migration; `@implementation`, span bridging, generated C interop header.
- **15:09 Swift-Java** — async/throwing functions, constrained extensions, conform Java
  classes to Swift protocols; official Android SDK on swift.org.
- **16:03 Editor support** — Swiftly toolchain integration, OpenVSX (Cursor, VSCodium,
  Kiro, Antigravity), getting-started checklist.
- **16:44 WebAssembly & JavaScriptKit** — compile to Wasm; up to 40x faster safe
  bridging (Goodnotes case study).
- **18:08 Embedded Swift** — existential types, untyped throws, DWARF debug info for
  coredump debugging; `EmbeddedRestrictions` warning group.
- **19:59 Performance Tuning** — explicit optimizer control + ownership extensions.
- **21:29 Optimizer Control** — `@inline(always)` and `@specialized`.
- **24:29 Ownership System & Noncopyable Types** — Equatable/Comparable/Hashable and
  associated types now work with noncopyable/non-escapable types.
- **26:18 Iterable Protocol & Borrow/Mutate Accessors** — borrow-based `for` loops over
  noncopyable elements; `borrow`/`mutate` accessors eliminate copies.
- **28:57 New Standard Library Types** — `UniqueBox`, `UniqueArray`, `Continuation`,
  `Ref`/`MutableRef`.
- **31:11 The Future of Swift** — Swift Build is now the default SwiftPM backend; new
  build, networking, Windows, Android workgroups; forums.swift.org invitation.

## Code

See `code.md` — 36 snippets extracted from the Code tab.
