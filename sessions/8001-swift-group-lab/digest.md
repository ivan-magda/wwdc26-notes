---
title: "Swift Group Lab — Full Digest"
session: WWDC26 · 8001
url: https://developer.apple.com/videos/play/wwdc2026/8001/
duration: 01:01:10
sources: transcript.md (whisper.cpp ggml-large-v3), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and context.
---

# WWDC26 · 8001 — Swift Group Lab

> A 61-minute live developer Q&A with a four-person Swift-team panel plus a host.
> Not a feature talk: it's the layer underneath the WWDC26 Swift sessions, where the
> people who design the language explain the "why," the current limits, and what they'd
> do differently. Recurring refrains: **profile before you optimize**, **lean on the
> compiler instead of escape hatches**, and **the project is open — show up on the
> forums and file GitHub issues with sample projects.**

## TL;DR

- **The async-function default got flipped, and the panel says it should have shipped
  that way from day one.** In Swift 6.2, non-isolated `async` functions now stay on the
  context they were *called* from instead of always hopping to the global concurrent
  thread pool. Real-world adoption showed the old default forced too many types to
  become `Sendable` and produced data-race errors when non-sendable values were passed
  back and forth. Offloading to the global pool is still available, but it's now the
  explicit opt-in. Candidly named as the one concurrency decision they'd redo.
- **`Sendable` has no runtime cost; `Equatable`/`Hashable` do.** `Sendable` is "just a
  tag" with no runtime representation. `Equatable`/`Hashable` carry the actual
  equality/hashing code, and the compiler keeps that code and the conformance around
  even if you never call it directly, because an `as?` cast to `any Equatable`/`any
  Hashable` can discover it at runtime.
- **New `~Sendable` syntax in Swift 6.4.** A cleaner spelling than the old "unavailable
  conformance to `Sendable`." Important distinction: an *unavailable* conformance means
  the type *and all subclasses* are definitely not sendable; `~Sendable` is merely the
  *absence* of a conformance, so a thread-safe subclass that adds no mutable state can
  still add `Sendable`. This is the mechanism Foundation is now using to annotate the
  "third category" of types more accurately.
- **Why `UserDefaults` isn't `Sendable` despite being documented thread-safe.** When
  Foundation audited everything for `Sendable`, a third bucket emerged: classes where
  the superclass is safe but a subclass isn't (the classic example: `NSString` is
  immutable and sendable, `NSMutableString` is a mutable subclass and is not). Rather
  than over-mark, they left these non-sendable. `~Sendable` now lets them fix this; the
  global `UserDefaults.standard` is expected to gain a `Sendable` conformance.
- **Tuples are handled differently from structs in the compiler.** The compiler
  "explodes" a tuple into separate parameters when passed to a function, whereas a
  struct is passed as a single entity. For a very large tuple this *could* be less
  performant than an equivalent struct. The panel was genuinely surprised by the
  reported case ("news to me") and asked for a GitHub issue with a sample project.
- **The hidden performance combo: `@inlinable` + `@inline(never)`.** `@inlinable`
  exposes the body across module boundaries and unlocks generic specialization and
  effects propagation, not just inlining. Pairing it with `@inline(never)` is "a very
  powerful trick" for generic code with cold paths: inline the hot path, force the slow
  byte-wise fallback out of line. Used roughly three times in the entire Swift
  networking portfolio.
- **`borrow`/`mutate` vs `get`/`set`.** `borrow` hands back a reference to data stored
  elsewhere; `mutate` is a mutable reference and requires you to be the sole accessor
  (compiler-enforced). More efficient (no copies, no extra code) but only when there's
  actually stored data to reference. Use them where you're performance-sensitive and
  sharing out data you already store; use `get`/`set` everywhere else.
- **Conditional tuple conformances are close.** Getting tuples to conditionally conform
  to `Equatable`/`Hashable`/`Comparable` is now "a fairly small evolution to parameter
  packs": you need syntax for a tuple type whose elements are a parameter pack plus a
  parameterized extension with a `where each T: …` clause. An experimental
  implementation already lives in the compiler repo, "almost all the way there."

## Panel

Host **Angelica** (Developer Relations) with **Holly** (Swift team — generics, type
inference, compiler diagnostics, concurrency; language steering group + core team),
**Corey** (Swift server networking team), **Tony** (Foundation, standard library, Swift
packages; Foundation work group), and **Doug** (Swift language team; has worked on Swift
since it started; language steering group).

## Favorite Swift 6.3 / 6.4 features (warm-up, 01:47–04:21)

