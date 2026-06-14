---
title: "Accessibility Technologies Group Lab — Full Digest"
session: WWDC26 · 8005
url: https://developer.apple.com/videos/play/wwdc2026/8005/
duration: 01:00:31
sources: transcript.md (whisper.cpp ggml-large-v3, session SD video audio), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and host routing. Some panelist names and one session reference are garbled by whisper and flagged inline.
---

# WWDC26 · 8005 — Accessibility Technologies Group Lab

> A roughly one-hour live developer Q&A with Apple's accessibility team. Not a feature
> talk: it's the practitioner layer under the WWDC26 accessibility sessions, with hands-on
> testing advice, candid API limitations, and the team's own habits as VoiceOver and
> low-vision users. The steady refrain: test VoiceOver first, design accessibility in from
> day one, get feedback from real users, and file feedback in Feedback Assistant.

## TL;DR

- **VoiceOver-first is the team's official triage answer.** Most assistive technologies
  share the same backend APIs (`isAccessibilityElement`, accessibility labels), so a
  strong VoiceOver pass largely carries Switch Control, Voice Control, and Full Keyboard
  Access "for free." Voice Control's own API surface is small; the panel says if Voice
  Control works well, the others usually do too. The extra per-technology API is "polish
  around the edges."
- **To suppress the system's auto image description, drop the image trait** — but that's a
  real tradeoff. Removing the trait also denies the user the new VoiceOver image-description
  features (follow-up questions about the image). A subtle sound plays after your own alt
  text and before the auto-generated description, so users already know which is which.
- **Dynamic Type comes to tvOS in 27**, including a large-text accessibility nutrition
  label for tvOS. The panel repeatedly nominates Dynamic Type as the single best place to
  start an accessibility effort: broad benefit, and it forces the relayout work (Control
  Center controls grow to fit larger text) that you don't want to discover at the end.
- **New this year for developer tooling:** a new XCTest API to drive VoiceOver from a Mac
  against an iOS device (should work in the simulator, per the panel, "to the best of my
  knowledge"); improved **Terminal** accessibility for VoiceOver (visible-beginning
  navigation, marks, tab-autocomplete reading); Xcode VoiceOver bug fixes in seed 1; and
  two new **Xcode AI skills** ("make this view/app accessible for VoiceOver" and one for
  Dynamic Type).
- **iOS 27 streamlines Touch Accommodations setup** with a calibration activity: you tap a
  target (like training eye tracking) and it outputs a recommended configuration instead of
  making you reason about toggles.
- **Two announced-but-unshipped items, stated plainly:** the FaceTime **video interpreting**
  feature is not in the iOS 27 beta yet (no integration details available), and there are
  **no new text-to-speech APIs** this year (back-end improvements only).

## Panel

Host **Cole** (core technologies evangelist) with **Julia Sonnen** (product marketing
manager for accessibility), **Drew** (accessibility software engineer; iOS/visionOS/iPadOS,
cognitive focus), **Greg** (engineering manager across accessibility products; low-vision
user), and **Syl** (accessibility QA; VoiceOver and braille user). A triage team worked
behind the scenes routing inbound questions. whisper has no diarization, so individual
attributions below follow self-introductions and Cole's on-air routing; where a turn can't
be safely attributed it's left to the panel.

## Warm-up: what's new for accessibility in 27 (01:34–04:51)

Julia framed the 27 releases as "a really good year for accessibility," much of it
announced around Global Accessibility Awareness Day (GAD, May 21; Newsroom post on the 19th).
The theme was bringing **Apple Intelligence thoughtfully into accessibility features**:

