# Code samples — Session 314

Extracted from the Code tab. The talk builds each example incrementally, so several
adjacent snippets are progressions of the same block; duplicates are collapsed below.
All snippets are CSS.

## Minimal container

```css
.container {
  display: grid-lanes;
}
```

## Three equal columns

```css
.container {
  display: grid-lanes;
  grid-template-columns: repeat(3, 1fr);
}
```

## Three columns with a gap

```css
.container {
  display: grid-lanes;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}
```

## Brick-wall variation (rows instead of columns)

```css
.container {
  display: grid-lanes;
  grid-template-rows: repeat(3, 1fr);
  gap: 10px;
}
```

## Explicit equal columns

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 10px;
}
```

## Unequal columns (wide center)

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 2fr 1fr;
  gap: 10px;
}
```

## Let the browser choose the column count (auto-fill + minmax)

```css
.container {
  display: grid-lanes;
  grid-template-columns:
    repeat(auto-fill,
      minmax(200px, 1fr));
  gap: 10px;
}
```

## Repeating narrow/wide pattern with auto-fill

```css
/* As shown on the slide. NOTE: the extracted source is missing a closing
   paren after the second minmax() — corrected form would be:
   repeat(auto-fill, minmax(8rem, 1fr) minmax(14rem, 2fr)) */
.container {
  display: grid-lanes;
  grid-template-columns:
    repeat(auto-fill,
      minmax(8rem, 1fr)
      minmax(14rem, 2fr);
  gap: 10px;
}
```

## Span an item across columns

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 10px;
}

.item {
  grid-column: span 2;
}
```

## Place an item explicitly (column only; row is chosen for you)

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 10px;
}

.item {
  grid-column: 2 / span 2;
}
```

## Subgrid: nested content joins the parent layout

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 10px;
}

.item {
  display: grid-lanes;
  grid-template-columns: subgrid;
  grid-column: span 2;
}
```

A regular `display: grid` can also use `subgrid` inside a Grid Lanes container (and
vice versa) — the talk shows both nestings:

```css
.item {
  display: grid;
  grid-template-columns: subgrid;
  grid-column: span 2;
}
```

## Flow tolerance — default keyword

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  flow-tolerance: normal;
}
```

## Flow tolerance — explicit length

```css
.container {
  display: grid-lanes;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  flow-tolerance: 2.1em;
}
```

---

## Useful facts surfaced by the code

- New display value: **`display: grid-lanes`**.
- Structure exactly one axis: `grid-template-columns` (waterfall) **or**
  `grid-template-rows` (brick wall), never both.
- All familiar Grid track sizing works: `fr`, `repeat()`, `auto-fill`, `minmax()`,
  fixed lengths.
- Item placement uses existing Grid properties — `grid-column: span N`,
  `grid-column: <start> / span N` — but **only the column (lane-cross) axis** is
  controllable; Grid Lanes always picks the row.
- `subgrid` is supported, so nested cards can align their contents with the parent
  tracks.
- New property: **`flow-tolerance`** — accepts the keyword `normal` (the spoken
  default resolves to `1em`) or an explicit length (e.g. `2.1em`); it loosens the
  shortest-column placement rule.
