---
title: "Craft clear names for features and labels in your app — Full Digest"
session: WWDC26 · 290
url: https://developer.apple.com/videos/play/wwdc2026/290/
duration: 15m
speakers: Heej (UX writer, Human Interface design team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 290 — Craft clear names for features and labels in your app

## TL;DR

Naming is a design decision. The name you give a feature, label, menu, or
setting is "as fundamental to the experience of your app as the layout,
interactions, and visual scheme." The session hands you three reusable tools:

1. **Criteria** — a good name **belongs**, **sets the right expectation**, and
   **works everywhere**. It's a guide, not a rulebook: you don't have to check all
   three boxes, and you decide how to weigh them given where the name lives.
2. **Process** — start from your **audience**, then run the **"think / feel / do"**
   exercise to brainstorm candidates, group them by theme, and gut-check them in
   natural sentences.
3. **Evaluation** — score candidates against the criteria and let the feature's
   **context** decide which criteria matter most.

The same criteria apply whether the right answer is **descriptive** (Enhance
Dialogue), **emotional** (Memories), or a **coined word** (AutoMix). Good names
compound — each one makes the next easier and becomes the language of your app.

---

## 1. Why naming matters (00:07)

Heej, a UX writer on the Human Interface design team, opens with the moment you
open an app "and just knew where to go — that feeling is designed." Naming is one
of the most powerful tools for creating it, and one of the easiest to overlook.

- Apple's 50-year track record of names that became everyday language — Mac,
  iPhone, iCloud — is the headline case, but **naming happens at every level**:
  menus, tab bars, settings. The small choices add up to how someone feels about
  your app.
- The talk's structure: the **qualities that make a name work** (and what's at
  stake when it falls short) → an **exercise** to reach the right name → how to
  **evaluate** any candidate so you leave with something concrete to apply.

## 2. The three criteria (01:18)

- **Belongs** — fit. A name that belongs doesn't just sound like your app; it works
  at every level: what users expect to find there, and how it lives among everything
  else you've named.
- **Sets the right expectation** — clarity and trust. When someone reads a name they
  are already predicting what they'll find; the right name delivers on that, and
  trust builds.
- **Works everywhere** — it travels, holding up across languages, markets, platforms,
  and the contexts where your app lives.

> Important caveat (02:02): names won't always check every box, and you may have
> criteria of your own — **trademark** considerations, **industry regulations**.
> Treat the three as **a guide, not a rulebook**. The trade-offs are yours.

### Case study: Apple Cash → "Balance" (02:19)

Labeling the amount sitting next to the Send button:

- **Spending Power** — compelling but not concrete; raises questions (credit limit?
  a score?). In a financial context, **ambiguity is the worst outcome**. Doesn't
  feel like it belongs in an Apple service. And the **tone**: if it's low or zero,
  "Spending Power" reads as a **judgment**, not a label — and payment services run
  on trust, which a judgmental name quietly breaks.
- **Current Funds** — accurately descriptive but "sounds like something out of a
  spreadsheet." The out-loud gut check fails: nobody says "let me check my Current
  Funds." It doesn't belong in a service for sending and receiving money.
- **Balance** ✓ — the industry-standard term: well-understood, clear, neutral. Here
  **clarity and trust come first, not brand expression.** It belongs, sets the right
  expectation, and travels without friction. Sometimes the obvious word is right
  because it's already doing the job.

### Case study: gym plans — clarity vs. brand (04:15)

- **Basic Access / All Access** — clear and easy to choose between.
- **Lightweight / Heavyweight** — fun and on-brand, but introduces a **learning
  curve** ("what do those actually mean?") that makes choosing harder.

The lesson: you don't have to maximize every criterion. Maybe the branded names
really do fit the gym better — that's fine, there's just **more work to make sure
they're understood**. Sometimes you lean into clarity, other times into brand; ask
what matters most **given where the feature or setting lives**, and let that set
your priorities.

## 3. The process — "think / feel / do" (05:25)