- **`@diagnose` attribute + general diagnostic improvements** (Holly). Finer-grained
  control over warnings: suppress deprecation warnings in specific parts of your code,
  or opt *into* off-by-default warnings (strict memory safety, strict concurrency) in
  the areas that matter, as a more surgical path to Swift 6 mode migration. (Attribute
  name "at-diagnose" per the transcript.)
- **`async`/`defer` and cancellation shields** (Corey). The two big structured-concurrency
  wins for the server/networking community: cleaner resource cleanup, removing custom
  cancellation shields they'd hand-rolled.
- **The new `Subprocess` package** (Tony). Fully open source and cross-platform.
  Plus performance-aligned improvements to `Data`, `URL`, and other types, and new APIs
  like **`ProgressManager`**. (He's asked whether he can name more than one; the host
  jokes "we might be here all day.")
- **Embedded Swift improvements** (Doug). Running Swift "in tiny places" — bare-metal
  firmware, small devices — is now much easier; code you write elsewhere now works in
  Embedded Swift. He plugs the sample projects in the GitHub repo.

## Developer Q&A

### Concurrency: data transfer, sendability, structured concurrency

- **Transferring ownership of non-sendable data between actors (Syracusa).** Use
  **region-based isolation**: you may transfer non-sendable data from one actor to
  another as long as the original can no longer access it afterward. The compiler can
  often prove this from usage; to annotate it deliberately on parameters/returns, use
  the **`sending`** keyword. *Storing* such values for later transfer is only possible
  via unsafe opt-outs today, but there's an active forum pitch (currently called
  **`disconnected`**) for a type that preserves that property so you can store and later
  transfer it. Still being designed — file feedback on the forums. (Holly)
- **Best practices and pitfalls in structured concurrency (Ashrafi).** Lean into it
  assertively; escape hatches cause trouble. Concretely (Corey): **avoid `Task` /
  `Task.detached` unstructured tasks** unless you're genuinely sending work elsewhere,
  and never in the mainline flow. Prefer **task groups** with object lifecycles that fit
  the lexical scope; Swift's `with`-style functions give you a clean spelling for this,
  though `deinit`-based cleanup works too. Write **linear** async code inside each task
  ("a recipe: A, then B, then C, then D"), don't fan out too much; reach for task groups
  only for genuine parallelism (fork-join / scatter-gather patterns). The other big win
  is **automatic cancellation propagation** — but async *cleanup* (flushing a partly
  written file, rolling back a DB transaction) runs in a canceled context where most
  Swift code refuses to proceed, so wrap it in a **cancellation shield**, a natural
  companion to async `defer`. Holly and Tony add: **non-sendable types help here** —
  they can't escape the concurrency domain, so within asynchronous-but-not-concurrent
  control flow you can use them freely and reason linearly. Tony's reframe: people
  constantly ask "how do I make this `Sendable`?" when often the answer is to make the
  ephemeral computation type *non-sendable*, which models intent better and avoids
  unnecessary copy cost. New `~Sendable` syntax (Swift 6.4) makes that explicit. And:
  don't rush into `@unchecked` to force Swift 6 mode, or you forfeit the compiler's
  safety guarantee.
- **High-frequency sensor data → `@Observable` model on the main actor without blocking
  the UI (Charlie Polly).** "It's a combo platter" (Corey, Tony, Holly). First question:
  how high is "high frequency"? If it's still well below your UI refresh rate, you may
  have *nothing to do*. If it's genuinely high: **minimize context switches** from the
  background actor to the UI, **accumulate/coalesce** updates instead of sending one per
  data change, and consider **debouncing** (Swift **Async Algorithms** has a `debounce`
  on async sequences). Ask how much **data loss** is acceptable — a voltmeter doesn't
  need a per-nanosecond UI update; save full data elsewhere if needed and only surface
  what the user must see. SwiftUI + `@Observable` already coalesce updates for free. Make
  sure the heavy work is async in the first place; "split the problem in half."

### Conformances, annotations, type checking

- **Overhead of unused/unnecessary conformances — `Sendable`, `Equatable`, `Hashable`,
  `Identifiable`, `Comparable` — added "just out of easier compiling habits" (sjk27).**
  Yes, there's a cost for `Equatable`/`Hashable`: the equality/hashing code plus the
  conformance itself, which the compiler keeps even if unused because an `as?` cast to
  `any Equatable`/`any Hashable` can find it at runtime. `Sendable` is different — a
  pure compile-time tag, no runtime representation, no cost. Corey extends the question:
  generic operator overloads taking a protocol type on one side widen the candidate set
  and *can* slow type checking, since the checker has to sort through which overload
  fits and which is best. Tony's API-design note: only conform when the conformance is
  **meaningful** — if it's `Equatable`, it should actually be equatable — to prevent
  later mistakes. (Doug, Corey, Tony)
