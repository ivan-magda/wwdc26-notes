---
title: "Principles of great design — Full Digest"
session: WWDC26 · 250
url: https://developer.apple.com/videos/play/wwdc2026/250/
duration: 17m
speakers: Linda, Doug (Design Evangelists, Apple)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 250 — Principles of great design

## TL;DR

Two Design Evangelists, Linda and Doug, reframe design from "how it looks / how it
behaves" to **"making something with intention."** Every feature costs a person's
**time, attention, and trust**, so deciding what *not* to build is as important as
what you do build. The talk presents **eight principles** — Purpose, Agency,
Responsibility, Familiarity, Flexibility, Simplicity, Craft, Delight — with a key
caveat: there's **no formula**. Leaning into one principle can mean compromising
another, and resolving those tensions with judgment is the actual craft of design.

Headline themes:

1. **Intention over aesthetics** — design is editorial; saying no is a feature.
2. **People in control, protected** — Agency (forgiveness, undo) balanced by Responsibility (privacy, safety, AI safeguards).
3. **Build on what people already know** — Familiarity (metaphor + consistency) without recycling one solution everywhere → Flexibility.
4. **Simplicity ≠ minimalism** — remove friction, not information; sometimes *add* context to be simpler.
5. **Craft earns trust; Delight is the byproduct** — not confetti, but the natural result of getting everything else right.

---

## 1. Intro — what is design?

Most of us, if honest, would answer "design is how something looks" or "how something
behaves." Not wrong, but an **incomplete picture**. At Apple, **design is making
something with intention** — focusing on what's most important to people so you build
something they'll truly value.

The framing device for the whole talk: **every feature asks something of the person
using it — their time, their attention, and their trust.** Those are finite and you
can't afford to waste them. So **choosing what to build is often a matter of deciding
what not to include.** Before a single sketch or line of code, ask whether the thing
has purpose.

Important meta-point stated upfront: there is **no formula** that guarantees the
perfect solution, and leaning into one principle can feel like compromising another.
That tension is what makes design interesting — judgment and intuition do the rest.

## 2. Purpose

The first foundational principle. Purpose means designing experiences that **genuinely
serve people**: ones that respect and adapt to their lives, are clear and considered,
and at their best a genuine joy to use. The actionable gate: **before writing code, ask
whether what you're making has real purpose and delivers something people will truly
value.**

## 3. Agency

**Agency is putting people in control.** People feel in control when you let them do
things *their* way.

- **Offer choices** — the best way to bring agency in. An interface should never stand
  in the way of what someone is trying to do.
- **Don't railroad** — instead of a predetermined path, let people dive in and explore
  at their own pace. Engagement rises with agency.
- **Forgiveness** — because freedom means mistakes. Make it easy to **undo** any action;
  **double-check destructive actions**; use **interruptions sparingly**, only when
  someone is about to make a big mistake. Forgiveness gives people the confidence that
  they can recover from anything they try — capable, secure, free to explore.

## 4. Responsibility

Freedom comes with a duty to protect people's well-being. On Apple platforms,
**responsibility means acting in people's best interest**, starting with privacy.

- **Privacy is a human right.** The "give me your phone number / for what? / I'll tell
  you once you give it to me" sketch maps directly onto interfaces that throw
  **permission prompts on launch** — before you even know what the app does — or ask
  for data without context. A responsible design **waits for the right moment**, asks
  **only for what's necessary**, and is **transparent about what the data is for**.
- **Safety.** Look hard at your functionality and ask: *How could this be misused? Who
  would be harmed? How do I prevent it?*
- **Responsible AI.** When you add intelligent features, **anticipate that a model may
  generate something unexpected or inaccurate.** The recipe-app example: a logged
  allergy plus a model that suggests a dangerous ingredient = real-world harm you can't
  leave to chance. Add safeguards — **previews, confirmations, disclaimers** — and
  **remove the feature entirely if the risk to safety outweighs the value.**

Taking this seriously is what leads to a product people can trust.

## 5. Familiarity

**Building on what people already know.** Your audience arrives with a lifetime of
real-world experience plus conventions learned from other interfaces.

