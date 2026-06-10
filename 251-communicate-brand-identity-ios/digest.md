---
title: "Communicate your brand identity on iOS — Full Digest"
session: WWDC26 · 251
url: https://developer.apple.com/videos/play/wwdc2026/251/
duration: 18m
speakers: Sarah (Design Evangelist)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 251 — Communicate your brand identity on iOS

## TL;DR

How to express brand on iOS without breaking the platform. The governing idea, made
concrete by Liquid Glass in iOS 26: your app is **two layers** — a **UI layer**
(navigation and actions, kept native and familiar) and a **content layer** (your
canvas, where brand belongs). Sarah walks five areas, each with a "keep it native vs.
make it yours" rule of thumb:

1. **Components** — standard for utilitarian/navigation tasks; customize only the
   high-impact, content-defining pieces.
2. **Content** — full-bleed imagery/video, immersive color, voice & tone, and motion
   are where brand lives.
3. **Color** — move brand color into the content/scroll area (Liquid Glass tints
   dynamically); use accent/tint for *meaning*; restraint + Dark Mode.
4. **Typography** — custom fonts must support Dynamic Type and reflow; the SF family
   alone can feel distinct.
5. **Iconography** — custom is fine but keep it simple, scalable, and platform-honest;
   SF Symbols is the free default; minimize logos.

Recurring exemplar apps: **Crumbl, Moonlitt, NYT Cooking, Gentler Streak, Slack**.

The throughline: brand should be **of service to the experience**, integrated in ways
that are "subtle but meaningful." Forcing brand over system conventions compromises
usability.

---

## 1. Where brand belongs — the two-layer model

Branding is partly **aesthetic** (typography, color, iconography) and partly a
**feeling** (voice/tone, motion). The temptation is to make the brand identical across
web, retail, marketing, and the iOS app — but each placement should respect its
context. iPhone users expect apps to look and feel like iOS, and most have no exposure
to your app on other platforms, so the iOS app shouldn't default to matching them.

Liquid Glass (iOS 26) reframes the interface as two layers:

- **UI layer** — global navigation and actions, expressed through tab bars and top
  toolbars that float above the content for easy access. **Lean on the familiar; don't
  reinvent the wheel.**
- **Content layer** — sits beneath the controls, holds everything that makes the app
  unique. **The best opportunity to express brand identity.**

## 2. Components — native by default, custom where it counts

- **Keep navigation native.** *Gentler Streak* has a distinct identity (playful
  illustrations, rich data viz) but its tab bar and top-toolbar actions are all
  standard. Standard components (grid views, grouped tables) are flexible and
  functional, and people instinctively know how to use them.
- **Customize selectively.** *Slack* builds a custom top toolbar (middle action shows
  channel info) but button sizes, floating-action placement, and popover behavior all
  feel iOS. *Moonlitt* (lunar-cycle tracker) uses a flat hierarchy with no tab bar and
  a **custom lunar-calendar component** — but on a **Liquid Glass backing**, with a
  primary dismiss action and a sheet with **concentric edges that match the hardware**.
  Unique, yet clearly belongs on iOS.
- **Spend custom effort where it has impact**, then audit for places to swap in standard
  components for functional tasks.