- **Should you tear out now-redundant annotations after a hand-annotated Swift 6
  migration (M. Traversoni)?** "I love talking about stuff like this" (Holly). It's
  **completely fine to keep redundant annotations**; some people prefer explicitness,
  especially where inference is non-obvious or could change under later edits. (Example:
  `nonisolated` on individual extension methods used to be required; you can now write it
  on the extension itself and remove the per-method ones, but they're not harmful.)
  Explicit annotations act as documentation of intent ("this type absolutely should not
  be `Sendable` — that's deliberate"). Add a comment explaining *why*, "because sometimes
  you come back and go, I clearly meant something by this, but I don't know what it was."
  Corey: treat your private helpers as if they have an API contract you hold yourself to;
  applying the annotations the compiler would have inferred is a useful discipline.
  ("Sometimes you just get a little bit ADHD about it and want to write `Equatable` and
  `Hashable` twice. I sometimes do. It helps.")

### `@MainActor` migration

- **Stopping `@MainActor` "concurrency contagion" in legacy apps without sacrificing
  Swift 6 safety.** Two approaches (Holly): if everything in a module *should* be on the
  main actor, flip on **main-actor-by-default mode** and explicitly annotate only the
  bits that offload work. Otherwise, start from the **leaf types** and work outward. Also:
  only the parts touching mutable state may actually need the main actor — mark other
  methods **`nonisolated`** so not every use of the type has to be on the main actor.
  Watch for a `static var` that's never actually mutated (often a computed property later
  turned stored): make it immutable and it can stay `nonisolated`, usable from anywhere.
  Referenced the **2024 "Migrate your app to Swift 6"** code-along for a worked example.

### Performance & profiling

- **Most essential modern Swift features/resources to adopt first for efficiency and
  performance (Yingzu).** **Profile first** (Tony). Instruments has a new **flame graph**
  view; start there so you optimize code that actually costs time and memory. He points
  to a "last year" WWDC performance talk that took an image-processing sample app and
  used Instruments to make each slowdown "magically disappear" with modern Swift
  techniques. Big performance focus this year, especially in Embedded Swift: **`Span`**
  and **`UniqueArray`** (a new type, recently accepted, also prototyped in the Swift
  Collections package). The host calls out **Nate**'s new "Swift performance using
  Instruments" session this year, which adds a **top-functions** view on top of the flame
  graph. Final reminder: it's not only about which APIs you pick — "don't forget your
  basic computer science classes and algorithms," watch your big-O.
- **Slow incremental builds — `swift emit-module` taking minutes; do type inference /
  generics / associated types affect incremental build performance, and how do you
  diagnose it (Jason Chung)?** These features can affect build performance *in the
  extreme*, but usually it's one particular expression that's slow, not the project
  overall (Doug). If it's specifically module *emission* that's slow, suspect the **other
  modules being imported**. With **explicit module builds** (rolled out in Xcode/Swift
  over the last couple of years) and the **build timeline** in Xcode, you can see where
  the compiler spends its time and prune excess dependencies — "performance tuning your
  build." Explicit modules is **on by default now**; there's an explicit-modules session
  from "about two years ago."
- **How do app developers know which features (`Mutex`, `InlineArray`, `Span`, typed
  `throws`, non-copyable types) are meant for them vs systems/embedded developers, and
  how to keep up (Parai Pan)?** "Language features aren't collectibles — you don't get a
  prize for having one of all of them" (attributed by Corey to a friend). Swift is built
  for **progressive disclosure**; you shouldn't need to learn everything to start (Tony).
  Don't jump to non-copyable types unless they help your app — **measure first**; Nate's
  talk shows the Instruments patterns that indicate you actually need one. When you *do*
  hit a performance problem, there's a tool with tighter restrictions that compiles to
  faster code (Doug): e.g. swap `Array` (copy-on-write, easy) for **`UniqueArray`** along
  the hot path when you see retain/release traffic in a trace; the compiler then guides
  you into the tighter rules. **Keep these adoptions isolated** — don't convert your
  whole project to `UniqueArray`. Corey: usually you'll *know* the moment in profiling
  when nanosecond-squeezing is genuinely needed; otherwise your time is better spent on
  bug fixes and features. They've had "great success introducing spans and a couple of
  unique arrays just along the hottest path" for most of the gain at small cost.

### Swift Package Manager & build system

- **Notable SwiftPM improvements in the latest Xcode/Swift, especially build and
  dependency-resolution performance for a large multi-package project (Mirko KG).** The
  biggest 6.4 change is one you won't notice because there's nothing to opt into
  (Holly): SwiftPM in Xcode and SwiftPM in other IDEs (VS Code via the open-source
  swift.org toolchains) previously used **different build-system implementations**; they
  are now **unified on the Swift Build package**. That brings consistency, a single point
  of maintenance for fixes/improvements, and the **Swift Build performance optimizations**
  (including **explicit modules**) to ordinary package builds. The build system is also
  better at **breaking a module's build into separate pieces** for more parallelism.
  (Previewed in 6.3, on by default in 6.4.)

### Concurrency retrospective

- **Anything the team would design differently now that Swift 6 concurrency has had real
  adoption?** Yes (Holly): the **non-isolated `async` function behavior**. Two evolution
  proposals changed it — first to always switch to the global concurrent thread pool,
  then (Swift 6.2) to **stay on the caller's context**. Real-world code, especially from
  early complete-concurrency-checking adopters, passed lots of non-sendable types back
  and forth between an actor-isolated context and these async functions, causing data-race
  errors because the actor still had access while the function ran on the global pool.
  Staying on the calling context removes that. "I held onto the belief for a very long
  time that running on the global concurrent thread pool was the right long-term model,
  but I was convinced over time" — insight they couldn't have gotten without real
  adoption; they'd just love to have shipped it that way and skipped the transition. Doug:
  it's an interesting trade-off — more available concurrency means more potential
  parallelism/performance, but the old default pushed too many types toward `Sendable`,
  "not the natural way to express all of these ideas." The new model is more approachable,
  closer to non-concurrent code, with explicit opt-in points for introducing concurrency.

### Language evolution

- **What's left to get tuples to conditionally conform to `Equatable`/`Hashable`/
  `Comparable`?** It's an evolution of **parameter packs** (Holly). You need syntax to
  write a tuple type whose element types are a parameter pack, plus a **parameterized
  extension** carrying that pack, with a `where each T: Protocol` clause (the parameter-pack
  keyword is literally `each`). Then it works. An **experimental implementation already
  exists in the compiler repository**, "almost all the way there." The intent is the
  general parameter-pack-based feature (so you can conform tuples to your *own* protocols),
  not the older bespoke per-protocol approach.

### `borrow` / `mutate`

- **`borrow`/`mutate` vs `get`/`set` — good practice to migrate altogether, and when not
  to (sjk27)?** They're part of Swift's **ownership model** (Doug). `borrow` gives a
  reference to data held elsewhere (e.g. a property stored in your struct); `mutate` is a
  mutable reference, usable only when you're the **sole** accessor (compiler-enforced).
  `get` produces a *new* value (possibly computed on the fly) and `set` can run arbitrary
  code. So `borrow`/`mutate` are more efficient — no copies, no extra code — but only when
  there's stored data to reference, and `mutate` imposes exclusivity. Use them **where
  you're performance-sensitive and sharing out data you already store**; use `get`/`set`
  for everything else.

## Lesser-known features the panel wanted to surface (39:18–53:01)

Two rounds: "the one Swift feature most developers don't know exists but should"
(Florentine F) and "favorite quality-of-life / quality-of-code feature, not the obvious
ones" — answered together by the panel.

- **`@inlinable` + `@inline(never)`** (Corey). `@inlinable` exposes a function body
  across module boundaries and unlocks more than inlining: generic specialization,
  effects propagation. Combined with `@inline(never)`, it lets you inline a generic hot
  path while forcing a laborious cold fallback (e.g. byte-wise copy) out of line — used
  ~3 times across the whole Swift networking portfolio, but invaluable when the compiler
  won't reliably inline what you want. (The panel also notes `@_inline` was recently
  stabilized as **`@inline(always)`**.)
- **Plain `as` type annotations to steer overload resolution** (Holly). When a large
  expression gives an ambiguity error across many overloads, inserting `as SomeType` on
  the part whose type you know can resolve the overload, infer a type parameter, or at
  least produce a more precise error. "I didn't realize how useful plain `as` was when I
  was first learning Swift until a while later."
- **Swift as an open-source project** (Tony). The open-source side isn't only for
  non-Darwin platforms — the language, standard library, Foundation, and networking APIs
  are all discussed on the forums, and the panel explicitly wants **more feedback from
  iOS developers** who may never touch server Swift. "It's the same language and the same
  libraries everywhere." You don't need to submit a PR to participate: ask about a
  confusing compiler error (great input for diagnostics), comment on an evolution
  proposal — even saying "this looks fine to me" is valuable — or work on
  swift-syntax. Reference to **Seema** discussing diagnostics text updates "last year."
- **Integer-overflow APIs** (Corey). Carry an overflow flag through a whole straight-line
  integer computation and check it once at the end. "When you need them, you really need
  them," but easy to miss — "I almost only ever remember them after I've overflowed the
  integer."
- **Key paths** (Doug). People build "big piles of closures" to abstract over properties
  when a key path lets you refer to a property without an instance. "It's been there for
  years, but somehow it gets missed." Corey notes the server ecosystem — especially
  database drivers — "lives and breathes" key paths for elegant API surfaces.
- **Swiftly** (Angelica). An easy installer for Swift toolchains, usable from Xcode (not
  just VS Code / CLI) to try newer toolchains and experimental features.
- **The `Iterable` protocols** (forward-looking, Corey). A new protocol family mentioned
  in "What's New in Swift" tying into non-copyable / non-escapable work, with the goal of
  making `for…in` over a `Span` feel like natural Swift while preserving lifetime and
  memory safety — expected to grow "a whole bunch of new container types."

## Favorite quality-of-life features round (53:01–59:59)

- **Bidirectional type inference + the generics diagnostics model** (Holly). How little
  type information you must write, and that Swift diagnoses a mistake in the
  *implementation* of generic code rather than at the use site (unlike languages that
  monomorphize and only surface errors when you instantiate). Appreciated most when
  switching back to the C++ that implements the compiler.
- **Tools for avoiding mutable shared state / copy-on-write** (Corey). Was "taken aback"
  to learn copy-on-write isn't a compiler trick but a feature you can use to build your
  own COW types; combined with the ownership model it holds up even in
  performance-sensitive code. Values it for "no spooky action at a distance."
- **The generic system / protocol extensions** (Doug). Extending a protocol to add a
  method is "wonderfully simplifying" — you're writing a generic algorithm, but the
  syntax "melts away" so it's as easy as non-generic code. (Protocol extensions weren't in
  Swift 1.0; added later.)