The trap: it's natural to name something by **what it does**, the **technology**, or
the **function**. But users don't see it that way — they want to know what it does
**for them**.

**Start from the audience.** Who is the app for — new parents? Marathon trainees?
With the audience in mind, ask: when they encounter this feature, what should they
**think**, **feel**, and **do**?

### Worked example: Apple Maps → "Visited Places" (06:05)

A feature that remembers places you've been (that new café, the park with accessible
trails). Run the exercise — **one idea per sticky note**:

- **Think** — what should people think? Easy, helpful, even clever. Write down as
  many as you can and **don't filter yet**; you're hunting for recurring **themes**,
  not the perfect word.
- **Feel** — the fun of rediscovering a place you couldn't name for ages; and
  **secure**, knowing your places are private and encrypted. Both matter.
- **Do** — find the feature, use it, and share places with whomever they want.

Then **step back and group by theme**. For this feature three themes emerged:
**Ease** (find it without effort), **Excitement** (the rediscovery surprise), and
**Security** (built with privacy in mind, so that has to come through).

**Filter against the criteria**, then **the sentence test**: drop each candidate
into something you'd actually say — "Hey, check out ___" / "search for ___." A name
that reads and sounds natural is worth exploring. (They considered "Private
Memories" this way.)

The team landed on **Visited Places** — descriptive, clear, already at home in an
interface that uses "places" throughout; it sets the right **ownership** expectation
(your places, not readable by Apple) and works across languages. Reminder: it
doesn't have to pass all three criteria, "but it's great when it does."

### Worked example: Apple Photos → "Memories" (09:29)

An algorithmic photo-grouping feature that surfaces moments that matter. The person
on the other end "isn't thinking about algorithms — they're looking for a memory."
**Memories** wins because it **meets them there** in a way a technical label can't;
it fits the app's tone and the relationship people have with their photos. Still
straightforward, but **Memories reads clearly because it names the emotional
payoff rather than the mechanism.**

## 4. Evaluation — does yours work, and why? (10:34)

Every example works, but for different reasons. The question is how you know when
yours does too.

### Apple Podcasts → "Enhance Dialogue" (10:42)

A feature that isolates voices / reduces background noise without altering the
original audio, living in the playback-speed menu. Iterating:

- **Vocal Isolation** / **Isolate Vocals** — a verb is right here (the feature is
  something you **do**, putting the user in control), but this is an **audio-engineering
  term**: it describes what the tech does, not what you experience.
- **Clarify Speech** — closer, but only tells half the story; there's more to the
  feature than clarity.
- **Enhance Playback** — puts the feature before the person (enhancing what? for whom?).
- **Enhance Dialogue** ✓ — answers **what's being enhanced and for whom** before you
  even tap. Fits the context, sets the expectation, delivers on it. Bonus evidence
  it belongs: the **same name already ships on Apple TV** for a similar feature.

### Apple Music → "AutoMix" (12:32)

A setting that handles song-to-song transitions automatically so playback never
stops. A **coined word**: **Auto** (happens without you) + **Mix** (blends songs)
form a word that doesn't exist yet is instantly understandable. Naming with intention
means **you don't have to default to existing words** — AutoMix "earns its clarity
from its parts," so the invented word doesn't have to explain itself. Branding
isn't only for hero features.

## 5. Takeaways (13:21)

Descriptive (Enhance Dialogue), emotional (Memories), branded (AutoMix) — the
criteria don't change; **how you weigh them is your call.** Three things to carry:

1. **Naming is fundamental** to the experience — on par with layout, interactions,
   and visual scheme.
2. **Come back to the criteria** every time: Does it belong? Does it set the right
   expectation? Will it hold up everywhere your app lives?
3. **The best names speak to the person, not just the function.** When what the
   product *is* and what the person *needs* align, the name truly belongs.

And good names **compound** — each one makes the next easier and, over time, they
become the language of your app. Closing pointer: check out Apple's **UX-writing
sessions from previous years** (no specific titles given).
