---
title: "Rediscover the HTML select element — Full Digest"
session: WWDC26 · 315
url: https://developer.apple.com/videos/play/wwdc2026/315/
duration: 9m
speakers: Tim (Safari Engineer)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 315 — Rediscover the HTML select element

## TL;DR

For years, going beyond the default `<select>` meant heavy JavaScript libraries or a
pile of `<div>`s — and re-implementing accessibility by hand. **Customizable Select**
changes that: you keep the real, semantic `<select>` element (keyboard nav, screen
reader support, native fallback) and style every part of it with plain CSS.

Three headline ideas:

1. **One opt-in unlocks it all** — `appearance: base-select`. Apply it to `<select>`
   to style the button, and to `::picker(select)` to style the drop-down menu.
2. **New styleable parts** — pseudo-elements `::picker-icon`, `::picker(select)`,
   `::checkmark`, and pseudo-classes `:open` and `:checked`. Options can hold *rich
   content* (SVG, images, video, emoji), and the menu can use any layout, including
   `display: grid`.
3. **No accessibility tax** — it's still a semantic `<select>`, so unsupporting
   browsers fall back to the native popup automatically (progressive enhancement), and
   assistive tech keeps working either way.

Availability: **Safari 27 and Chrome 135.** The demo is a photographer's portfolio
("Sort by" menu + a category picker with icons).

---

## 1. Why this exists

The native `<select>` (a "pull-down button" on Apple platforms) is powerful: basic
accessibility out of the box, keyboard navigation of `<option>` elements, screen-reader
support, no external libraries. Its problem is purely cosmetic — it matches the OS, not
your site, so it can feel out of place in a custom design. Customizable Select keeps the
power and lets you restyle the look.

## 2. Style the select button

Start with the baseline markup and opt in. Because `font-family` was set on `body`, the
select button now inherits the site font once it's in the customizable model:

```css
body {
    font-family: Gill Sans, sans-serif;
}

select {
    appearance: base-select;
}
```

Then style it like any element — background, border, padding:

```css
select {
    appearance: base-select;
    background-color: var(--green-10);
    border: none;
    padding: 0.6em 1em;
}
```

Swap the dropdown arrow with the new `::picker-icon` pseudo-element, and use the `:open`
pseudo-class to restyle the button while the menu is showing:

```css
select:open {
    background-color: var(--green-100);
    color: white;
}

select:open::picker-icon {
    content: url(icons/arrow-white.svg);
}
```

## 3. Customize the drop-down menu

The menu has its own styleable parts: `::picker(select)` for the menu container and
`::checkmark` for the option's check. To start clean you opt the menu out of native
styling, then treat it as a normal box (spacing, border, shadow):

```css
::picker(select) {
    appearance: base-select;
    padding: 4px;
    margin-top: 0.5em;
    border: 1px solid rgba(0,0,0,0.2);
    border-radius: 9px;
    box-shadow: 0 4px 20px rgba(0,0,0,0.2);
}
```

Emphasize the active option and dim the rest with `:checked` / `:not(:checked)`, then
replace the check glyph with `::checkmark`:

```css
option:checked {
    font-weight: 600;
}

option:not(:checked) {
    color: #777;
}

option::checkmark {
    content: url(checkmark.svg);
    width: 0.65em;
}
```

## 4. Go beyond text options

Options can now contain rich content — images, video, emoji, whatever. The category
picker puts an SVG plus a label inside each option. Note the **empty `alt=""`**: the
visible label text already announces the category, so an alt string would make screen
readers say "Flowers" twice.

```html
<option value="flower">
    <img src="flowers.svg" alt="">
    <span class="text">Flowers</span>
</option>
```

With the checkmark removed, highlight the selection a different way:

```css
option::checkmark {
    display: none;
}

option:checked {
    background: #00857e;
    color: white;
}
```

A long vertical list of icon options didn't fit the window, so the menu is laid out as a
grid — the drop-down is just another element you can use full CSS on:

```css
::picker(select) {
    display: grid;
    grid-template:
       1fr 1fr / 1fr 1fr 1fr;
    gap: 1rem;
}
```

## 5. The `<selectedcontent>` element

By default the select's built-in button only renders text, so the chosen option's icon
doesn't appear in the closed button. Customizable Select lets you **replace the built-in
button** by placing a `<button>` as the first child of `<select>` — previously illegal
HTML. Inside it, `<selectedcontent>` mirrors the rich content of the currently selected
option (e.g. the SVG next to the "Everything" label):

```html
<select>
    <button>
        <selectedcontent></selectedcontent>
    </button>
    <option>...</option>
    <option>...</option>
    <option>...</option>
</select>
```

## 6. Fallback & accessibility

Because it's still a semantic `<select>`, browsers that don't support customizable
select fall back to the native popup automatically — progressive enhancement just works,
and the built-in accessibility features come along for free. The guidance: test against
non-supporting browsers and assistive tools; webkit.org has a best-practices blog post.

The talk also notes a radial color picker built entirely with customizable select, and
that the photo gallery itself uses **Grid Lanes** (separate session).