- **VoiceOver image descriptions and Live Recognition enhancements.**
- **More personalized Accessibility Reader.**
- **More intuitive Voice Control** (describe anything on screen in natural language).
- **Automatic subtitles in personal videos** (Julia's favorite: subtitling videos of her
  kid so her parents can follow what's said).

Panel favorites, even if not brand-new:

- **Vehicle motion cues**, now on visionOS and **spatialized** while wearing Vision Pro.
- **Voice Control's AI natural-language on-screen description** ("a game changer").
- **Built-in VoiceOver image descriptions** — described as a single in-app flow: swipe up on
  an image, use the VoiceOver custom action, double-tap, and get a description without leaving
  the app for the share sheet or a separate AI model.

## Developer Q&A

### Testing strategy and prioritization

- **Best advice for testing usability?** Beyond Accessibility Inspector (audit elements) and
  Device Hub (test across screen sizes), the panel's first instinct is **Dynamic Type / large
  text**: lay out the UI dynamically so it can relayout at accessibility font sizes, which
  also pairs with multiple/different screen sizes.
- **"On most teams accessibility testing means VoiceOver and they call it a day. How do you
  prioritize across assistive techs when you can't test everything, and which do we most
  underestimate?"** The panel appreciated the honesty and reassured: you can ship a great
  accessible experience without an expert per feature. **Test VoiceOver first** — it's
  approachable and convenient (no need to obtain a switch), and because many assistive
  technologies build on the same accessible foundation, much of Switch Control and Voice
  Control "comes for free." Voice Control's API surface is small; if it works well, most
  others do too. The panel added that real-user feedback, while not required, polishes the
  experience the way visual design feedback does. (Syl on testing, Greg "bang for your buck,"
  Drew on shared backend APIs.)

### VoiceOver behavior and APIs

- **"Can I override or turn off the system-provided image description? VoiceOver reads my
  provided description immediately followed by the system's, on a SwiftUI image with an
  accessibility-label modifier."** Take the **image trait off** and VoiceOver stops appending
  its own description, representing just your label. The panel flagged the tradeoff: removing
  the trait denies the user the new image-description features (the ability to ask further
  questions about the image), and noted a subtle sound already separates author alt text from
  the auto-generated description. There was an explicit values discussion about artist intent
  vs. letting a blind user experience artwork through AI description. (Syl on the sound and
  tradeoff; Greg/Julia on the artwork question.)
- **"A button triggers a network request; I swap in a progress view then update the button.
  How do I make VoiceOver announce completion?"** Use accessibility notifications:
  **layout-changed** for smaller UI changes (you can pass an accessibility element to move
  VoiceOver focus, but don't yank focus too often), **screen-changed** for whole-screen
  changes, or the **announcement notification** (pass a string, VoiceOver speaks it).
- **"Any updates to the text-to-speech APIs this year?"** No new API. Back-end improvements
  only. The panel suggested filing feedback requests.
- **"With the new improved AI voices, will any be available for VoiceOver?"** The voices used
  on VoiceOver are **specifically tuned for screen-reader use** (VoiceOver users often run
  speech very fast). (Drew, with Syl backing.)

### Custom controls (AppKit and SwiftUI)

- **"For custom AppKit controls with context menus, hover actions, and custom buttons, how do
  I expose the same actions to VoiceOver?"** Default answer: **custom actions** — but on the
  Mac they're less common and users tend to skip them, so reserve them for accelerators/pro
  features. For important hover actions, consider real additional buttons. A favorite SwiftUI
  tool is **accessibility representation**: take a view, tell accessibility to ignore it, and
  re-expose it (one SwiftUI button can be exposed as four). AppKit can do the same via
  **accessibility children** plus your own accessibility elements (trickier). The panel also
  flagged hover actions as hard for cognitive accessibility (hidden, require searching the UI),
  so use them sparingly. (Greg on the APIs; Drew plus-one on cognitive.)
  - Note: whisper inserted a stray "BRIAN DORSEY:" tag at the start of this answer. No Brian
    was introduced; treat it as an artifact.
- **"For reusable SwiftUI views (labels, icons, composite wrappers), how do I expose stable
  accessibility identifiers for UI tests and Accessibility Inspector without misusing the
  accessibility label?"** Wrap the reusable view so callers **must** provide both an
  accessibility identifier and an accessibility label; add checks that disallow a nil label.
  That enforces stable, concrete labels for everyone reusing the component. The panel
  specifically urged requiring labels on symbol/icon buttons.
- **"SwiftUI has a new `reorderable` modifier. How do I make it accessible to VoiceOver?
  Dragging and dropping isn't a VoiceOver idiom."** The panel wasn't certain it works in the
  beta and suspected the platform mattered: iOS drag-and-drop works well in VoiceOver, but on
  macOS "some other things need to land" before it's reliable. Suggested taking it to the
  forums.

### macOS and cross-platform

- **"We're adding VoiceOver to our macOS app. With this year's updates, what should we
  revisit, and how do we test across assistive technologies on the Mac?"** The biggest Mac
  difference is **user expectations**: Mac VoiceOver users multitask and expect
  **accelerators** — hotkeys to jump to the sidebar, inspector, sections. Those aren't
  assistive-tech APIs, they're app features, and they also benefit power keyboard users. The
  new VoiceOver updates (e.g. image descriptions) come "for free." Tip: add VoiceOver to the
  **accessibility shortcut** (triple-click on iOS; **Command-F5** or **triple-press Touch ID**
  on Mac, no configuration needed, or **Command-Option-F5** without Touch ID) so you can
  toggle it without getting stuck in Settings. (Greg on accelerators; Cole/Drew on the
  shortcut.)
- **"Platform-specific pitfalls for a universal app (macOS, iPadOS, watchOS, tvOS) coming
  from iOS?"** Even setting accessibility aside, a great SwiftUI app needs per-platform
  tweaks, and accessibility is the same. Key nuances:
  - **iOS VoiceOver navigation is linear; macOS is hierarchical** — **grouping** matters a
    lot on the Mac. A complex app like Final Cut Pro with hundreds of controls is technically
    usable without groups but a poor experience; grouping can be make-or-break. iOS benefits
    too (jump-to-next-group via the rotor).
  - **Hit testing** matters far more for iOS VoiceOver; **accessibility children order**
    matters more on macOS.
  - Input modality differs: Mac always has a keyboard (expect strong keyboard support); tvOS
    has only a remote and linear navigation; phone is touch-based; plus mouse/trackpad,
    Vision Pro input, the **watch's Digital Crown**, and mobility users (someone using eye
    tracking won't lean on the keyboard). Test each platform individually. (Greg and Syl;
    Syl emphasized macOS grouping for efficient keyboard navigation.)

### Cognitive and mobility

- **"How does Apple design for people with limited or no use of their hands or arms (limb
  differences, paralysis, tremors, temporary injuries)?"** There's no one solution; different
  users need different tools. Hands-free input: **Voice Control, head tracking, eye tracking,
  Sound Actions, Switch Control**. Modified touch: **Touch Accommodations** and
  **AssistiveTouch**; plus **Reachability**. iOS 27 adds a **streamlined Touch Accommodations
  setup with a calibration activity** (tap a target, get a recommended flow). The panel
  praised **on-device eye tracking** using the built-in camera (vs. accurate but hard-to-set-up
  external trackers): a user said it finally let them entertain themselves on an airplane
  without a boom-mounted rig or help changing the movie. Recurring lesson: two people with the
  same disability often use vastly different solutions, so the goal is to let users adapt the
  device rather than box them into one technology. (Julia on the feature set and calibration;
  Greg on eye tracking.)
