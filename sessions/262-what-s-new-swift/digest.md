---
title: "What's new in Swift — Full Digest"
session: WWDC26 · 262
url: https://developer.apple.com/videos/play/wwdc2026/262/
duration: 32m
speakers: Becca, Evan
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 262 — What's new in Swift

## TL;DR

This is the omnibus "what's new in Swift" talk for **Swift 6.3 and 6.4**, split across
four acts. Four headline themes:

1. **Less ceremony in everyday code** — drop parentheses around `some`/`any` on
   optionals, get warned about silently-ignored Task errors, call `async` in `defer`,
   use `weak let`, mark types `~Sendable`, and lean on a second memberwise initializer.
   Two bigger wins: **`anyAppleOS`** collapses the multi-platform availability soup into
   one condition, and **`@diagnose`** gives per-declaration control over warnings.
2. **Libraries keep maturing** — `withTaskCancellationShield`, `mapKeyedValues`, and a
   cross-platform `FilePath` in the standard library; Swift Testing gains issue
   severity, dynamic cancellation, flaky-test repetition, and **two-way XCTest interop**;
   **Subprocess hits 1.0**; Foundation adds `ProgressManager` and keeps migrating to
   Swift (Data, NSURL/CFURL) for real performance wins.
3. **Swift goes everywhere** — a new **`@C` attribute** exports Swift functions to C
   (the `@objc` story, for C); Swift-Java gains async/throws + protocol conformance;
   an **official Android SDK** ships on swift.org; the VSCode extension lands on
   **OpenVSX** (Cursor, VSCodium, Kiro, Antigravity) with Swiftly toolchain management;
   **WebAssembly** is real with JavaScriptKit bridging **35–40x faster** (Goodnotes
   case study); and **Embedded Swift** grows existentials, untyped throws, and DWARF
   coredump debugging.
4. **Opt-in performance without losing safety** — `@inline(always)` and `@specialized`
   hand you the optimizer's steering wheel, and the **ownership system** takes several
   steps forward: noncopyable/non-escapable `Equatable`/`Comparable`/`Hashable`, a new
   **`Iterable`** protocol for borrow-based `for` loops, **`borrow`/`mutate` accessors**,
   and new types **`UniqueBox`, `UniqueArray`, `Continuation`, `Ref`/`MutableRef`**.

The running example throughout is a rocket-launch scheduling app.

---

## 1. Everyday language improvements (Swift 6.4)

A grab bag of annoyances removed:

- **Optional `some`/`any` without parentheses** — previously operator precedence forced
  `(any P)?`; now `any P?` resolves to the only sensible reading.
- **Ignored Task error warning** — silently dropping an error thrown from a Swift
  Concurrency `Task` now warns, nudging you to handle it in the task or save the task
  and check later.
- **`async` calls allowed in `defer`** — the old restriction is gone.
- **`weak let`** — a class that needed `@unchecked Sendable` only because of a `weak var`
  can switch to immutable `weak let` and pass `Sendable` checking honestly.
- **`~Sendable`** — explicitly state a type should *not* be `Sendable`; as a bonus,
  subclasses can still opt back into `Sendable`.
- **Second memberwise initializer** — a struct mixing `internal` and `private`
  properties now also gets an `internal` memberwise init usable from other files.

```swift
let landingTask = Task {
    try lander.fly(to: moon)
}

defer {
    await orbiter.rendezvous(with: lander)   // async in defer now OK
}

try await orbiter.justHangOut(waitingFor: landingTask)
```

```swift
final class Spacecraft: Sendable {
    weak let dockedAt: SpaceStation?          // weak let
}

class Mission: ~Sendable { ... }              // explicit non-Sendable
class CrewedMission: Mission, @unchecked Sendable { ... }   // subclass can opt back in
```

## 2. anyAppleOS availability

The OS-version alignment Apple started "last year" is now exploited by the language:
condense `@available(macOS 27, iOS 27, watchOS 27, tvOS 27, visionOS 27, *)` into a
single **`anyAppleOS`** condition. It works as the default with per-platform carve-outs
layered on top, and also drives **`#if os(anyAppleOS)`**.

