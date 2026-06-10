---
title: "Design intuitive search experiences — Full Digest"
session: WWDC26 · 292
url: https://developer.apple.com/videos/play/wwdc2026/292/
duration: 16m
speakers: Rob (Apple Design team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 292 — Design intuitive search experiences

## TL;DR

A designer-facing guide to where search should live in your app and how it should
behave, reframed around the **Liquid Glass** design system. Three headline themes:

1. **The search field is a system component, not a thing you reinvent.** It ships with
   a leading magnifying-glass icon, placeholder text, a clear button, and (on iOS, when
   focused) a Cancel button — and it auto-adopts the right styling (glass in a toolbar,
   content styling in the scroll region) based on where you put it. Keep those core
   elements intact even with custom branding.

2. **Placement is a design decision driven by two questions:** *how do people navigate
   my app?* (do you need to accommodate a tab bar?) and *what is the scope of my
   search?* (placement changes users' perception of what they're searching). Liquid
   Glass makes bottom-of-screen search ergonomic on iOS — the field animates up over
   the keyboard for reachability.

3. **Best practices make search feel effortless:** recent searches (so people don't
   even type), predictive suggestions (so they don't finish typing), filtering (scope
   bars, contextual filters, search tokens), and a graceful empty state.

The talk walks every recommendation through real Apple apps: Mail, Stocks, Apple TV,
Phone, Apple Music, Notes, Files, Freeform, Settings, Maps, Photos.

---

## 1. The Search Field component (1:39)

Apple provides a standard search component with the elements people expect:

- **Leading search icon** — visually establishes the UI as a search field.
- **Placeholder text** — communicates where to enter the search term.
- **Clear button** — appears once text is entered.
- **Cancel button (iOS only)** — appears when search is focused; exits search and
  dismisses the keyboard.

It **automatically adopts the correct presentation style** for its location:

- **Glass** when placed in a toolbar.
- **Standard content styling** when placed in the scroll region of the app.

**Branding guidance:** if your app has distinct iconography, keep the core search-field
elements intact. Custom icons should closely resemble the symbols they replace — the
magnifying glass is "universally recognizable as search," so don't trade it away for
recognizability.

## 2. Patterns and placement (2:52)

> Two questions to ask before choosing: **(1)** How are people navigating my app? (e.g.
> do I need to accommodate a tab bar?) **(2)** What is the scope of my search? Placement
> shapes the user's perception of what content they're searching.

### iOS placements

Search can live as:

- a **field or button in the toolbar** (bottom or top),
- a **tab** in the tab bar, or
- an **inline field** under the top toolbar or in the content area.

**Placement drives the active-state animation:**

- **Bottom toolbar** → the field animates up over the keyboard, optimizing
  reachability and input. *Preferred.*
- **Inline field** → stays at the top when active, avoiding bottom-of-screen UI.

**Worked examples:**

- **Mail (bottom toolbar)** — navigation happens through the mail list with contextual
  toolbars; bottom placement is ergonomic, adjacent to primary actions, and makes it
  clear you're searching your mail. The field width auto-adapts to the number of
  adjacent toolbar items (leading + trailing action). With more than two other toolbar
  items, search can **start as a button** and animate into a field when tapped.
- **Stocks (top toolbar)** — the bottom is occupied by a sheet, precluding a bottom
  toolbar/field, so search goes in the top toolbar; it still pulls up over the keyboard
  when tapped.

### Tabbed apps and the Search Tab

For tabbed apps with varied rich content, create a **primary entry point for search** —
a single place where people expect to find all relevant content. Two Search Tab styles:

- **Standard tab** — lands on a page with the search field at the top, leaving room for
  content/suggestions before searching. Good when the app has breadth and people are in
  an exploratory mindset. *Example: Apple TV* surfaces genres/categories first to
  ground people in what's available.
- **Prominent / button tab** — conveys that tapping immediately engages search and
  brings up the keyboard. Good when people know what they want and need quick access.
  *Example: Phone* — tap search, keyboard up, get back to a recent call or contact;
  search is never more than a tap away.

### Scoped (per-tab) search

Sometimes searching just one tab is the right call. *Example: Apple Music library* —
search is inline with the content, under the title; the title plus descriptive
placeholder text reinforce that you're searching only the albums in your library, not
the whole app. Especially useful when the app has **more than one search field** and
**location is critical to scope.**

### iPad & Mac

Wider displays and shared navigation models → approach search similarly on both, and
keep the two experiences aligned. Primary search field placements:

- **Trailing toolbar position** — for split-view apps searching across multiple columns
  (Mail, Notes, Files). Lets people navigate results while keeping selected content
  visible in the detail view; the most common, familiar pattern. Also use it when
  results should appear in the detail view (Freeform: search directly filters the
  boards). With multiple toolbar items/groups, the field **scales or collapses into a
  button** by available space; on activation it **expands to a text-input width** and
  moves overflow items into a menu.
- **Top of the sidebar** — when filtering content/navigation that lives in the sidebar
  (Settings). Useful with a rich detail view to draw a clean line between the list being
  searched and an adjacent view (Stocks: search finds/adds symbols to the list; sidebar
  placement clarifies scope — placing it over Top Stories would imply searching news).
- **Dedicated search tab/section** — for rich, multi-section apps (Apple Music) that
  want one place to search everything; the immersive approach also gives a larger canvas
  to express results.

## 3. Best practices (10:30)

### Recent searches

People often return to something they searched before — showing recent searches lets
them get back without typing.

- **iOS:** show inline when the field becomes focused.
- **iPad/Mac:** in a menu if the field is in the toolbar or sidebar; alongside other
  content suggestions on a Search Tab page.
- Be **selective** — sometimes only surface results the user actually viewed/engaged
  with. Think about what's most helpful in *your* app.
- Let people **remove** recents: swipe gesture on individual items, plus a clear-all
  button in the section header.

### Predictive suggestions

Once typing starts, show relevant results fast, with predictive suggestions that reduce
typing.

- Suggestions should **directly correspond** to what's typed and feel like a natural
  completion.
- **Visually distinguish user input from the predicted part** to keep people oriented.
- **Limit the count** so results stay front and center.
- With good ranking, people generally shouldn't have to type the whole query.

### Filtering

Start broad, then let users narrow. Tools:

- **Scope bar** — lightweight filtering control. *Mail:* switch between all mailboxes
  vs. the current mailbox; reinforces where you're searching.
- **Contextual filters** — for apps spanning many categories, offer a richer set, but
  only show filters relevant/contextual to what the person is looking for to avoid
  overwhelm. *Maps:* filters tailored to location types from restaurants to hiking
  trails.
- **Search tokens** — keyword filters that surface as you type and appear as highlighted
  text inside the field; users keep adding to the search. They allow more natural
  language and can be **combined** (*Photos:* "Joshua Tree" + "2021"). Caveat: tokens
  are **less discoverable**, so don't use them to replace visible filtering UI — pair
  them with a scope bar or other filter controls.

### Failing gracefully

When a search returns nothing, show a **well-considered empty state** rather than a
blank view (which makes people wonder if the search even ran). Apple provides a
**content unavailable view** that, configured for search, shows a search symbol, title,
and subtitle. Consider echoing the **current search text** so people can catch typos.

## 4. Next steps (15:20)

Audit prompts the talk leaves you with:

- Are there places where search could move to the **bottom** to improve reachability?
- Does a tabbed app **benefit from a Search Tab**?
- Are you using **suggestions and filters** to make search effortless?

Pointers: the **Human Interface Guidelines**, Apple **design resources**, and prior-year
design-system talks.