- **"Features made with neurodivergent people in mind?"** Two big cognitive features:
  **Guided Access** (keep someone in a single experience: a book, a show, a FaceTime call,
  without wandering off) and **Assistive Access** (a simplified visual language: big icons,
  larger default text, larger touch targets). Assistive Access recently gained a **TV app**
  (a library; tap a downloaded show, get play/pause and back), and there are **developer APIs
  to build a fully optimized Assistive Access version of your app**. Also helpful across iOS:
  Screen Time, **Speak Screen / Speak Selection**, and **Accessibility Reader** (text-to-speech
  with word highlighting). (Drew.)

### Building accessibility in from the start

- **"At the start of an app project, what process keeps accessibility built-in rather than
  bolted on, from UX research and wireframing into Swift, testing, and assistive-tech
  support?"** Asking the question already puts you ahead. Bolting accessibility on at the end
  is "taping it over the top" and never matches designing from the ground up. Practical moves:
  check each feature with VoiceOver from day one; study a successful comparable app; do
  usability research if you have the resources. For large-team buy-in, **show, don't tell**:
  Syl described demonstrating to developers that as a VoiceOver user "my phone is just saying
  literally nothing — I can't touch this at all," because the human connection moves people more
  than abstract asks. Greg added: put accessibility on the **planning checklist alongside
  security and privacy** so it's elevated for everyone; force prototypes to carry some
  accessibility; and still **reserve refinement time near the end** (after the UI solidifies)
  to fine-tune VoiceOver and Dynamic Type without redoing it every time the UI churns. Cole's
  framing: bake it into every iteration, not a two-week panic at the end. Drew added a
  prioritization point: weigh **criticality to users**, not just utilization/headcount — don't
  judge accessibility features purely by the bell curve.
- **"What new accessibility tech could a medical student's EHR patient portal fold in now?"**
  **Accessibility Reader** (system-wide, launch from any app, customize long-form reading,
  integrates with spoken content) and **image descriptions / Image Explorer** (which describe
  not just photos but **charts and data-rich images**). More generally, "where do you start?":
  **Dynamic Type first**, then VoiceOver labels, then refine. (Drew; Greg on where to start.)

### Testing, regressions, and tooling

- **"I was excited to see a VoiceOver option in the Xcode 27 beta Device Hub, but it didn't do
  anything. Simulator-only or physical devices? Is this the year we can test iOS VoiceOver on
  a Mac?"** There's a **new XCTest API to test VoiceOver on your iOS device from a Mac**, and
  to the panel's best knowledge it should work in the simulator (check in seed 1). They flagged
  it as a great forums question and noted the feedback report. (Greg, "10,000-foot view.")