```swift
extension Mission {
    @available(anyAppleOS 27, *)
    func showStatus() { ... }

    @available(anyAppleOS 27, *)
    @available(tvOS, unavailable)         // carve-out
    func launch() { ... }

    #if os(anyAppleOS)
    func makeLiveActivityWidget() -> some Widget { ... }
    #endif
}
```

## 3. @diagnose — per-declaration warning control

A single attribute to **ignore**, **enable**, or **promote-to-error** a named warning
group inside one declaration, without touching the rest of the project:

```swift
@diagnose(DeprecatedDeclaration, as: ignored, reason: "Flying with surplus hardware")
func makeApolloSoyuzMission() -> Mission { ... }

@diagnose(StrictMemorySafety, as: warning)   // turn on a normally-off warning here
func uplinkCommand(...) { ... }

@diagnose(ErrorInFutureSwiftVersion, as: error)   // adopt a future error now
func fetchPosition() -> (x: Double, y: Double, z: Double) { return self.rotation }
```

Evan reuses this later: `@diagnose` is also the tool for working around the
`EmbeddedRestrictions` warning group in mixed embedded/full-Swift libraries.

## 4. Module selectors (`::`) — Swift 6.3

When two imported modules both export `SaturnV`, `Module.Type` dot syntax can break down
(e.g. if module `Rocket` *also* contains a type `Rocket`, since Swift prefers type names
over module names). The new **module selector** `Module::Member` always treats the
left-hand name as a module:

```swift
let rocket1 = SaturnV()            // ambiguous error
let rocket2 = Rocket.SaturnV()     // prefers `Rocket::Rocket.SaturnV` — wrong
let rocket3 = Rocket::SaturnV()    // correctly finds `Rocket::SaturnV`
```

It also disambiguates members (e.g. two `fire()` extension methods):
`launchPadTechnician.HumanResources::fire()`. Apple's guidance: great for conflicts you
don't control (think SwiftUI vs. a database package both shipping `View`) and defensively
in macro-generated code — but **don't** intentionally design conflicting APIs and lean
on `::`.

## 5. Standard library

```swift
// Cancellation shield — finish/rollback work even after cancellation
withTaskCancellationShield {
    radio.send(makeSOSPacket())
}
```

- **`withTaskCancellationShield`** — inside it, cancellation checks always return false.
  Keep the region short (finish writing to disk, roll back partial work).
- **`mapKeyedValues`** — like `mapValues` but the closure also receives the **key**, so
  you no longer hand-build a dictionary when the key matters.
- **`FilePath`** — a cross-platform path type based on Swift System, handling
  platform-specific quirks (the demo shows macOS named-resource forks being dropped from
  `.components`).

## 6. Swift Testing (Swift 6.4)

- **Issue severity** — `Issue.record(..., severity: .warning)` surfaces non-fatal
  findings without failing the test / blocking CI.
- **`Test.cancel`** — dynamically skip a test (or a single parameterized argument)
  instead of running it or failing.
- **Flaky-test repetition** — `swift test` can repeat until pass or until fail, with a
  max-repetitions cap; "repeat until pass" only re-runs the still-failing cases.
- **Two-way XCTest interop** — XCTest assertion failures are reported as Swift Testing
  issues, and `#expect` works inside an `XCTestCase`. Both directions report as
  **warnings by default**; you can promote them to failures in Xcode build settings.
  Migration-focused, with a pointer to **"Migrate to Swift Testing."**

## 7. Subprocess 1.0

Last year's package reaches 1.0 with a simplified execution type, better error handling,
and convenience streaming. Output/error streams are exposed on the execution object as
**`AsyncBufferSequence`** (created once each); the new **`strings()`** method reads
output line-by-line and respects grapheme-cluster boundaries so multi-byte characters
aren't split. Cross-platform semantics improved (process file descriptors, termination
statuses).

```swift
let result = try await Subprocess.run(.name("ls"),
                                      input: .none,
                                      output: .sequence,
                                      error: .string(limit: 4096)) { execution in
    execution.standardOutput.strings().filter { $0.hasSuffix(".obj") }
}

for try await objectFile in result.closureOutput {
    print("Object file: \(objectFile)")
}
```
*(source extraction has `standardOtput`/`objectFiles` typos — corrected above)*

