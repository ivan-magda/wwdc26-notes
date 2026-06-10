# Session 315 — Rediscover the HTML select element

- **URL:** https://developer.apple.com/videos/play/wwdc2026/315/
- **Duration:** 9m
- **Speakers:** Tim (Safari Engineer)

## Description

Introducing Customizable Select — a way to fully style the HTML `<select>` element
with CSS while keeping its built-in accessibility. Available in Safari 27 and Chrome
135. The session builds a "Sort by" menu and a category picker for a photographer's
portfolio site, going from the native pull-down button all the way to a grid-based
drop-down with SVG icons inside both the options and the selected button.

## Key topics

- `appearance: base-select` — the opt-in that switches the select from native styling to the customizable model
- New pseudo-elements: `::picker-icon` (the dropdown arrow), `::picker(select)` (the menu itself), `::checkmark` (the selected-option check)
- New `:open` pseudo-class for styling the button while the menu is showing; `:checked` for the active option
- Rich content inside `<option>` — SVGs, images, video, emoji — plus an empty `alt=""` trick to avoid screen-reader double-announcing
- Custom drop-down layouts via standard CSS (`display: grid`, `grid-template`, `gap`)
- Replacing the built-in button: a `<button>` as the first child of `<select>`, with `<selectedcontent>` mirroring the chosen option's rich content
- Progressive enhancement — unsupporting browsers fall back to the native popup automatically, accessibility preserved either way

## Related sessions to fetch (referenced in this talk)

- [ ] Learn CSS Grid Lanes (Brandon — used for the photo gallery layout)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Customizable Select fully styles `<select>` in CSS while keeping built-in accessibility; Safari 27 + Chrome 135. Demo: a photographer's portfolio ("Sort by" menu + category picker).
- **2:32 Style the select button** — `appearance: base-select` opts into the new model; customize with familiar CSS (font, background, border, padding). `::picker-icon` swaps the arrow; `:open` changes colors while the menu shows.
- **3:47 Customize the drop-down** — apply `appearance: base-select` to `::picker(select)` to style the menu; adjust spacing/borders/box-shadow; emphasize the active option with `:checked`; replace the check with `::checkmark`.
- **5:00 Go beyond text options** — put rich content (SVG, images, labels) directly inside `<option>`; break the default vertical list with a grid (`grid-template`, `gap`) while preserving accessibility, including empty `alt` so screen readers don't double-announce.
- **6:50 The selectedcontent element** — replace the built-in select button with a `<button>` as the first child of `<select>`, and use `<selectedcontent>` inside it to mirror the currently selected option's rich content (icon + label) in the button.
- **7:46 Fallback for unsupported browsers** — browsers without customizable select fall back to the native popup automatically; progressive enhancement works because it's still a semantic `<select>`. Accessibility preserved either way.
- **8:49 Next steps** — try the demo on webkit.org; experiment in your own projects; test against assistive tools and non-supporting browsers. For the photo layout, see "Learn CSS Grid Lanes."

## Code

See `code.md` — snippets extracted from the Code tab.
