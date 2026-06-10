# Code samples — Session 227

_No Code tab for this session._ This is a demo/design talk; the agent-generated
Swift never appears as copyable snippets. What it *does* give you are reusable
**prompt patterns** and a SwiftUI **animation vocabulary** — both captured below.

## Spoken APIs / SwiftUI concepts named

No concrete API signatures are shown on screen, but the talk names these SwiftUI /
platform building blocks:

- **Xcode coding agents** — new-conversation button; agents write real native code.
- **Xcode previews** — "show canvas" button; each variation gets its own *named*
  `#Preview`. Tabs in the canvas switch between named previews.
- **Animation styles**
  - **Ease** — gentle accelerate / decelerate / both; you choose the duration.
  - **Spring** — three tunable parameters: **stiffness**, **damping**, **mass**.
  - **Bouncy** — a SwiftUI preset animation style tried at the end.
- **Other dynamic elements** beyond animation: **friction & inertia** (perceived
  weight when dragging, e.g. closing a Music sheet), **device motion** (accelerometer
  / gyroscope — Wallet's Apple Cash iridescent parallax), **haptics** (Find My
  proximity cues).
- **Animation phases** — break a transition into named phases (phase 1: transition to
  detail page; phase 2: staggered, delayed row entrances) so you and the agent share
  vocabulary; tune delay + stagger values per phase.

## Prompt patterns (the real takeaway of this session)

### 1. Explore — generate many variations at once

```text
Create several variations of a UI for managing a book club that meets regularly.

Features I want: <list them explicitly — e.g. current book + cover, meeting
location/time, member standings, discussion thread>.

Mood: <stylistic cue — e.g. warm coffee-shop palette, OR paper + beautiful
typography>.

Give me multiple divergent options. Put each variation in its own Swift preview
with its own unique, descriptive name.
```

Result in the demo: 10 named variations (Club Hub, Cozy, Editorial, Blueprint
Atelier, …), clickable in the preview canvas.

### 2. Remix — recombine the elements you liked

```text
From the variations above, I like: <the standings board from X>, <the current-book
image from Y>, <the typography from Editorial>.

Create new hybrids using only these elements. Each iteration gets its own Swift
preview with its own unique name.
```

### 3. Make it feel lived in — sample data + edge cases

```text
Populate these views with realistic sample content for a book club (discussions
centered on books).

Cover these edge cases explicitly: no meeting scheduled yet (empty state); very long
next-meeting descriptions (truncate vs. multi-line); unbounded lists — many club
members, long message threads, many past books, an overlong leaderboard.

Put the sample models in their own file so they're easy to edit and reusable across
future prototypes. Each variation gets its own Swift preview with a descriptive name.
```

### 4. Build a tuning panel for the dynamic moments

```text
Build a tuning panel to manage this animation.

The animation has two phases: (1) the view transitions to the detail page; (2) each
subsequent row animates in with staggered timings.

Expose controls for: <spring stiffness/damping/mass OR ease duration>, per-phase
delay, stagger amount, and a way to inspect each phase in isolation.

Lay the tuning panel side by side against the UI on a wider window — not as a clunky
modal that obstructs the content — so I can toggle settings without context
switching. Include a resize control to expand onto a larger canvas.
```

Tuning panels generalize beyond animation: also use them to swap app states, colors,
font styles, or visual offsets.
