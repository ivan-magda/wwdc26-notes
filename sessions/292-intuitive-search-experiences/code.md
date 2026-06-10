# Code samples — Session 292

_No Code tab for this session._ This is a design talk, so there are no extracted
snippets. The session is conceptual and platform-agnostic about implementation.

## Spoken APIs / named components

The talk references concrete UI components and named Apple apps as examples, but does
not show code. APIs/components named or strongly implied:

- **Search field component** — Apple's standard search control (`UISearchBar` /
  SwiftUI `.searchable(...)`); auto-adopts glass styling in a toolbar vs. standard
  content styling in the scroll region.
- **Search tab** — a tab-bar entry dedicated to search; can be a standard tab (lands
  on a page) or a prominent/button tab (taps straight into the keyboard). Maps to
  SwiftUI `Tab(role: .search)` style search tabs.
- **Scope bar** — lightweight filtering control to switch result scope (Mail: all
  mailboxes vs. current mailbox). Corresponds to `UISearchBar` scope buttons /
  `.searchScopes(...)`.
- **Search tokens** — keyword chips that appear as highlighted text inside the search
  field and can be combined (Photos: "Joshua Tree" + "2021"). Corresponds to
  `UISearchToken` / SwiftUI search token APIs.
- **Content unavailable view** — empty/no-results state showing a search symbol,
  title, and subtitle; should echo the current search text. Corresponds to SwiftUI
  `ContentUnavailableView.search` / `UIContentUnavailableConfiguration`.

> Names mapped to SDK symbols are best-effort inference from the design vocabulary;
> the session itself does not name framework types. Verify exact API names against the
> HIG and framework docs before quoting in a post.
