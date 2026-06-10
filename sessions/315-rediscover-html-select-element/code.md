# Code samples — Session 315

Extracted from the Code tab. Timestamps map to the talk's chapters.

## Baseline: select with its label (HTML)

```html
<label for="sort-select">Sort by</label>
<select id="sort-select">
    <option>Newest</option>
    <option>Oldest</option>
</select>
```

## Opt into customizable select (inherit body font)

```css
body {
    font-family: Gill Sans, sans-serif;
}

select {
    appearance: base-select;
}
```

## Style the button — background, border, padding

```css
select {
    appearance: base-select;
    background-color: var(--green-10);
    border: none;
    padding: 0.6em 1em;
}
```

## :open state colors

```css
select:open {
    background-color: var(--green-100);
    color: white;
}
```

## Swap the dropdown arrow with ::picker-icon (open state)

```css
select:open {
    background-color: var(--green-100);
    color: white;
}

select:open::picker-icon {
    content: url(icons/arrow-white.svg);
}
```

## Style the drop-down menu — ::picker(select)

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

## Emphasize the selected option, gray out the rest

```css
option:checked {
    font-weight: 600;
}

option:not(:checked) {
    color: #777;
}
```

## Replace the default checkmark — ::checkmark

```css
option::checkmark {
    content: url(checkmark.svg);
    width: 0.65em;
}
```

## Rich content inside an option (SVG + label, empty alt)

```html
<option value="flower">
    <img src="flowers.svg" alt="">
    <span class="text">Flowers</span>
</option>
```

## Hide the checkmark and highlight the checked option instead

```css
option::checkmark {
    display: none;
}

option:checked {
    background: #00857e;
    color: white;
}
```

## Grid layout for the drop-down

```css
::picker(select) {
    display: grid;
    grid-template:
       1fr 1fr / 1fr 1fr 1fr;
    gap: 1rem;
}
```

## Full category picker markup (SVG icons per option)

```html
<select>
    <option value="anywhere">
        <img src="icons/all.svg" alt="">
        <span class="text">Everything</span>
    </option>
    <option value="buildings">
        <img src="icons/buildings.svg" alt="">
        <span class="text">Buildings</span>
    </option>
    <option value="flowers">
        <img src="icons/flower.svg" alt="">
        <span class="text">Flowers</span>
    </option>
</select>
```

## Replace the built-in button with a custom one + <selectedcontent>

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

---

## API surface surfaced by the code

- **Opt-in:** `appearance: base-select` on `<select>` and on the `::picker(select)` pseudo-element.
- **Pseudo-elements:** `::picker-icon` (arrow), `::picker(select)` (menu), `::checkmark` (option check).
- **Pseudo-classes:** `:open` (menu showing), `:checked` / `:not(:checked)` (option state).
- **New HTML:** a `<button>` may now be the first child of `<select>`; `<selectedcontent>` mirrors the selected option's rich content into that button.
- **Accessibility:** empty `alt=""` on an option's image avoids double-announcing the adjacent text label.