- **Metaphor** — used since the earliest interfaces (the **trash can**: unwanted things
  go in, and you can retrieve a mistake from it). The trick is **not too literal, not
  too abstract.** The **inspector** example: too literal and people don't recognize what
  you're showing; too abstract and the idea doesn't land. A good metaphor lets people
  **predict** what something will do — used right, it instantly clicks; used wrong, it
  surprises in a bad way (e.g. a trash-can icon that doesn't mean delete, or a
  "creative" delete icon that loses immediate recognition). For common actions, **don't
  reinvent the wheel.**
- **Consistency** — things that look the same should behave the same. If one button
  navigates, another toggles, another opens a modal, there's no learnable pattern.
  **Consistent placement** matters too: on Mac you always close a window from the
  top-left corner. Same action, same location across screens and devices = people don't
  have to think.

But familiarity does **not** mean recycling one solution everywhere — which leads to
flexibility.

## 6. Flexibility

A flexible design recognizes that **people use your design in ways as unique as they
are.** Support the different contexts people are actually in.

- **Context** — listening to music changes completely by situation: at home through
  speakers, on a run with AirPods + Watch, driving fully hands-free. Accommodating
  these feels more comfortable and serves a wider audience.
- **Platform strengths** — iPhone wants quick, touch-based interactions; Mac expects
  deep workflows and precise pointer control. Every device deserves a solution that
  takes advantage of what makes it unique.
- **Range of abilities** — get curious about your audience: age, languages, pro vs.
  novice, reliance on accessibility features. You won't solve for everyone on day one,
  but you can keep making the experience more inclusive.
- **Personalization** — when no single layout works for everyone (e.g. controls), let
  people **rearrange** or **hide** what they don't use. Flexibility is an investment
  that proves you designed with *them* in mind.

## 7. Simplicity

**Stripping away the unnecessary so the core purpose shines** — and crucially,
**simple is not minimal.** Burying all functionality in one place looks minimal but
isn't simple. Simple designs are **frictionless and intuitive**; people find what they
need without effort. Two routes:

- **Concise** — plain language, no jargon, speak naturally, avoid redundancy, get to
  the point, and reduce the number of steps. Respect people's time.
- **Clear** — the design communicates what it does. Clarity is built with **hierarchy**
  (order, spacing, contrast) so the most important item is the most obvious one. Clear
  interfaces answer: *What do I pay attention to? What can I interact with? How do I
  interact?*

Every element must earn its place — distill data (a graphic may beat a table),
summarize so people focus on what they care about. The counterintuitive twist:
**sometimes simpler means adding more.** A video play/pause control that also shows
**where you are and how much time is left** gives the context needed to make an
informed decision. You've reached simplicity when you have **exactly enough.**

## 8. Craft

**The attention to detail that tells people you care.** We all recognize a cheap
product — a rickety door, a shirt that unravels — and software is the same: laggy
buttons, jittery scrolling, misaligned icons, layouts that break on rotation feel
**fragile**, and fragility makes people doubt the quality of the results. A
meticulously crafted design does the opposite: it **inspires confidence.**

Ingredients of well-crafted design (high-quality "materials"):

- **Beautiful fonts** that look great across devices.
- **Thoughtful colors** that adapt across light and dark.
- **Clear graphics and iconography.**
- **Responsive animations** that feel fluid and give immediate, natural feedback.
- All on a **solid foundation of reliable, secure SDKs.**

Quality takes **time** and comes from **iteration**. And craft is **continual** —
a large part is **maintenance**: great design has longevity, so keep evolving it, and
when new features or hardware arrive, explore whether they fit your experience. Evolving
with those changes makes people feel supported and rewarded.

## 9. Delight

**Hard to define, instantly recognized.** Delightful interfaces are satisfying,
enriching, and create a real emotional connection — one that starts when an experience
**feels human**.

The key correction: **delight isn't confetti** or flourishes tacked on at the end. You
create it by **identifying the emotion you want your audience to feel** — relaxed,
confident, excited — and finding opportunities to reinforce that throughout the design.
**Delight is the sum of the consideration you put in — the natural result of getting all
the other principles right.** Design with intention and care — agency to act, safety to
explore, comfort of familiar patterns, the ability to make it their own — and you create
an experience that's a true joy to use.

## Closing / resources

- **Human Interface Guidelines** — the best place to start designing for Apple platforms.
- A **new design principles page** has been added to the HIG to go deeper on these ideas.
