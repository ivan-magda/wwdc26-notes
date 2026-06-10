# Code samples — Session 251

_No Code tab for this session._ It's a design talk; there are no code snippets in the
supplement.

## Spoken APIs / technologies

Concrete platform features named in the transcript (no code shown, but worth knowing
the API surface behind each):

- **Liquid Glass** (iOS 26) — the design language that splits the app into a UI layer
  (controls floating above) and a content layer beneath. Glass controls pick up content
  color dynamically.
- **SwiftUI Zoom Transitions** — used by NYT Cooking for recipe comments
  (`navigationTransition(.zoom(...))` / `matchedTransitionSource`). Connects the tap
  target to the transition state.
- **Spring animations** (SwiftUI) — Gentler Streak's monthly recap "pop."
- **Context menus** (SwiftUI `.contextMenu` / `Menu`) — standard, flexible component:
  icons on actions, grouped sections with optional headers, secondary menus/modals,
  and a built-in morph animation from the tapped action. Free out of the box.
- **Tab bars & top toolbars** — standard UI-layer components to keep native.
- **Sheets with concentric edges** — Moonlitt's lunar calendar sheet matches the
  hardware corner radius (the iOS 26 concentric/`.containerShape`/corner-radius work).
- **Accent / tint color** — app-level tint applied to controls and actions for status,
  feedback, and selection.
- **Dark Mode** — support low-light color palettes.
- **Dynamic Type** (Accessibility) — system text scaling; built into SF system fonts;
  must be implemented and tested for custom fonts (reflow, don't truncate). Also scales
  standard tab labels/icons at large accessibility sizes.
- **San Francisco system font family** — SF Pro (default), SF Compact (small sizes),
  SF Mono (code/alignment), New York (serif reading/display), SF Rounded. 150+
  languages.
- **SF Symbols** — Mac app + Xcode integration, 7,000+ symbols, font-like dynamic
  scaling, multiple weights, accessibility and localization support, free.
- **Widgets** — WidgetKit surface for extending brand color/imagery beyond the app
  (Crumbl).