- **"Besides manual testing, how do you prevent accessibility regressions — snapshot testing,
  automated UI tests with accessibility assertions?"** **Automated testing**: XCTest and
  XCUITest rely on the accessibility hierarchy across all Apple platforms, so an inaccessible
  app fails its UI tests. Add assertions on accessibility labels (e.g. assert UI elements have
  labels). The new VoiceOver test API can drive VoiceOver element to element and validate it
  speaks the right thing, usable as a smoke test ("can VoiceOver reach all N elements? does it
  hit any element that speaks nothing?"). Automated testing "gets you 90% of the way there."
  (Greg.)
- **"I'm a blind iOS developer. Any accessibility improvements in Xcode and developer tools
  this year?"** Yes: **improved Terminal accessibility** (move to visible beginning, better
  marks, better reading of tab-autocomplete suggestions, and more), Xcode VoiceOver bug fixes
  in seed 1, and "more to come" hinted for Xcode. Greg added that **SwiftUI itself is a leveler**:
  no more WYSIWYG Interface Builder requirement, everyone defines UI in code, so a blind
  developer works the same way as peers. Mac VoiceOver rotors in Xcode (not new) jump between
  methods, errors/warnings in a file, variable names. AI is expected to level the field
  further by scanning and summarizing code for those who can't quickly visually scan it. Drew
  plugged the **new Xcode AI skills** (seed 1) for VoiceOver and Dynamic Type: "make this view
  accessible for VoiceOver," "what's wrong with my file with regard to VoiceOver" — Apple's
  internal accessibility knowledge boiled into a skill. (Syl on Terminal; Greg on SwiftUI/AI;
  Drew on AI skills.)

### visionOS and announced-not-shipped

- **"For spatial tools that use eye tracking or gaze, how do designers avoid assuming all
  users can rely on visual gaze?"** visionOS has **Pointer Control**: instead of gaze (or
  hands), use a body part as the pointer — **head, finger, or wrist as an anchor**. Drew noted
  he and a teammate named **Dan** presented a comprehensive visionOS accessibility session at
  the visionOS launch that covers this, and that there's an **API for making RealityKit content
  accessible** (also covered there). (Drew.)
- **"More on the FaceTime video interpreting feature announced at WWDC — how do third-party
  apps integrate, how is the session initiated, any entitlements or restrictions?"** That
  feature is **not yet in the iOS 27 beta**; more information when the APIs ship. (Panel.)

### Closing thank-you

A developer wrote in not with a question but to thank the team and note that **"assistive
technology is for everyone"** — they use VoiceOver and Dictation daily without a disability
and love it. The panel highlighted it as a closing thought.

## Unconventional facts & takeaways (the live-Q&A gold)

- **VoiceOver-first carries most of the other accessibility technologies.** Because Switch Control, Voice
  Control, and others read the same `isAccessibilityElement` / accessibility-label backend, a
  great VoiceOver pass genuinely covers most of the rest. Voice Control's incremental API
  surface is small; the per-technology API is "polish around the edges."
- **The image-trait suppression tip has a real cost.** Removing the image trait silences the
  duplicate auto-description but also strips the user's ability to ask follow-up questions
  about the image. And there's already a subtle sound demarcating author alt text from
  auto-generated description, so users aren't confused by the two.
- **A subtle audio cue marks where your alt text ends and AI description begins** — small
  detail, on by default, easy to miss.
- **Dynamic Type is the panel's universal "start here."** Broad benefit, and implementing it
  forces relayout (Control Center controls grow for larger text), which is a two-way street
  you must handle early, not at the end.
- **macOS VoiceOver is hierarchical, iOS is linear** — so **grouping** is make-or-break on the
  Mac (Final Cut Pro-scale apps), while **hit testing** dominates on iOS. A concrete, often
  overlooked distinction.
- **Mac accelerators (hotkeys) double as accessibility.** Jump-to-sidebar/inspector hotkeys
  aren't assistive-tech APIs but materially improve the VoiceOver experience and also serve
  power keyboard users.
- **SwiftUI leveled the field for blind developers** by removing the WYSIWYG Interface Builder
  step — everyone now edits UI in code.
- **Candid "not shipped yet" admissions:** FaceTime video interpreting has no beta API; there
  are no new text-to-speech APIs this year (back-end only).
- **VoiceOver voices are tuned specifically for screen-reader use**, distinct from the general
  improved AI voices, because VoiceOver users often run speech very fast.
- **Cultural color:** the panel are users of their own products (Greg low-vision, Syl
  VoiceOver/braille, Julia large-text/zoom); "everyone is different — two people with the same
  disability use vastly different solutions"; "show, don't tell" demos to win team buy-in; and
  the on-airplane eye-tracking story as the emotional anchor for on-device features.
