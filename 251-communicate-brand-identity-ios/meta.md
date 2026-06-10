# Session 251 — Communicate your brand identity on iOS

- **URL:** https://developer.apple.com/videos/play/wwdc2026/251/
- **Duration:** 18m
- **Speakers:** Sarah (Design Evangelist)
- **Type:** Design session (no Code tab)

## Description

A design talk on striking the balance between expressing your brand identity and
preserving the familiar paradigms of iOS. With Liquid Glass in iOS 26, think of your
app as two layers: a UI layer (navigation/actions, kept native and familiar) and a
content layer (your canvas for brand expression). The session walks through five areas
— components, content, color, typography, and iconography — using real apps (Crumbl,
Moonlitt, NYT Cooking, Gentler Streak, Slack) that brand well without compromising the
native feel.

## Key topics

- **Two layers:** UI layer (global navigation, native components) vs. content layer
  (the best place to express brand). Liquid Glass formalizes this split.
- **Components:** lean on standard components (grids, grouped tables, context menus,
  tab bars, top toolbars); customize only high-impact areas. Custom utilitarian
  components can look dated/non-native.
- **Content as canvas:** full-bleed imagery/video (Crumbl weekly flavors), immersive
  edge-to-edge color (Moonlitt), voice & tone in copy, and motion (SwiftUI Zoom
  Transitions, spring animations) as part of the brand experience.
- **Color:** move brand color into the content/scroll area so Liquid Glass picks it up
  dynamically; use accent/tint color with meaning (status, feedback, selection); use
  sparingly; support Dark Mode.
- **Typography:** custom fonts (Crumbl Sans) must support Dynamic Type and reflow, not
  truncate. SF family (SF Pro, SF Compact, SF Mono, New York, SF Rounded) can deliver a
  distinct voice with no custom font at all (Gentler Streak).
- **Iconography:** custom icons fine anywhere (NYT Cooking) but keep them simple and
  scalable; honor platform conventions (Share icon differs iOS/Android/Web); SF Symbols
  (7,000+, free, font-like scaling, in Xcode) as the default. Minimize logos in-app.

## Related sessions to fetch (referenced in this talk)

- [ ] Add personality to your app through UX writing

## Chapter summary (Summary tab)

- **0:00 Intro** — balance brand expression with familiar iOS paradigms; brand is both
  aesthetic (typography, color, iconography) and a feeling (voice/tone, motion). Don't
  default to making the brand identical across web/retail/other platforms — respect the
  iOS context. Five areas to cover: components, content, color, typography, iconography.
- **2:24 Components** — Liquid Glass = two layers (UI vs. content). Keep navigation
  native (Gentler Streak); customize high-impact components only (Slack toolbar,
  Moonlitt lunar calendar with Liquid Glass backing + concentric edges); standard
  components like context menus come fully animated out of the box in SwiftUI.
- **6:41 Content** — content layer is the canvas: full-bleed video (Crumbl), immersive
  3D/color (Moonlitt), words/voice & tone, and motion (NYT Cooking Zoom Transitions,
  Gentler Streak spring animations). Watch perf — dropped frames hurt perception.
- **11:49 Color** — move brand color into the scroll/content area so Liquid Glass tints
  dynamically; use accent/tint for meaning (Slack); exercise restraint; support Dark
  Mode; extend brand to Widgets (Crumbl).
- **11:50 Typography** — custom typefaces (Crumbl Sans) must support Dynamic Type and
  reflow gracefully. SF system fonts + variants (Pro/Compact/Mono/New York/Rounded) can
  feel distinct without a custom font (Gentler Streak).
- **14:17 Iconography** — custom icons can live anywhere (NYT Cooking) but stay simple
  and scalable and respect platform conventions (Share icon). SF Symbols: 7,000+, free,
  font-like, in Xcode. Minimize in-app logos (NYT logo fades on scroll).

> Note: the Summary tab lists Color at 11:49 and Typography at 11:50, which is almost
> certainly a typo in Apple's markers — Typography actually starts around 11:49 in the
> transcript and Color around 09:17. See `digest.md` Open Questions.

## Code

No Code tab for this session — it's a design talk. See `code.md` for the spoken
APIs/technologies (SwiftUI Zoom Transitions, Dynamic Type, SF Symbols, Liquid Glass).