## 8. Foundation

- **`ProgressManager`** — a new progress-reporting type built for async/await that
  separates progress *composition* from *reporting* and supports type-safe metadata
  (the demo attaches per-stage `deltaV` and later summarizes it).
- **Continued Swift migration** — decades of Objective-C replaced with Swift: faster
  `Data` span access, equality, iteration, and mutation; faster `Data`↔`NSData`
  bridging; **`NSURL` and `CFURL` unified into one Swift implementation** (faster, less
  memory). Apple's framing: this is only possible *because* of Swift's interop — you can
  migrate an implementation without changing the API surface.

```swift
let manager = ProgressManager(totalCount: 100)
try await rocket.launch(mission.subprogress(assigningCount: 100))

Task {
    for await update in Observations({ mission.fractionCompleted }) {
        print("🚀 Mission \(Int(update * 100))%")
    }
}
```

## 9. Beyond Apple platforms

Apple frames Swift as a full-stack language ("apps like Weather, services like realtime
phone-call spam detection, the kernel, down to firmware").

### Swift → C with `@C`

The mirror image of `@objc`. `@C` exports a Swift function to C; it applies to functions
over C-compatible types (integers, pointers, imported C structs, raw-value enums), and
the compiler blocks incompatible types.

- Pair **`@C` + `@implementation`** to implement a C function already declared in a
  header (no new C declaration needed).
- For brand-new functions, the compiler emits a declaration into the generated C interop
  header so C can call back in.
- Safe interop bridges Swift **`Span`** to C automatically — pass a span instead of
  array+count. C++ interop also bridges to **C++20 spans**.
- Pitch: incremental, memory-safe migration of C codebases to Swift.

### Swift-Java + Android

Swift-Java now calls **async and throwing** Swift functions from Java, captures more of
the generics system (**constrained extensions**), and lets **Java classes conform to
Swift protocols** — making Swift feel natural from Java/Kotlin on Android. An **official
Swift SDK for Android** is now on swift.org (shipped as part of Swift 6.3), so Swift code
can be shared between Android and iOS.

### Editor support

The VSCode Swift extension integrates **Swiftly** for installing toolchains from
swift.org in-editor, and is now on the **OpenVSX** marketplace — bringing it to
**VSCodium, Cursor, Kiro, and Antigravity**. A getting-started checklist walks through
install → new project → run → tests → docs.

### WebAssembly + JavaScriptKit

The open-source toolchain can compile to **Wasm** — same language for native apps,
backend servers, and frontend. **JavaScriptKit** bridging is now safer and faster:
**Goodnotes** compiled their existing Swift code to Wasm for a web interface and
benchmarked safe bridging at **35–40x faster** than the old dynamic bridging. Caveat:
Wasm binaries ship to every visitor, so **binary size matters more than ever**.

### Embedded Swift

Growing the language subset that fits constrained hardware:

- **Existential types** — store multiple protocol-conforming types in an array / pass to
  a function.
- **Untyped throws** — built on the same existential machinery.
- **DWARF debug info** — type-layout metadata moved into DWARF (not the binary), keeping
  size down while making **coredump debugging** far better (embedded systems often only
  leave a core dump).
- The **`EmbeddedRestrictions`** warning group flags features unavailable in embedded
  contexts; `@diagnose` lets mixed libraries manage those diagnostics.

## 10. Performance tuning — optimizer control

Opt-in, "you usually won't need these" features. The compiler's most powerful
optimizations (inlining, specialization) duplicate code and can backfire — bigger and
slower instead of smaller and faster — so 6.3/6.4 hand you the controls:

- **`@inline(always)`** (new, 6.4) — forces inlining even when the optimizer is unsure;
  pairs with the long-standing **`@inline(never)`**. For class methods that might be
  overridden, combine with `final`.
- **`@specialized(where ...)`** (6.3) — a `where` clause that tells the compiler to
  generate a specialized clone of a generic function for specific concrete types, useful
  in libraries where the optimizer can't see how a generic is used.

```swift
@inline(always)
func makeInts(randomized: Bool) -> [256 of Int] { ... }

@specialized(where Values == [UInt8])
func histogram<Values>(of values: Values) -> [256 of Int] where Values: Sequence<UInt8> { ... }
```
*(`[256 of Int]` is the `InlineArray` count-prefixed sugar.)*

## 11. Performance tuning — the ownership system

The conceptual core of the talk. Most Swift perf problems are **unnecessary copies**.
Prior options each had a flaw: copy (slow), put-in-an-object (reference-counting churn),
or `UnsafePointer` (no compiler-checked safety — back to C). Swift codified safe sharing
as **borrow** (both sides read-only for the duration) and **mutate** (exclusive access,
the other side fully blocked), both compile-time-verified. This year's steps forward:

- **Protocols on noncopyable/non-escapable types** — `Equatable`, `Comparable`,
  `Hashable` now work on **noncopyable** types; `Equatable`/`Comparable` also on
  **non-escapable** types. **Associated types** can be noncopyable/non-escapable too.

- **`Iterable` protocol** — `for` loops can now **borrow** elements instead of copying
  them (as `Sequence` does). Works with noncopyable elements, skips reference counting
  for objects/copy-on-write types, and can `throw` mid-loop like an `AsyncSequence`.
  Exclusivity checking forbids mutating the iterable while looping (often a `Sequence`
  perf trap anyway). The loop **prefers `Sequence` when available**, falls back to
  `Iterable`. The iterator yields elements in **batches of spans** (`nextSpan`), ending
  on an empty span — efficient for types that can return everything in one span.

  ```swift
  protocol Iterable<Element, Failure>: ~Copyable, ~Escapable {
      associatedtype Element: ~Copyable
      associatedtype IterableIterator: IterableIteratorProtocol<Element, Failure>, ~Copyable, ~Escapable
      associatedtype Failure: Error = Never
      func makeIterableIterator() -> IterableIterator
      var underestimatedCount: Int { get }
  }
  ```

- **`borrow` / `mutate` accessors** — replace `get`/`set` on computed properties to avoid
  copying. The `UniqueBox` example: a `get`/`set` property wrapping a 2 KB
  `[256 of Int]` copies the whole array in and out to change one `Int`; `borrow`/`mutate`
  mutate in place and also unlock **noncopyable `Value`**.

  ```swift
  public var value: Value {
      borrow { valuePointer.pointee }
      mutate { &valuePointer.pointee }
  }
  ```

- **New standard-library types:**
  - **`UniqueBox`** — heap box that's now in the stdlib (the example type made real).
  - **`UniqueArray`** — like `Array` but **noncopyable**; store noncopyable elements,
    skip reference counting, no fixed-size limit.
  - **`Continuation`** — compile-time single-resume check; safer than
    `CheckedContinuation`, as efficient as `UnsafeContinuation`.
  - **`Ref` / `MutableRef`** — like a `Span` for a *single* value; a storable container
    for a borrow/mutation that can be passed, returned, and used in generic types. Make a
    `Ref` by borrowing, a `MutableRef` with prefix `&`. They're **non-escapable**, so the
    access ends at scope exit. Enables previously-impossible APIs (return a `MutableRef`
    to a property) and fixes perf traps — e.g. hoist a repeated dictionary lookup out of
    a loop without the old "move the loop into an inout-param helper" trick:

    ```swift
    var countRef = MutableRef(&counts[key, default: 0])
    for set in sets where set.contains(key) {
        countRef.value += 1
    }
    ```
  - **`withTemporaryAllocation`** now uses `OutputSpan` instead of
    `UnsafeMutableBufferPointer` for safe temporary memory.

## 12. The future of Swift

All of the above was developed in open source across Apple OSes, Linux, Windows, and
beyond.

- **Swift Build** (open-sourced last year) is now the **default build backend for Swift
  Package Manager**, aligning SwiftPM builds with Xcode.
- New **workgroups**: build & packaging, networking (next-gen cross-platform APIs), and
  Windows.
- The **Android workgroup** released the first Swift SDK for Android (Swift 6.3),
  enabling Android↔iOS code sharing.
- Invitation to participate at **forums.swift.org**.
