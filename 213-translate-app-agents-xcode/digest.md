---
title: "Translate your app using agents in Xcode — Full Digest"
session: WWDC26 · 213
url: https://developer.apple.com/videos/play/wwdc2026/213/
duration: 15m
speakers: Avery (Apple Localization team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 213 — Translate your app using agents in Xcode

## TL;DR

Xcode 27 lets a coding agent translate your app's strings directly inside Xcode,
and the headline isn't "an LLM translates strings" — general-purpose LLMs already
do that. The headline is **context**. String Catalogs have been quietly
accumulating it for years (two years ago: *where* a string is used; last year:
*how* it's used, for auto-generated comments). Xcode 27 feeds all of that into
translation agents so they disambiguate things a context-blind model gets wrong
(the classic example: "book" = a thing to read vs. reserving a hotel room).

Four themes:

1. **One-button translation flow** — ask an agent to translate into a language;
   Xcode prepares the project (adds the language, builds every target to discover
   strings, creates/populates String Catalogs), then the agent batches strings
   out to **subagents**, each given per-string context including how the string
   was translated in *other* languages.
2. **Consistency** — custom terminology choices propagate. After overriding
   "landmarks" to the Canadian term `attraits`, a later, separate conversation
   building a brand-new feature reused `attraits` without re-reading the earlier
   chat.
3. **Review & iterate in a tight loop** — ask the agent to render the UI in the
   target language to catch truncation (French runs long), vertical clipping
   (Thai), or RTL alignment (Arabic); use **TestFlight** for native-speaker
   feedback on languages you can't verify yourself.
4. **Best practices** — make strings localizable first; guide agents with a
   `TRANSLATION.md` glossary / tone / do-not-translate list; choose a
   large-context model; mind low-resource languages. Exported XLIFF marks
   agent translations with the `leveraged-mt` qualifier.

The running demo is a landmarks-learning app translated into Canadian French.

---

## 1. Why context, not just an LLM (Introduction · 0:00)

Traditional ML models struggled with software localization; modern LLMs are
great at general-purpose translation but still trip on software strings **without
the right context**. The motivating ambiguity: the English word *book* — a thing
to read, or the act of reserving a room — looks identical in source but has
distinct translations in other languages. Pick wrong and the UI misleads.

Xcode already has the context to resolve this, accumulated through String
Catalogs:

- **Two years ago** — String Catalogs began tracking *where in code* a string is
  used.
- **Last year** — they began tracking *how* strings are used, to auto-generate
  descriptive comments.
- **This year (Xcode 27)** — that context is brought together and fed to coding
  agents that translate your strings directly in Xcode.

## 2. Adding translations (1:49)

The demo app uses SwiftUI, so its UI is **localizable by default** — `Text` and
`Button` automatically expose their strings for localization.

```swift
Text("Hello, world!", comment: "A standard greeting")
```

You can route strings to a named String Catalog with a custom table name:

```swift
Text("Hello, world!", tableName: "Greetings", comment: "A standard greeting")
```

The flow, kicked off via the **New Conversation** button in Xcode's toolbar
("translate my app into Canadian French"):

1. **Agent → Xcode: prepare the project.** Xcode adds the language to project
   settings (Canadian French), then **builds all targets for all supported
   platforms** so every localizable string is discovered, then adds newly
   discovered strings to String Catalogs — creating them automatically if none
   exist. Default table is `Localizable`; custom `tableName:` values get their
   own catalog. (The demo project ended up with four String Catalogs.)
2. **Agent selects the strings to translate** — here, the whole project, so it
   reads all four catalogs.
3. **Agent batches the strings to subagents.** Xcode gives each subagent context
   per string: where in code it's used, a list of strings using similar
   terminology, and even **how the string was translated in other languages**.

### Plurals handled per language

Translating `%lld items` (the `%lld` placeholder becomes a number at runtime):
English varies by plural ("one item" / "two items"); the subagent produced the
Canadian French plural variations `un élément` / `deux éléments`. Other languages
need more or fewer plural categories — Xcode tells subagents exactly which
variations are required for the target language.

### Iterating on terminology

The default translation for "landmarks" was the well-understood `lieux
d'intérêt`. The developer (a Canadian French speaker) preferred the more local
tourism term `attraits` ("attractions") and renamed the app to `Attraits phares`
("Flagship attractions"). Asking the agent to make the change updated all
relevant strings, including the app name and "Draw a sketch of this landmark."

### Consistency across conversations

Adding a *new* feature (a label challenging users to discover all landmarks) in a
**separate new conversation** still produced the custom `attraits` translation —
without reading the previous conversation. Xcode guided the agent to discover and
reuse a translation it "would not have chosen by default," keeping the app
cohesive in Canadian French.

## 3. Review and iterate (8:11)

Adding translations is only half the job — they have to *work at runtime*.
Languages differ: Canadian French sentences are longer on average than English,
so text can overflow.

- **Truncation check:** ask the coding agent to render the new feature's UI in
  the target language and look for truncation. In the demo it flagged a string
  truncating before the end of the sentence. From there the dev decides: an
  implementation bug, a UI redesign to fit longer text, or asking the model for a
  shorter translation.
- The same render-and-inspect loop catches **vertical clipping** for tall scripts
  like **Thai** and **incorrect alignment** for **right-to-left** languages like
  **Arabic**.
- Deep dives: **"Build multilingual-ready apps"** and **"Get it right (to
  left)."**

### Native-speaker feedback via TestFlight

A developer's edge with coding agents is that they understand code and can tell
when an agent is wrong — but that edge **disappears for languages you don't
speak**. Use **TestFlight** to have native speakers test localized builds and
send suggestions or screenshots of issues before public release. (The dev caught
the `attraits` improvement only because they speak the language; TestFlight is
where equivalent suggestions for *other* languages would come from.) Deep dive:
the **"Get Started with TestFlight"** tech talk.

## 4. Best practices (11:02)

### Make strings localizable

A well-localized app needs *all* user-facing strings translated, which requires
them to be localizable in the first place. SwiftUI is localizable by default;
elsewhere use `String(localized:)` or `LocalizedStringResource`:

```swift
String(localized: "Hello, world!", comment: "A standard greeting")

LocalizedStringResource("Hello World!", bundle: #bundle, comment: "A standard greeting")
```

Deep dive: **"Code-along: Explore localization with Xcode."**

### Guide the agents

Agents already have Apple's translation expertise via **language-specific style
guides that ship in Xcode**, but they respect guidance you provide. You can write:

- a **glossary** of preferred translations for specific words,
- a **do-not-translate list** (product names, trademarks),
- a plain-text **tone** description (a banking app vs. a kids' app read very
  differently).

Put this in an `AGENTS.md` section, but the recommendation is a dedicated
**`TRANSLATION.md`** — so the extra context is loaded **only** for
translation-related tasks, not every agent interaction. The agent reads it only
when working on translation.

### Choosing a model

- Translation is "complex and long-running," especially for cross-project
  terminology consistency — prefer a model with a **large context window** that
  **excels at extended requests**.
- **Low-resource languages**: some languages appear less in training data, which
  can yield lower-quality translations; some models are better/worse per
  language. Check the provider's docs on training-data language distribution.

### `leveraged-mt` qualifier

When handling localizations exported from Xcode, check the `leveraged-mt`
`state-qualifier` to know which translations came from an agent:

```xml
<trans-unit id="Grand Canyon" xml:space="preserve">
  <source>Grand Canyon</source>
  <target state="translated" state-qualifier="leveraged-mt">Grand Canyon</target>
  <note>Name of the 'Grand Canyon' landmark.</note>
</trans-unit>
```
