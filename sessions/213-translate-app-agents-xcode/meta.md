# Session 213 — Translate your app using agents in Xcode

- **URL:** https://developer.apple.com/videos/play/wwdc2026/213/
- **Duration:** 15m
- **Speaker:** Avery (engineer, Apple Localization team)

## Description

Beginning in Xcode 27, you can translate your app's strings directly in Xcode
using coding agents. Xcode feeds translation agents the context it has been
accumulating in String Catalogs (where a string is used, how it is used, and how
it was translated in other languages) so the agent picks the right meaning for
ambiguous words. The session walks through adding translations, reviewing and
iterating on them (truncation checks, native-speaker feedback via TestFlight),
and best practices for guiding the agents with glossaries and custom terminology.

## Key topics

- Xcode 27 lets coding agents translate String Catalogs, fed by the context Xcode tracks (where/how strings are used, prior translations in other languages)
- The translation flow: agent tells Xcode to prepare the project → Xcode adds the language, builds all targets to discover strings, creates/populates String Catalogs → agent batches strings to subagents
- Subagents receive per-string context (code location, similar-terminology strings, other-language translations) and correctly handle plural variations per language (`%lld items` → `un élément` / `deux éléments`)
- Consistency across conversations — a brand-new feature reused the custom `attraits` translation for "landmarks" without re-reading the earlier conversation
- Review & iterate: ask the agent to render UI in the target language and look for truncation, vertical clipping (Thai), or RTL alignment issues (Arabic)
- Native-speaker feedback via TestFlight — developers can't verify languages they don't speak
- Best practices: make strings localizable (SwiftUI is by default; otherwise `String(localized:)` / `LocalizedStringResource`), guide agents with a `TRANSLATION.md` (or `AGENTS.md` section) glossary/tone/do-not-translate list
- Model choice: prefer a large-context model good at long-running requests; watch for low-resource languages underrepresented in training data
- `leveraged-mt` state-qualifier in exported XLIFF marks agent-provided (machine) translations

## Related sessions to fetch (referenced in this talk)

- [ ] Build multilingual-ready apps
- [ ] Get it right (to left)
- [ ] Get Started with TestFlight (tech talk)
- [ ] Code-along: Explore localization with Xcode
- [ ] Xcode, agents, and you

## Chapter summary (Summary tab)

- **0:00 Introduction** — Why context matters for software localization (the `book` = read vs. reserve ambiguity). String Catalogs have tracked *where* (2 years ago) and *how* (last year) strings are used; Xcode 27 brings that context to translation agents.
- **1:49 Add translations** — Ask an agent to translate the app into Canadian French via the New Conversation button. Xcode prepares the project (adds language, builds all targets to discover strings, creates/populates String Catalogs), then the agent batches strings to subagents with per-string context and correct plural variations. Custom terminology (`attraits` for "landmarks", app name `Attraits phares`) is applied and reused in a later new feature.
- **8:11 Review and iterate** — Ask the agent to render the new feature's UI in the target language and spot truncation; investigate as a bug, redesign, or shorter-translation problem. Same technique catches vertical clipping (Thai) and RTL alignment (Arabic). Use TestFlight to get native-speaker feedback on languages you don't speak.
- **11:02 Best practices** — Ensure user-facing strings are localizable (SwiftUI default; else `String(localized:)`). Agents use Apple's built-in language style guides but respect your guidance — put a glossary / do-not-translate list / tone in `TRANSLATION.md` (or an `AGENTS.md` section). Pick a large-context model; mind low-resource languages. `leveraged-mt` qualifier flags agent-translated strings in exports.

## Code

See `code.md` — 4 distinct snippets from the Code tab (SwiftUI localization, `String(localized:)` / `LocalizedStringResource`, and the `leveraged-mt` XLIFF trans-unit).
