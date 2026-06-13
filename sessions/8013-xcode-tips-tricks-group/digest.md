---
title: "Xcode Tips and Tricks Group Lab — Full Digest"
session: WWDC26 · 8013
url: https://developer.apple.com/videos/play/wwdc2026/8013/
duration: 01:01:35
sources: transcript.md (whisper.cpp ggml-large-v3, SD video audio), meta.md
compiled: 2026-06-13
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and host routing. Whisper renders some names inconsistently (Casper/Katsper) and turns some read-aloud developer usernames into garbled "names"; those are flagged, not attributed to panelists.
---

# WWDC26 · 8013 — Xcode Tips and Tricks Group Lab

> A roughly 62-minute live developer Q&A with a five-person panel from the Xcode team
> (plus a host). Not a feature talk: it's the unscripted layer beneath this year's
> Xcode sessions, where the people who **build Xcode with Xcode** answer upvoted
> questions and trade the keyboard shortcuts, hidden menus, and project-file habits
> they actually use daily. Two refrains run through the whole hour: **use Git / source
> control**, and **file feedback in Feedback Assistant with a concrete use case** —
> because much of what's asked for is roadmap input, not a setting that already exists.

## TL;DR

- **Blue folders are the panel's single most-repeated tip.** Converting `.xcodeproj`
  groups (gray) to folders (blue) stops Xcode from recording the file list, which can
  cut project-file size by roughly a third and "drastically" reduce merge conflicts.
  John says he can't recall a single conflict on the Xcode project since adopting them,
  across a large team and many sub-projects. Adoption workflow: right-click a group >
  Convert to Folder; hold Option to pre-flight and see what must be resolved first;
  resolve all errors and commit *before* migrating so you don't collide with teammates.
- **Inline playgrounds in any Swift file.** A `#playground` macro (added "last year")
  lets you run code snippets in any Swift file with inline expression results;
  `File > New > Playground` now gives a single Swift file with the macro in it. Works
  for exploring Foundation Models and for SwiftUI preview code in a standalone file.
- **"Delete Derived Data" is both a real fix and a superstition.** It's a new menu item
  in Xcode 27 (long-requested). It genuinely works around build-system race conditions
  and hidden inter-target dependencies, but the panel's position is that you should
  *never need to*: it's masking a bug (theirs or yours). Xcode 27 fixed many such
  cases; if you still reach for it, file a reproducible feedback. Bonus: **Swift Build,
  Xcode's build system, is open source**, so you can read why a build failed.
- **Cut a type, paste into the file navigator, get a new file.** Cut the text you want
  to move, put keyboard focus in the file navigator, and paste: Xcode creates a new file
  from the pasteboard and guesses the name from the content (paste a `struct Person` and
  you get `Person.swift`). Jake's favorite for splitting a multi-type scratch file.
- **Profile on a real device and a release build.** Two of the most common profiling
  mistakes called out by the Instruments panelist: profiling on the simulator (it
  doesn't represent the real device) and profiling a debug build (you want the compiler
  optimizations applied, or you'll chase problems that don't exist in the real world).
- **Pin individual canvas tabs (new this year).** Previously you pinned a whole file's
  canvas; now you can pin one tab. Pin your ContentView preview, then navigate data and
  other files while the preview keeps live-updating.

## Panel

Host **Angelica**, with a five-person Xcode-team panel introducing themselves at the top
of the hour:

- **Chris Miles** — live coding experiences: previews, playgrounds, and integrating
  those into agentic workflows.
- **Jake** — designer on the Xcode team; recently themes and toolbar customization.
- **John** — manages several front-end Xcode teams: window chrome, source editor,
  search, code completion, and design tools.
- **Casper** — Instruments team; builds profiling tools. (Whisper also renders this as
  "Katsper.")
- **Chris** (the second Chris) — the Organizer window for crash reporting and App Store
  uploads, plus some of the signing UI in Xcode.