- **Context menus** are an underused standard component: shown from a top-toolbar
  button, scoped to the whole screen (vs. inline), flexible (icons, grouped sections
  with optional headers, secondary menus/modals), and **animated out of the box in
  SwiftUI** (Moonlitt's settings menu morphs from the tapped action). Rebuilding such
  utilitarian elements rarely reinforces brand and often makes an app feel dated or
  non-native.

## 3. Content — the canvas

The content layer is where brand expression lives. It can include imagery, video,
words, and motion:

- **Imagery / video** — *Crumbl* uses **full-bleed videos** of its weekly flavors;
  because they change weekly they have a clear purpose and deepen the product
  connection.
- **Immersive color** — *Moonlitt* goes edge-to-edge, mimicking night-sky gradation,
  with **3D elements** showing your position relative to the moon. Content taking over
  the whole interface fits its purpose.
- **Words / voice & tone** — copy shapes feeling (playful, trustworthy, etc.). Deep
  dive: **"Add personality to your app through UX writing."**
- **Motion** — apps are dynamic, not static screens. *NYT Cooking* uses **SwiftUI Zoom
  Transitions** for recipe comments (delightful *and* functional — connects tap target
  to transition state). *Gentler Streak* uses **spring animations** on its monthly
  recap so content pops. Motion emphasizes hierarchy — but **dropped frames / slow loads
  hurt perception** even when users can't name why.

## 4. Color — move it into the content, give it meaning

- Before iOS 26, apps put solid backgrounds on top toolbars and tab bars, which were
  bulky and **letterboxed** the content into a smaller area.
- New recommendation: **move brand color into the content area / scroll view**, so
  Liquid Glass controls sit above it and **pick up the brand color dynamically**.
- Use color to **create meaning** — hierarchy, groupings, interaction — not decoration.
  This is your **accent / tint color**, usually on controls and actions. *Slack* uses
  tint sparingly: new-info sections, unread badges, new-message creation, selected tab.
  It also scrolls its former solid toolbar color away so content spreads edge to edge.
- **Restraint:** too much color is distracting/overwhelming; use it intentionally for
  maximum impact. But don't be afraid of it — it aids communication, evokes brand, and
  provides visual continuity.
- **Personal device → support Dark Mode.** Gentler Streak, Slack, and NYT Cooking all
  have refined low-light palettes. No Dark Mode → negative experience that reflects on
  the product.
- **Extend brand beyond the app:** *Crumbl* **Widgets** carry the pastel palette and
  distinct imagery — recognizable at a glance.

## 5. Typography — custom is great, but it must scale

- Typography can be expressive/bold/elegant but should always be **functional**.
- *Crumbl* built its own typeface, **Crumbl Sans**, used in marketing and in memorable
  app moments (large flavor headers).
- The main caveat with custom fonts on iOS is **Dynamic Type** (the Accessibility
  setting that scales system text). It's built into Apple's system fonts; with custom
  fonts you must **implement and test** it. *Crumbl* stays legible at larger sizes by
  **reflowing to multiple lines rather than truncating**. Dynamic Type also enlarges
  standard tab labels/icons at accessibility sizes.
- **You may not need a custom font at all.** **San Francisco** is the system font for
  all Apple platforms (150+ languages): **SF Pro** (default), **SF Compact** (small
  sizes), **SF Mono** (code/column alignment), **New York** (serif reading + display).
  *Gentler Streak* uses system fonts **entirely**, mixing widths and variants like
  **SF Rounded** to feel distinct.

## 6. Iconography — simple, scalable, platform-honest

- You can use custom iconography almost anywhere — content views and controls.
  *NYT Cooking*'s icons have sharper edges and a line-weight variant; they're unique but
  not overly detailed, so they scale to small sizes (tab bar, toolbars, inline actions).
- **Honor platform conventions.** NYT Cooking's **Share** icon differs across iOS,
  Android, and Web — each stays true to its platform's sharing pattern, despite a shared
  house style. Icons should be identifiable and purposeful, not heavily stylized.
- **SF Symbols** is the free default: a Mac app with **7,000+ symbols**, font-like
  dynamic scaling, multiple line weights, accessibility + localization support, and
  **built into Xcode** (no exporting/handoff for design teams). Neutral by design to fit
  any app.
- **Logos:** in-app, people already know which app they're in. Logos eat real estate
  better used for content. *NYT Cooking* shows its logo only on the Home tab and **fades
  it on scroll** — understated, refined, unobtrusive.

## Wrap-up

Every example brands in a way that **complements** the experience rather than distracts
from it: predictable navigation people understand instantly, identity woven in subtly.
iOS has established interactions; forcing brand over them compromises UX. Be mindful of
where brand oversteps system behavior or confuses familiar conventions.
