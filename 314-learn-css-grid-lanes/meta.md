# Session 314 — Learn CSS Grid Lanes

- **URL:** https://developer.apple.com/videos/play/wwdc2026/314/
- **Duration:** 10m
- **Speakers:** Brandon (engineer, Safari team)

## Description

CSS Grid Lanes is a new web layout mode for masonry-style "waterfall" and "brick
wall" designs — the pattern where items of mixed aspect ratios flow into the
available space without stretching, zooming, or cropping. It sits between Flexbox
and Grid: it structures one axis and leaves the other free, packing items tightly
while preserving their natural proportions. It takes about three lines of CSS and
ships today in Safari 26.4 (behind a flag elsewhere).

## Key topics

- `display: grid-lanes` — new layout mode that solves masonry / waterfall / brick-wall layouts without JS libraries, floats, or Flexbox hacks
- Conceptual placement: structures one axis (columns OR rows), leaves the other free; each item lands in whichever column leaves it closest to the top
- Build a container in three lines: `display: grid-lanes`, `grid-template-columns` with `fr` units, `gap`
- Brick-wall variation: swap `grid-template-columns` for `grid-template-rows` (one direction only, not both)
- Full track sizing carries over: unequal columns (`1fr 2fr 1fr`), `auto-fill` + `minmax()`, repeating narrow/wide patterns
- Item-level control with existing Grid properties: `grid-column: span 2`, explicit placement (`2 / span 2`), `subgrid` (rows are always browser-chosen)
- `flow-tolerance` property — loosens the shortest-column rule so items prefer earlier columns when heights are close, fixing DOM-vs-visual order accessibility issues; default `1em`
- Safari Web Inspector has full Grid Lanes overlay support (column/row lines, order numbers, gaps)

## Related sessions to fetch (referenced in this talk)

- [ ] What's new in WebKit for Safari 27

## Chapter summary (Summary tab)

- **0:00 Introduction** — Grid Lanes is a new layout mode for masonry-style waterfall and brick-wall patterns in a few lines of CSS; available now in Safari 26.4.
- **1:35 CSS Flexbox and Grid** — what a layout mode does; why Flexbox and Grid fall short for mixed aspect ratios, where stretching, zooming, and cropping all distort the design.
- **2:45 CSS Grid Lanes** — fits between Grid and Flexbox; structures one axis, leaves the other free so items pack without distortion; each item lands in whichever column leaves it closest to the top; works for images, text, or anything.
- **3:55 Build a Grid Lanes container** — first layout in three lines: `display: grid-lanes`, `grid-template-columns` with `fr` units, and `gap`.
- **4:31 Implement brick variation** — flip 90° by swapping `grid-template-columns` for `grid-template-rows`; one direction at a time.
- **4:49 Experiment with different layouts** — full track-sizing power: unequal widths, `auto-fill` with `minmax()`, repeating narrow/wide patterns.
- **5:40 Control individual items** — `grid-column: span 2`, explicit column placement (rows still chosen for you), `grid-template-columns: subgrid` to align nested content.
- **7:05 Flow Tolerance** — shortest-column rule can confuse keyboard users when DOM order and visual order diverge; `flow-tolerance` loosens the rule (default `1em`).
- **8:46 Web Inspector** — debug visually with the overlay: column/row lines, gaps, and order numbers over each item.
- **9:20 Next steps** — WebKit's Grid Lanes Field Guide (interactive demos); try it in Safari 26.4; share feedback; watch "What's new in WebKit for Safari 27."

## Code

See `code.md` — 11 distinct CSS snippets extracted from the Code tab.
