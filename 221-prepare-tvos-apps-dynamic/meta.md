# Session 221 — Prepare your tvOS apps for Dynamic Type

- **URL:** https://developer.apple.com/videos/play/wwdc2026/221/
- **Duration:** 10m
- **Speakers:** Isis (engineering manager, accessibility team)

## Description

Large Text support is now available system-wide on tvOS 27, so apps can
automatically scale text to match someone's needs and preferences. By supporting
Dynamic Type, your app adapts its layout to larger sizes and can advertise its
accessibility support in its Accessibility Nutrition Labels for tvOS on the App
Store. This session covers how Large Text works on tvOS, how to find the small
parts of an app that need adjusting, and how to adapt layout in response to text
size.

## Key topics

- Large Text / Dynamic Type is new and system-wide on **tvOS 27**; works the same as on iOS — UIKit and SwiftUI scale standard components (Labels, Buttons, navigation tab bars) automatically
- Users enable it in **Settings → Accessibility → Display → Text Size**, choosing from **Large up to Accessibility XXXL**
- Indicate Larger Text support in **Accessibility Nutrition Labels for tvOS** to reach users searching for accessible apps
- Common issues: **fixed font sizes**, **hard-coded width/height constraints** → truncation and clipping; need for extra padding/spacing at larger sizes
- Fixes: replace hard-coded fonts with **semantic text styles** (`.caption`, etc.); replace fixed widths with **`maxWidth: .infinity`** flexible constraints
- UIKit equivalent: `UIFont.preferredFont(forTextStyle:)` + `adjustsFontForContentSizeCategory = true`
- Adaptive layout when scaling alone isn't enough: read `dynamicTypeSize` (SwiftUI) to reduce grid columns via `containerRelativeFrame` (6 → 4) or switch stack axis via `AnyLayout` (HStack → VStack)
- UIKit adaptive layout: `UIStackView.axis` driven by `preferredContentSizeCategory.isAccessibilityCategory`, updated through `registerForTraitChanges(_:action:)` with `UITraitPreferredContentSizeCategory`
- For very long titles, consider a custom **marquee** strategy

## Related sessions to fetch (referenced in this talk)

- [ ] _None explicitly named._ The talk points to iOS Dynamic Type knowledge as a head start and to Accessibility Nutrition Labels, but does not cite specific other WWDC sessions.

## Chapter summary (Summary tab)

- **0:01 Introduction** — Large Text is new and system-wide on tvOS 27; supporting Dynamic Type lets apps scale text automatically and advertise support in Accessibility Nutrition Labels.
- **2:46 Identify common issues** — avoid fixed font sizes and rigid constraints that block scaling and cause truncation/clipping; keep UI elements flexible rather than hardcoded.
- **6:13 Adapt your layout** — replace hardcoded sizes with standard text styles; when standard scaling isn't enough, adapt layout dynamically (e.g. fewer grid columns) by checking the `dynamicTypeSize` environment value.

(Derived chapter timings from the transcript, matching the Summary tab markers above:)

- **0:07 Overview** — what Large Text is, where users find it (Settings → Accessibility → Display → Text Size, Large → Accessibility XXXL); media app demo scaling automatically.
- **2:47 Identifying issues** — fixed fonts, hard-coded constraints, truncation/clipping; the "Signup information" caption that won't scale; fix with `.caption` style + `maxWidth: .infinity`; UIKit's `adjustsFontForContentSizeCategory`.
- **6:13 Adapting layout** — grid column reduction (6 → 4) via `dynamicTypeSize` + `containerRelativeFrame`; conditional stack with `AnyLayout` (H↔V); UIKit `UIStackView` axis + `registerForTraitChanges`.
- **9:18 Action plan** — use standard text styles, test with Large Text, adapt layouts, indicate support in Nutrition Labels.

## Code

See `code.md` — 6 snippets extracted from the Code tab.
