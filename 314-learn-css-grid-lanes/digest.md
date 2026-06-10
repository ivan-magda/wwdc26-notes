---
title: "Learn CSS Grid Lanes — Full Digest"
session: WWDC26 · 314
url: https://developer.apple.com/videos/play/wwdc2026/314/
duration: 10m
speakers: Brandon (Safari team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 314 — Learn CSS Grid Lanes

## TL;DR

Grid Lanes is a brand-new CSS layout mode — `display: grid-lanes` — that does
masonry ("waterfall") and brick-wall layouts natively, in roughly three lines of
CSS, with no JavaScript library, no float hacks, and no Flexbox workarounds.

Three headline ideas:

1. **A third layout mode between Grid and Flex.** A layout mode answers two
   questions: *where do items go* and *how much space do they get*. Flexbox
   structures one axis with a single flowing lane; Grid structures two axes into a
   rigid cell matrix that leaves gaps for mixed aspect ratios. Grid Lanes structures
   **one** axis and leaves the other free, so items pack tightly while keeping their
   natural proportions — no stretch, no zoom-overflow, no crop.
2. **It reuses everything you already know from Grid.** `grid-template-columns` /
   `grid-template-rows`, `fr`, `repeat()`, `auto-fill`, `minmax()`, `gap`,
   `grid-column: span`, explicit placement, and `subgrid` all carry over.
3. **Two new pieces of behavior:** the placement algorithm (each item drops into
   whichever column leaves it closest to the top) and the **`flow-tolerance`**
   property that tunes that algorithm to fix accessibility/visual-order mismatches.

Ships **today in Safari 26.4**; behind a flag in other browsers.

---

## 1. The problem: masonry, and why Grid/Flex fall short

The target pattern is masonry — a waterfall where content flows down the page in
columns and each item settles beneath the last; flip the axis and it's a brick wall
flowing across in rows. Historically you reached for a JS library, or improvised
with floats or Flexbox "that almost works — until it doesn't."

Why the existing modes don't cut it:

- **Flexbox** gives one axis and a single lane of items that wrap and keep flowing
  in the chosen direction (row or column).
- **Grid** gives two axes and places items into cells at the intersection of tracks.
  With **mixed aspect ratios** you get large empty areas where short items don't fill
  their cell. For images that leaves three bad options: **stretch** (distorts),
  **zoom** (overflows the container), or **crop** (loses information).

## 2. What Grid Lanes is

It sits between Grid and Flex: structures just one dimension and leaves the other
free. Unlike Flex (single lane wrapping down the page), Grid Lanes **distributes
content across multiple lanes**. The result is a tightly packed, staggered layout
that preserves each item's natural proportions.

**Placement rule:** items are placed one by one, and each one lands in whichever
column leaves it **closest to the top**. That's why earlier items sit higher and
later items fill in below. It works with any content — images, text blocks (the
browser sizes their heights), headlines spanned across columns, mixed designs.

## 3. Build a container (three lines)

```css
.container {
  display: grid-lanes;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}
```

- `display: grid-lanes` — the new layout mode.
- `grid-template-columns` sets the number of tracks and each track's width. `fr`
  ("fractional unit") splits the container's available space into fractions, so
  `repeat(3, 1fr)` is three equal columns.
- `gap` spaces items apart, exactly like Grid.

## 4. Brick-wall variation

Flip the layout 90° by swapping the column axis for the row axis:

```css
.container {
  display: grid-lanes;
  grid-template-rows: repeat(3, 1fr);
  gap: 10px;
}
```

The catch: **you pick one direction, not both.** Columns give you a waterfall; rows
give you a brick wall.

## 5. Track sizing — the full Grid vocabulary works

```css
/* Unequal columns: wide center */
grid-template-columns: 1fr 2fr 1fr;

/* Let the browser decide how many columns fit */
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));

/* Repeating narrow/wide pattern */
grid-template-columns: repeat(auto-fill, minmax(8rem, 1fr) minmax(14rem, 2fr));
```

`auto-fill` creates as many columns as will fit; `minmax()` sets a floor each column
can grow past to fill space. (The Code-tab extraction of the last example is missing
a closing paren — see `code.md`.)

## 6. Controlling individual items

Grid Lanes reuses Grid's item-placement properties:

```css
/* Stretch across two columns; the rest of the layout reflows around it */
.item { grid-column: span 2; }

/* Place explicitly: start in column 2, span columns 2 and 3 */
.item { grid-column: 2 / span 2; }
```

**Key constraint:** you control **column placement, but not row** — Grid Lanes always
decides the row for you.

### Subgrid for nested content

A card that spans two columns can let its own children join the parent's tracks:

```css
.item {
  display: grid-lanes;
  grid-template-columns: subgrid;
  grid-column: span 2;
}
```

In the recipe-card demo, the card's image and text don't participate in the parent
layout until `subgrid` is added — then the image takes one column and the text the
other, each sized to its content. Nesting works both ways: a regular `display: grid`
with `subgrid` inside a Grid Lanes container, or Grid Lanes inside Grid.

## 7. Flow tolerance

The default rule — "place the next item in the shortest column" — usually looks
great, but can break **visual vs. DOM order**. When two columns are nearly the same
height, the slightly shorter one wins the next item, which can make rows read
left-to-right then right-to-left. That mismatch between **tab order and visual order**
hurts accessibility and confuses keyboard users.

`flow-tolerance` is the dial that loosens that rule:

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  flow-tolerance: normal;   /* default resolves to 1em */
}
```

With tolerance on, for each new item the browser asks: *is the taller column less
than the shorter column **plus** flow-tolerance?* If yes, the item fills the earlier
column anyway (preferring DOM-forward order); if the gap exceeds tolerance, it drops
into the genuinely shorter column. You can also set an explicit length:

```css
flow-tolerance: 2.1em;
```

Default is **`1em`**; tune it to your content.

## 8. Debugging with Web Inspector

Safari's Web Inspector has full Grid Lanes support. Turn on the overlay to get:

- column and row lines,
- **order numbers** projected over each item (so you can see exactly how placement
  resolved),
- the gaps drawn between items.

Useful when tuning `flow-tolerance` or diagnosing a surprising placement.

---

## Availability & next steps

- **Safari 26.4** today; behind a flag in other browsers.
- WebKit team's **Grid Lanes Field Guide** — interactive demos covering every
  property in the talk.
- Related: **"What's new in WebKit for Safari 27."**
