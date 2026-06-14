---
title: "Create UI prototypes using agents in Xcode — Full Digest"
session: WWDC26 · 227
url: https://developer.apple.com/videos/play/wwdc2026/227/
duration: 18m
speakers: Sam (Apple Design Team, prototyper)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 227 — Create UI prototypes using agents in Xcode

## TL;DR

A design-team prototyper's playbook for using **Xcode coding agents + Xcode previews**
to prototype with intention. The argument: because it's never been easier to *produce*
an app, the differentiator is *intentional design*, and prototyping is how you get
there. Three phases, one throughline.

1. **Explore — go wide, remix, repeat.** Don't accept the agent's first vague guess.
   Write specific prompts that generate *many* divergent UI variations at once, each in
   its own named Swift preview, then prompt the agent to recombine the elements you
   liked.
2. **Make it feel lived in.** Have the agent act as a stand-in user: populate the
   prototype with plausible sample content and deliberately cover edge cases — empty
   states, truncation vs. multi-line, unbounded lists. Keep sample data in its own
   reusable file.
3. **Tune key moments.** Animation, springs, friction, device motion, haptics are
   harder to get right. Instead of editing scattered constants, have the agent build a
   **custom tuning panel** with live controls, laid out side-by-side on a wide canvas.

Throughline: **agents are collaborators, not designers.** They produce real native
code (so prototypes carry forward), but your judgment has final say. Do not delegate
critical thinking.

The running example is a **book-club management app**.

---

## 1. The two primitives

Everything rests on two Xcode features:

- **Coding agents** — start a new conversation, describe the change or feature in
  natural language, the agent writes it. Crucially, it produces **real native code**,
  so a prototype isn't throwaway — you carry it forward into the shipping app.
- **Xcode previews** — visualize and interact with UI without rebuild/run. Reach them
  via the **"show canvas"** button; the file needs a preview view specified. Each
  variation the agent makes gets its own *named* preview, and the canvas shows them as
  switchable tabs.

The stated philosophy, up front: *"Do not delegate critical thinking to these tools…
you always have final say."*

## 2. Exploring UI possibilities — go wide, remix, repeat

The anti-pattern: typing *"create a UI for managing a book club that meets
regularly."* It generates *something* fast, but:

- the layout is arbitrary (one of many valid organizations),
- the agent **guesses** at features you never specified (polling, a photo gallery),
- and you get **anchored** on a flawed start — stuck with navigation that fit a
  feature set you didn't want, accreting feature creep as you patch it.

The fix — make prompts specific and divergent:

- **Name your features** up front. You know your problem space better than the agent.
- **Give stylistic cues** — the mood/feeling (warm coffee-shop palette? paper +
  beautiful typography?).
- **Ask for multiple options.** Early exploration is the cheapest it will ever be.
- **One named Swift preview per variation** so you can flip between them.

In the demo, the better prompt yields **10 distinct solutions** — *Club Hub* (tab
structure), *Cozy* (system New York typeface, clear current-book + location section),
a *racetrack* progress metaphor, *Editorial* (clean typography), *Blueprint Atelier*
(grid → detail), and one that "was worth a shot."

Then **remix**: a follow-up prompt naming the variations and the specific elements
worth combining (e.g. a standings board + an image of the current book) produces new
hybrids from only those elements. Iterating this way, Sam refines from a Cozy-inspired
design → racetrack visual → neutral appearance → simplified/de-duplicated layout.

> Mantra: **make lots of variations and see which components or ideas inspire you.**

See `code.md` prompt patterns 1 and 2.

## 3. Making your app feel lived in

Real user feedback is the goal, but early on you may not have the app in a state where
people can use it with their own content. So let the agent **play the role of a user**
and fill the prototype with realistic content — going from a blank, loosely-filled
template to something rich and close to real use.

Prompting tips:

- **Ask for many previews, not one** (recurring theme).
- **Think through edge cases yourself** and state them concretely so the agent doesn't
  overlook them. Examples from the demo:
  - **Empty state** — how does the detail area look with no meeting scheduled yet?
  - **Long input** — should text truncate or wrap to multiple lines?
  - **Unbounded growth** — club member count, message-thread length, number of past
    books, leaderboard length.
- **Keep sample content plausible** for the audience (book-club discussions should be
  about books).
- **Put sample models in their own reusable, editable file** so you can revise them
  and reuse across future prototypes.
- One named preview per variation.

What surfaced from running real content through the UI:

- No blank-slate UI existed → added account management + call-to-action controls.
- Long next-meeting descriptions overran the book cover → allowed truncation; realized
  the book title was redundant with its cover → removed it.
- An overlong leaderboard buried the discussion → always show *your* relative rank,
  with an **expand control** for the full list.
- Real book covers inspired a new idea: adapt the detail page's colors to the cover.

> The specific fixes matter less than the lesson: **real content reveals the problems**
> a blank template hides. Still: nothing beats real-world use; this is a head start on
> the feedback stage.

See `code.md` prompt pattern 3.

## 4. Tuning key moments

Static elements (navigation, controls, type, color) are the easy part. SwiftUI's
**interaction, animation, and transitions** are harder to get right. A vocabulary of
dynamic elements:

- **Ease** — object gently accelerates, decelerates, or both; you pick the duration.
- **Spring** — mimics a spring force; three params: **stiffness, damping, mass**.
- **Friction & inertia** — perceived weight when dragging an element (e.g. the weight
  felt closing a sheet in Music).
- **Device motion** — response to accelerometer/gyroscope (Wallet's Apple Cash
  iridescent parallax).
- **Haptics** — communicating key moments/modes (Find My's getting-warmer cues).

This app's key moments are animations, so the talk focuses there.

### Build a tuning panel

Modifying constants directly in Xcode previews works, but the constants you care about
often live in scattered files and the context switching is clunky. Better: **have the
agent build a custom UI whose only job is tuning the parameters relevant to your
interface.** The demo shows a simple "toss an element at a goal" interface with a menu
button that opens a tuning panel for spring properties.

Best practices for asking the agent for a tuning panel:

- **Be detailed about what you're iterating on** — animation styles? spring curves?
  multi-element animations? a transition where views enter/leave the hierarchy?
- **Break the animation into phases** — gives you and the agent shared vocabulary. The
  demo's transition has two: (1) view transitions to the detail page; (2) every
  subsequent row animates in with staggered timings.
- **Tuning panels generalize** — not just animation params, but swapping app states,
  colors, font styles, visual offsets.
- **Lay it out side by side on a wider window** so toggling settings doesn't obstruct
  content or force context switching. A panel that obstructs in a small window can use
  a **resize control** to expand onto a larger canvas.

Outcome: per-phase controls let Sam inspect each phase in isolation, decrease the
delay/stagger values, try the **bouncy** preset — and the post-transition sequence
ends up smooth and delightful.

> The reusable flow: *anytime you're managing multiple configs of a view or choosing
> between animation/interaction parameters, make a tuning panel, shorten the feedback
> loop, get to what feels optimal.*

See `code.md` prompt pattern 4.

## 5. Next steps / the throughline

The closing message: **don't think of agents as designers — think of them as
collaborators** that help you arrive at the best experience. They produce real code
that carries forward, but the key piece of the puzzle is **your judgment.**

Pointer to the deep-dive: **"Xcode, agents, and you."**