The host closes by thanking "Chris, Jake, John, Casper, other Chris." Because the
transcript has no diarization, individual attributions below follow self-introductions
and the host's routing of each question; where a tip can't be safely attributed it's
left to "a panelist."

## Warm-up — favorite lesser-known Xcode tips (00:01:19–00:05:25)

The host opens with the developer-submitted (and her own) question: what useful tips do
most people miss? She notes the in-joke that **Xcode is built with Xcode**, so the panel
uses it daily. Going around the table:

- **Inline playgrounds** (Chris Miles): the `#playground` macro added last year, runnable
  in any Swift file with inline results; `File > New > Playground` gives the modern
  single-file form. The panel piles on: you can test **Foundation Models** in those
  files, and put UI code plus a preview in a standalone Swift file.
- **Per-workspace themes** (Jake, "kind of cheating because it's new this year"): pick a
  different theme per workspace so two projects look distinct (one red, one blue) — easier
  to tell them apart, or just to match your mood.
- **Cut-and-paste a new file** (Jake): cut the types you want to split out, focus the file
  navigator, paste; Xcode makes a new file and guesses its name from the content. His
  go-to when a scratch file has grown several types and needs splitting up.
- **Find Call Hierarchy** (the second Chris): invaluable while refactoring Objective-C to
  Swift — quickly see where a function is called from and follow dependencies multiple
  levels deep.
- **Cmd-Shift-J and a recolored debug Xcode** (John): Cmd-Shift-J reveals the current
  file's location in the project navigator. And when developing Xcode itself, the team
  changes the *appearance* of the debug build (e.g. to dark mode) so they can tell the
  Xcode-under-test apart from the Xcode they're working in.
- **Open Quickly + Cmd-Shift-J combo** (a panelist): use Open Quickly to search for a
  *function* (not a file), then Cmd-Shift-J to locate it exactly in the project.
  "My favorite combo."

## Developer Q&A

The host reads upvoted developer questions and routes them. Asker handles are quoted as
read; several are likely whisper-garbled and are reproduced as heard.

### Underrated / recent features that deserve more attention

