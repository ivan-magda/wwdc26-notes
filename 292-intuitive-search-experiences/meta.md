# Session 292 — Design intuitive search experiences

- **URL:** https://developer.apple.com/videos/play/wwdc2026/292/
- **Duration:** 16m
- **Speakers:** Rob (Apple Design team)

## Description

How to design an intuitive search experience for your app. Covers the Search Field
component and its core elements/interactions, the full range of search placement
patterns across iOS, iPadOS, and macOS (and how to choose between them), and best
practices for recent searches, predictive suggestions, filtering (scope bars, tokens),
and graceful empty states. Framed around the Liquid Glass design system, which
introduced new patterns to make search more ergonomic on iOS and to take advantage of
the larger iPad and Mac displays.

## Key topics

- **Search Field anatomy** — leading search icon, placeholder text, clear button, and (iOS, when focused) a Cancel button; auto-adopts glass styling in a toolbar vs. standard content styling in the scroll region.
- **iOS placement** — bottom toolbar (field or button), top toolbar, tab bar (search tab), or inline field; placement drives where the field animates when active (over the keyboard from the bottom; stays at top when inline).
- **Search Tab choices** — standard tab (lands on a page with content/suggestions before searching, e.g. Apple TV) vs. prominent button tab (taps straight into the keyboard, e.g. Phone).
- **Scoped vs. global search** — inline per-tab search (Apple Music library) reinforces a narrow scope; a dedicated Search Tab signals global/unified search.
- **iPad & Mac placement** — trailing toolbar position (split-view apps, Mail/Notes/Files; results in detail view, Freeform), top of sidebar (Settings, Stocks), or dedicated search tab/section (Apple Music). Keep iPad and Mac aligned.
- **Adaptive sizing** — field width adapts to adjacent toolbar items; scales or collapses into a button, expands for text input on activation, overflow items move into a menu.
- **Best practices** — recent searches (inline on iOS, menu on iPad/Mac toolbar/sidebar, alongside content on a Search Tab; swipe-to-remove + clear-all); predictive suggestions (distinguish typed vs. predicted text, limit count); filtering via scope bar, contextual filters (Maps), and search tokens (Photos); graceful empty state via content unavailable view.

## Related sessions to fetch (referenced in this talk)

- [ ] Human Interface Guidelines — Searching (resource, not a session)
- [ ] Apple design resources (resource)
- [ ] Prior-year design-system talks (Rob points to "these talks from previous years" — IDs not named on screen)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Search as a core tool for finding, navigating, and discovering content; first thing people reach for; Liquid Glass introduces new ergonomic search patterns. Agenda: search field, patterns/placement, best practices.
- **1:39 Search field** — Core elements: leading search icon, placeholder text, clear button, iOS Cancel button on focus. Auto-styling (glass in toolbar, content style in scroll region). Keep core elements intact even with custom branding; custom icons should resemble the symbols they replace.
- **2:52 Patterns and placement** — Full range of placements across iOS (toolbar field/button, tab, inline), iPad, and Mac (trailing toolbar, sidebar top, dedicated tab/section). Two questions: how do people navigate the app, and what is the scope of search. Worked through Mail, Stocks, Apple TV, Phone, Apple Music, Notes, Files, Freeform, Settings.
- **10:30 Best practices** — Recent searches; predictive suggestions; filtering with scope bars, contextual filters (Maps), and search tokens (Photos); failing gracefully with the content unavailable view.
- **15:20 Next steps** — Audit your app for reachability (move search to bottom), Search Tab opportunities in tabbed apps, and suggestion/filter usage. Pointers to HIG and design resources.

## Code

See `code.md` — design session, no Code tab. Concrete APIs named in the talk are listed there.