- **Replacing C with Swift** (Tony) — "very satisfying" to get safety and memory-safety
  guarantees in a fluent language "without all the very sharp edges of writing something
  in C."

## Unconventional facts & takeaways (the live-Q&A gold)

- **Candid "we'd redo this" admission:** the non-isolated `async` default (caller-context
  vs global pool) is the concurrency decision the panel would change to ship the 6.2
  behavior from the start. A rare on-record reversal of a long-held design belief.
- **Corrected misconception:** `Sendable` is free (a compile-time tag, no runtime
  representation), unlike `Equatable`/`Hashable`, whose code is retained for `as?`
  discovery even when unused.
- **Subtle but load-bearing distinction:** an *unavailable* `Sendable` conformance binds
  a whole class hierarchy as non-sendable; `~Sendable` is just the *absence* of a
  conformance, so safe subclasses can still opt in. This is why Foundation can now fix
  `UserDefaults` (the global `UserDefaults.standard` is expected to become `Sendable`).
- **Compiler internals surprise, live:** tuples are "exploded" into separate parameters
  while structs pass whole; a large tuple can be slower than the equivalent struct. The
  panel hadn't seen it ("news to me") and asked for a GitHub issue with a sample.
- **A genuinely rare trick:** `@inlinable` + `@inline(never)` has been used "three times"
  across the entire Swift networking portfolio — a real number you won't find in docs.
- **Cultural color:** "Language features aren't collectibles — you don't get a prize for
  having one of all of them." The "ADHD about annotations, I write `Equatable` and
  `Hashable` twice" aside. Holly recognized at Platform State of the Union "in the exact
  same shirt." Corey's "you couldn't go five minutes writing a web app without key paths."
- **Process tell:** the panel routes bugs to **GitHub issues with sample projects** and
  language-design questions to **forums.swift.org** — and stresses that *participating*
  (asking about a confusing diagnostic, commenting on a proposal) counts as contributing,
  no PR required.
- **`UniqueArray`** is described as recently accepted into Swift and also prototyped in
  the Swift Collections package — a fresh type to watch alongside `Span`.
