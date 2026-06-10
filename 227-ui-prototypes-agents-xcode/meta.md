# Session 227 — Create UI prototypes using agents in Xcode

- **URL:** https://developer.apple.com/videos/play/wwdc2026/227/
- **Duration:** 18m
- **Speakers:** Sam (prototyper, Apple Design Team)

## Description

How to use Xcode's coding agents and Swift previews to prototype with intention.
The session walks through three phases of early-stage design work: exploring many
divergent UI directions at once, populating a prototype with realistic sample data
and edge cases so it feels "lived in," and tuning the dynamic moments — animation,
springs, interaction — by having the agent build custom tuning panels. The
throughline: treat coding agents as collaborators, not designers; your judgment
makes the final call.

## Key topics

- Two Xcode primitives this all rests on: **coding agents** (describe a change, the
  agent writes real native code) and **Xcode previews** (visualize/interact without
  rebuild — reach them via the "show canvas" button).
- **Go wide, remix, repeat** — craft specific prompts that generate *multiple* UI
  variations at once, each in its own named Swift preview; then prompt to recombine
  the elements you liked.
- Prompt hygiene: name your features up front, give stylistic/mood cues, and always
  ask for multiple options early when exploration is cheapest.
- **Making the app feel lived in** — agents generate plausible sample content and act
  as a stand-in user; ask for many previews covering edge cases (empty states,
  truncation vs. multi-line, unbounded lists/leaderboards/conversations).
- Keep sample data in its own reusable, editable file so you can revise it and reuse
  it across prototypes.
- **Tuning key moments** — taxonomy of dynamic elements: ease vs. spring animation
  (spring params: stiffness, damping, mass), friction/inertia, device motion
  (accelerometer/gyroscope parallax), and haptics.
- **Tuning panels** — have the agent build a custom UI to adjust parameters live;
  break animations into named *phases* for a shared vocabulary; lay the panel out
  side-by-side on a wider window so toggling doesn't obstruct or require context
  switching.
- Agents produce real Swift code, so prototypes carry forward into the actual app.

## Related sessions to fetch (referenced in this talk)

- [ ] Xcode, agents, and you

## Chapter summary (Summary tab)

- **0:00 Introduction** — Why intentional prototyping matters for standing out; the
  session covers using Xcode coding agents + previews to find creative starting
  points, bring in real content, and tune key moments and interactions.
- **2:56 Exploring UI possibilities** — Craft prompts that generate multiple UI
  variations at once and remix the most promising elements into refined iterations.
- **7:31 Making your app feel lived in** — Use agents to populate prototypes with
  realistic sample data and cover edge cases, including empty states, long text, and
  unbounded lists.
- **11:19 Tuning key moments** — Refine the dynamic elements of your prototype and
  build custom tuning panels to adjust animation parameters in real time.
- **17:31 Next steps** — Treat agents as collaborators rather than designers, with
  your judgment as the key to finding the best experience; pointer to "Xcode, agents,
  and you."

## Code

See `code.md` — no Code tab for this session; it's a demo/design talk. Prompt
patterns and the SwiftUI animation vocabulary named in the transcript are captured
there instead.