- **"Which recent feature deserves more attention?" (asker read as "Phanteks").** John's
  answer: the **blue folders vs. gray groups** distinction (added "maybe Xcode 16, might
  have been 15"). Folders don't record the file list, so converting a project to folders
  generally yields *zero* diffs in the project file when you add files, drastically
  cutting team conflicts. The Instruments team adopted it too, and used the migration as a
  chance to clean up messy grouping.
- **Scheme user-defaults toggles** (a panelist, "an oldie but a goodie"): in Edit Scheme
  you can set user defaults and check/uncheck them; the team constantly trials features
  behind a user default this way.
- **Console filtering by component/category** (Chris Miles): filter the console down to
  components or categories for far cleaner debugging — and you can have an agent organize
  the logging for you.
- **Filter-as-you-type menus** (a panelist, "less discoverable"): many Xcode menus,
  especially the jump-bar menus at the top of the editor, look like normal menus, but if
  you start typing they filter with code-completion / Open Quickly style matching — a fast
  way to a function definition.
- **Preview variants and preview arguments** (Chris Miles): show variants (light/dark,
  Dynamic Type sizes), and **new this year, pass arguments to previews** — feed
  `enum.allValues` and a single preview renders a grid across all cases.
- **Memory Graph in Instruments** (Casper): Memory Graph Debugger finds leaks but not
  always footprint; there's a (somewhat hidden) Share button to open the memgraph in
  Instruments and see the allocation timeline. A memory-analysis session this year
  demonstrates this export.
- **Conditional breakpoints** (a panelist): secondary-click a breakpoint to open a modal
  for conditions, ignore counts ("number of retries"), and repetitions. Leave a breakpoint
  in place and have it trigger only when you actually need it.

### "Most underrated feature even experienced devs miss" (asker "Phanteks")

- **Blue folders again** — "I can't stress enough how much this will reduce your project
  conflicts." The catch is the one-time conversion step; the payoff is worth it.
- **Pin individual canvas tabs** (Chris Miles): new this year you pin one tab rather than
  the whole file. Pin your ContentView preview, then roam data and other files while it
  keeps updating.
- **Breakpoints that signal a sound and don't pause** (a panelist): "always a good one."
- **Xcode Cloud** (panel): easy to set up, "just works on its own," with lots of updates
  this year — improved onboarding, build performance, **set up right inside Xcode without
  going to App Store Connect**, and **webhook configuration now built into Xcode**.
- **Powerful search navigator** (a panelist + John): keyword and partial-word search by
  default, but also **regular expressions** and symbol search; multi-word "proximity"
  search; **combine the search field with the bottom filter field for a Boolean-style
  search**; and a little-known trick — highlight results in the Find Navigator and press
  **Delete** to remove them from the result set, narrowing scope as you work through a
  rename. Also: Cmd-click a disclosure triangle to collapse all siblings and see every
  matching file name at once.
- **Type hierarchy queries** (John): alongside Find Call Hierarchy, Xcode added queries to
  show a **type hierarchy** both up to the root and down to the leaves — handy to see how
  many types conform to a protocol or how many subtypes exist.
- **The profiling tools generally** (Casper): from the Organizer (how your app behaves
  with real users) to Instruments; "profiling is a pretty addictive process." The
  **flame graph combined with top functions** makes hotspots much easier to find. There
  are dedicated Instruments templates for **SwiftUI, concurrency, and agentic Foundation
  Models**, and you can add other instruments to a template and save it for reuse.

### Running Xcode 26 and 27 on the same machine

Two related questions ("Formidable Studio"; "mblackmon"):

- **Yes, run both.** "Totally supported" — the team runs many Xcode and macOS versions
  daily. Main caveat: stick to features in the already-shipped **public SDK** so your app
  still builds with the older Xcode. This year only **one** project feature requires
  Xcode 27, and you'd have to go out of your way to use it (a checkbox in the inspector
  opting into stricter project validation/loading — "you're not going to check this by
  accident").
- **Version/OS notes:** you can use both 26 and 27 on **macOS Tahoe**; Xcode 26 does *not*
  run on **macOS GoldenGate** (transcript's name for the next macOS). Watch that
  `xcode-select` points at the right place when building/shipping to the App Store.
- **Project-file incompatibility:** "some years, but not really this year."
- **Use Xcode Cloud** to test what your build status would be on Xcode 27 without building
  it yourself — **25 free hours**, plenty for this.

### Minimizing `.xcodeproj` merge conflicts (without Tuist / XcodeGen)

Asker "Scott G" notes painful `.xcodeproj` conflicts that some teams solve with third-party
project generators. John's answer (the most detailed of the lab):

- **Adopt blue folders** — roughly a one-third project-size reduction.
- **Align targets with folder contents.** Blue folders are *members of targets* (groups
  weren't). Any deviation from "this folder's files all belong to this target" gets
  recorded in the project file. A 150-file framework all in one target records nothing; a
  couple of stray test files for a test target *will* be listed. Get them in alignment.
- **First migration is a big diff.** Two-thirds of the project disappears, which is *why*
  conflicts stop. On a big team, the hard part is landing that initial step while
  colleagues keep editing the project file.
- **Migration workflow:** right-click a group > Convert to Folder; **hold Option to
  pre-flight** — the menu tells you what must be resolved before migrating. Resolve all
  errors *without* migrating anything, commit that, then finish the migration later at a
  quiet moment.
- **"Minimize cross-project references" checkbox** in the project inspector — set by
  default for projects created in recent years, but not for older ones. Checking it is
  "basically free" and can delete thousands of lines from project files if you use many
  sub-projects.
- **XCConfig files** for build settings: move some or all build settings into text
  `.xcconfig` files; the editor still lets you inherit and override. To find a setting's
  name, open the **Help inspector** and select a build-settings row, or copy the build-
  setting row to paste the key into the config file. XCConfig source editing was "tuned up"
  this year (syntax coloring). Config files give a clean audit trail and support comments.

### Markdown and source control (asker "Newby")

- **Markdown:** Xcode has a good renderer now; view files rendered or, via **Open As >
  Source Code**, as raw Markdown — and you can edit in either form. The same Markdown
  editing experience applies when an **agent** creates a plan or other Markdown in the
  editor, and you can view rendered Markdown side-by-side via the canvas preview button.
- **Source control:** the panel will take the **multi-select stash deletion** request back
  to the team. They **completely redid the source-control navigator** this year ("you
  can't tell because it's exactly the same, it just works better"), improved package/
  project cloning and authentication consistency, and got **much better performance with
  millions of tags**. New: **red/green diff colors** by default, and you can **customize
  those diff colors** in your theme if you prefer something else.

### Swift Package Manager macro re-approval (asker "Tammy Santana")

When an SPM package (example given: an MLX Swift LM package) updates its macros, Xcode
requires **manual re-approval every time**, breaking CI and blocking compilation. Can macro
trust be pinned to a **package identity** instead of re-approving every update? The panel
has no package-manager expert on the table; they're surprised trust is invalidated on every
update and will route it to colleagues. **File feedback** naming the exact package and
behavior so they can track it down.

### Open Terminal at the project root (asker "C. Gontijo")

Not available directly today. Workarounds: open Terminal and **drag a file reference from
the navigator into Terminal** to `cd` there. Related and useful: the file inspector now
lists the **working-copy relative path**, which you can double-click to grab for Git
operations. The panel encourages a feedback request — Show in Finder exists on right-click,
so a Terminal equivalent is plausible.

### Daily keyboard shortcuts (asker "Chrisya")

A rapid round. Names of menu commands are quoted as spoken:

- **Cmd-Shift-J** — reveal the current file in the project navigator.
- **Cmd-Shift-Y** — show/hide the debug area.
- **Cmd-Shift-O** — Open Quickly (navigate to any symbol).
- **Cmd-R / Cmd-U / Cmd-I** — run / test / profile ("the basics… in the Product menu").
- **Cmd-Ctrl-R** — **launch without building**; tear down and relaunch to recreate on-disk
  state, or relaunch after an agent reports a successful build without rebuilding.
- **Cmd-Option-P** — refresh/resume previews when they pause on larger changes.
- **Cmd-Option-Up / Down** — switch between implementation and header in Objective-C.
- **Control-6** — opens the rightmost element of the path control above the editor (all the
  types/functions in the file); type to filter — "a really scoped Open Quickly on the file
  I'm already in."
- **Control-backslash** — jump to the next diff in the current file.
- **Cmd-slash** — toggle a comment (called out as great for commenting out a SwiftUI
  modifier). (Spoken once as "command backslash," corrected to comment-toggling; treat the
  comment shortcut as Cmd-slash.)
- **Control-Option-Command-G** — re-run the last test. The panelist's mnemonic: "all three
  modifiers in the bottom row, plus G — don't try to remember which ones, just all three."
- **Tip for learning a shortcut:** press a menu command repeatedly and watch which menu item
  highlights to discover its key equivalent.
- **Reminder:** Xcode Settings has a searchable table of every action where you can view and
  create your own key bindings.

### Common beginner mistakes (asker "Claire KC")

- **Don't try to learn every feature at once.** Xcode is "a workhorse of an app"; start
  with the source editor, inspectors, and navigators.
- **Use automatic signing** (the second Chris). Old online advice pushed manual signing;
  for most cases now, automatic signing "is really there for you," and manual workflows are
  where newbies get stuck.
- **The simplified Xcode 27 start** (Jake): the toolbar now surfaces previously-buried
  controls at the top level (right-click to customize/swap for your workflow), and a new
  project gives you a blank app with no questions to answer — code and see results on the
  right immediately, same for a playground. No need to dive into the most complicated
  template and pile on targets.
- **Use source control / Git — even solo.** Repeated emphatically: "the best undo system
  ever," on by default for new projects. Keep features on separate branches. "If you don't
  know how to use it, your agent will." Reach for **agents as a safety net** while learning.
- **Profiling mistakes** (Casper): don't profile on the **simulator** (it doesn't represent
  the real device), and don't profile a **debug build** (you want the optimized release
  build, or you'll chase non-problems).
- **Adopt the modern frameworks** (a panelist): SwiftUI and Swift Concurrency.
- **Learn from code-along sessions** — seeing a concept *in the context of Xcode* teaches
  you what the icons and tools are for. One panelist's personal method: take a small open-
  source project (e.g. a 48-hour game jam game) and rewrite it one-to-one until it works,
  letting Xcode "power you up" once your code runs.
- **Organize early** (a panelist): start with folders from the start — "folders are cheap
  and easy and you'll thank yourself later." Agents are good at reorganizing a 600-line
  file. The closing reframe: "there are no beginner mistakes, just things you have to try a
  bit more."

### DocC documentation workflows (asker "Protonster")

The panel is candid that documentation isn't their strongest habit. What they offer:

- The **documentation preview assistant** shows anything written in your source files; you
  can host a **DocC archive as a static website for PR review** and include it in diffs.
- **Rein agents back** on docs — they tend to over-document and reveal implementation
  details; review generated doc comments, especially when finalizing public API.
- **Write articles** — the Instruments team writes articles before a feature ships so
  internal developers can learn it early. You can include **light- and dark-mode images
  that auto-swap**.
- **DocC is open source** (check the repo); there are good DocC sessions from a few years
  ago, and the **generative-AI search on developer.apple.com** surfaces DocC resources.

### Build time and build performance (asker "Theo K")

- **Cmd-Ctrl-R (run without building)** is the fastest way to re-run when your code changes
  don't need rebuilding for this debug pass.
- **Reduce invalidation:** how much of last build gets invalidated depends on how connected
  your source is. **Split code into modules** (packages or frameworks) so whole sections
  become unreachable from others and don't rebuild after unrelated changes.
- **Build-log assistant editor** shows the **build timeline** to see why more is building,
  or taking longer, than expected.
- **Type-check timing warnings:** opt into build settings (e.g. via a config file) that warn
  when Swift expressions take longer than a threshold to type-check; ratchet the threshold
  down and split slow expressions across lines.
- **Script phases** that run unconditionally are a gotcha — they can be slow or invalidate
  prior results. **Declare their inputs in the project editor** so they only run when
  needed. ("Build with Timing Summary" was mentioned as a related assistant.)
- **Explicit modules** and Swift's newer **`private` / `internal import`** can rein in
  cross-module relationships and improve build-product reuse (panel notes they aren't the
  deepest experts here — go to the forums for specific cases).

### Faster Xcode previews (asker "Pirca")

Same principles as build performance. Previews share build artifacts with run, so do an
**initial build and run first**; a subsequent preview is then relatively quick because it
reuses the built products. The same holds for inline playground macros. Beyond that:
modularize your project.

### Integrating swift-format into Xcode (asker "Newby")

The developer added swift-format as a build phase, but it throws "loading from disk" alerts
while typing. Suggestions: **choose when it runs** — on build, on save, or (recommended) as
a **pre-commit step in your Git workflow**, which is least disruptive and gets correct
formatting into the repo at the right time (when the PR is ready for review, not on code you
may refactor away). Also a good candidate for a feedback report given the specific workflow.

### Underutilized LLDB commands and breakpoint actions (asker "Protonster")

Isolating state changes *without* stopping execution:

- **Play sound** breakpoint action — confirm a code path is hit without pausing.
- **Log message** straight from a breakpoint action — saves adding a `print`; the breakpoint
  logs for you and nothing gets committed into your source.
- **Print a backtrace** or call **any LLDB command** from a breakpoint action.
- **Conditional breakpoints tied to a modifier key** (John's trick): for hard-to-isolate
  layout / drag-and-drop bugs with no clean condition, make a breakpoint conditional on the
  **Shift key being held**; drive the program to the right state, then press Shift to
  trigger and stop at exactly the right moment. You can also **skip a number of breakpoint
  hits** when you know the Nth one is the one you want.
- **`po` vs the variables view:** a panelist admits to over-reaching for `po` when the
  **variables view** already shows everything in scope. Use **`expression` + Swift's
  `print`** when you need exact escaping in the output. For hex-heavy work (Instruments),
  **`p/x`** prints as hex.
- **The pause button** — sometimes intentionally pause a running program just to see where
  it is when you haven't set a breakpoint or added logging.

### Seeing unused vars / properties / functions (asker "thbi")

Comes down to what the compiler can reliably determine. **Unused local variables** produce
warnings because the compiler can reason about scope. **Public properties / functions can't**
be reasoned about — anyone could call them. **Find Call Hierarchy** helps check a specific
function but won't enumerate everything in a project. There are third-party tools for this;
if you want Xcode to surface it, **file feedback with the use case**.

### Delete derived data — engineering or superstition? (asker "BrewInstallPoppy")

"It's both." It genuinely works around real problems, but the panel's stance is that you
*shouldn't have to*:

- **Why it works:** a target depends on another target's output in a **non-obvious** way;
  build ordering usually produces the dependency first, but when it doesn't you get a
  baffling "can't see this definition" error. Nuking derived data and rebuilding fixes it.
  "It's like debugging a race condition in your code, except in the Xcode build system, and
  you don't get to see the source code — well, now you do" (**Swift Build is open source**).
- **Other reasons:** reclaiming disk space from piled-up previous builds; working around an
  actual build-system bug.
- **The fix is a feedback, not the ritual:** "we actually don't think you should ever have
  to delete the derived data." Send a reproducible bug report with the edit sequence that
  triggers it. **Xcode 27 is meaningfully better** — branch switching that used to fail
  builds now works "way, way, way better," and many of these cases were specifically fixed.
- **What else to reach for:** **Clean**; you *used* to be able to **turn off parallelized
  builds** to more predictably surface a missing dependency (a panelist is unsure it's still
  supported); and if a project frequently *analyzes* clean, look for a **missing dependency**
  rather than deleting derived data repeatedly. Worktrees each create their own build folder,
  so derived data can grow and occasionally want a clean.

### Agent context compression in "Cloud Code" (asker "Tammy Santana")

A developer building a 50-file SwiftUI app with "Cloud Code" (whisper's rendering of an
agentic coding tool, read as such) reports that when **context compresses mid-session the
agent silently loses project understanding**, and they wrote a 230-line "Cloud MD"
(`CLAUDE.md`-style) file as a workaround; they'd like a **compression indicator** and a
**pause-before-editing** mode. Treated as a feature request ("anything's possible with
feedback"). The panel's practical advice: use **plan mode** and **write the plan/artifacts
to disk** so context lives on disk rather than only in the chat window — durable across
model switches, sessions, and days. Caution: a markdown context file is **loaded into
context by default**, so a 230-line file grows context every time; file feedback for the
indicator.

### Signing and provisioning profiles (asker "klkj99")

Mismatches and code-signing issues, plus manually switching provisioning profiles across
many targets to build and test in Instruments (the second Chris + Casper):

- **The Instruments symptom is usually a signing setup issue.** Using a **distribution
  certificate for the release configuration** breaks tools like **Allocations** that need a
  **debuggable** build with the right entitlement. And disabling `CODE_SIGN_INJECT_BASE_
  ENTITLEMENTS` to work around it is also wrong.
- **Use automatic signing.** People reach for manual signing to support CI, but with **Xcode
  Cloud the signing is handled for you**, so you can keep automatic signing locally.
- **Don't distribution-sign locally.** It feels like the release configuration should use
  distribution signing, but it shouldn't — use **regular signing locally** and only
  distribution-sign when you **upload to the App Store**. Doing it locally is unnecessary and
  can "mess things up." This is a common cause on large, long-lived projects where a
  workaround got baked in.

### Code folding / structural navigation to suppress noise (asker "Protonster", closing)

How to fold code and use structural navigation to keep only high-value logic on screen.
Treated mostly as a feature request — "imagine a world where you see basically what the jump
bar gives you, but in code form." **File feedback with your specific workflow.** Today you
can **organize source so noise is contained** (split a type across extensions or files —
"put the junk in the junk drawer") and check the **Editor menu for code-folding actions**.
The honest note: "what's visual noise?" depends on the use case, so feedback is what lets
them chase it.

## Unconventional facts & takeaways (the live-Q&A gold)

- **Xcode is built with Xcode**, and the team **recolors the debug Xcode** (e.g. dark mode)
  to tell the Xcode-under-test apart from the one they're working in. They also juggle many
  Xcode and macOS versions daily.
- **Cut text → focus the file navigator → paste = a new, auto-named file.** A genuinely
  hidden productivity move; the file name is guessed from the pasted content.
- **Blue folders are members of targets** (groups weren't), and *misalignment* between a
  folder's files and its target is exactly what gets recorded back into the project file —
  the subtle reason a sloppy conversion still produces diffs.
- **Hold Option on Convert to Folder to pre-flight** the migration and see blockers before
  committing to it; resolve and commit errors first, migrate later.
- **Press a menu command repeatedly and watch which item highlights** to reverse-engineer
  its keyboard shortcut. Several panelists admit they "remember shortcuts in their hands,
  not their heads" (the Control-Option-Cmd-G "all three modifiers" mnemonic).
- **Delete the result of a Find Navigator search** (highlight + Delete) to whittle a result
  set down as you work a rename; **Cmd-click a disclosure triangle** to collapse all
  siblings and see every file name at once.
- **Customize diff colors** — Xcode 27 ships red/green diffs by default, but you can theme
  them (the panelist jokingly offers "purple and orange").
- **Swift Build is open source**, and so is **DocC** — you can read the build system to
  understand a "haunted" build, and read DocC to understand its behavior.
- **Profiling candor:** never the simulator, never a debug build; **flame graph + top
  functions** is the fast path; there are templates for **SwiftUI, concurrency, and agentic
  Foundation Models**.
- **Memory Graph can be exported into Instruments** via a "somewhat hidden" Share button to
  see the allocation timeline — beyond what the Memory Graph Debugger shows.
- **Agents woven through the workflow:** organizing logging, splitting an oversized file,
  drafting and reining-in docs, and the **plan-to-disk** pattern so context survives
  compression. The signing answer and the "Cloud MD" question show real agentic tools (read
  by whisper as "Cloud Code" / "Cloud MD") in developers' daily Xcode use.
- **Xcode 27's start is deliberately simpler:** blank projects with no template modal,
  controls promoted into a customizable toolbar — an explicit response to beginner-mistake
  feedback.
- **macOS naming:** the transcript names the next macOS "**GoldenGate**" — Xcode 26 doesn't
  run on it; Xcode 26 + 27 coexist on **Tahoe**.
- **The lab's two mantras:** "**use Git, even solo**" (the best undo system) and "**file
  feedback with a concrete use case**" — repeated so often a panelist apologizes for
  "sounding like a broken record."
