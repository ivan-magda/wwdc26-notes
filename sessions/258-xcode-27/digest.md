---
title: "What's new in Xcode 27 — Full Digest"
session: WWDC26 · 258
url: https://developer.apple.com/videos/play/wwdc2026/258/
duration: 28m
speakers: Jake, Breckin (Xcode team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 258 — What's new in Xcode 27

## TL;DR

Xcode 27 is pitched as an end-to-end companion across the whole app lifecycle, demoed
on a "Paper Airplanes" sample app. Two presenters split it: Jake covers creation, Breckin
covers shipping and post-launch. Headline themes:

1. **A customizable workspace** — redesigned Toolbar (now fully reorderable), a new themes
   system with sliders/presets/per-project themes, and inline issues that stay subtle while
   you type and only go full-intensity after a build.
2. **Frictionless starting points** — instant *untitled* projects ("no questions asked")
   and standalone `.swift` files that show previews + playground results without a project.
3. **Coding agents move into the editor** — agent conversations are now editor panes (tabs,
   splits), with a `/plan` command that scopes work and spawns parallel sub-agents before
   touching code, plus a sidebar tracking parallel conversations.
4. **Device Hub** — one window to run, inspect, and evaluate apps across simulators *and*
   physical devices, including accessibility settings and iPhone Mirroring resize testing.
5. **Post-launch tooling expands** — agent-driven localization, a redesigned Organizer
   (new storage + hitches metrics, Metric Goals, agent-generated fix recommendations),
   Top Functions in Instruments, and a much shorter Xcode Cloud onboarding.

The throughline: agents and the workspace run through every stage of the lifecycle.

---

## 1. Workspace & Toolbar (1:01)

The Toolbar and Themes were revamped for more customization.

- Controls previously in the jump bar — **history navigation** and **editor controls** —
  move up into the Toolbar.
- **Activity info** (e.g. build progress) appears under the window title.
- Center: the new **coding-agent entry point** and the scheme/destination picker.
- Top right: add tabs/editor panes, editor settings, and a **3-way editor-mode chooser**:
  1. previews & playgrounds in the canvas,
  2. related content in the Assistant Editor,
  3. a source-control change-review mode.
- The **branch picker moved to the bottom bar** (room for long branch names).
- Best part: the Toolbar is **fully customizable** — add/remove/reorder items.

## 2. Themes (2:13)

A new **Appearance panel** in Settings configures themes:

- The standard theme is brighter/more colorful. **Two sliders**: one for text-color
  intensity, one for background intensity (ramps from a tint → full window background color
  → a vibrant gradient at max).
- Not locked to defaults — pick any tint (e.g. pink) and the whole palette regenerates.
- **Preset themes** (e.g. "Neon Noir") are browsable; the full per-value color list below is
  generated from the base palette. Override a value (e.g. keyword → pink) and it's **locked**
  (palette edits stop influencing it); a **reset** action restores it to automatic.
- **Fonts** use the same palette model: base fonts for code/prose/console drive derived fonts
  across editors; change size/variation/family and the rest update.
- Themes tint the **entire workspace window**, including other editors and project settings.
- **Per-workspace themes**: assign a different theme (and separately-saved font settings) per
  project to tell similar projects apart at a glance. Dark appearance is far more vibrant.

## 3. Inline issues (5:04)

- Predictive / "live" issues get a **subtle, theme-blended look** while typing, to cut
  distraction and to differentiate them from build-time issues.
- As you edit, Xcode predicts issues as if a build were about to run.
- On a real **build**, subtle predictions either upgrade to **full-intensity** warnings/errors
  or get dismissed if they were resolved.

## 4. New project workflows (6:08)

- **File > New** offers starting points: **App** (SwiftUI), **macOS Command Line Tool**,
  **Swift package**, **Playground** (a standalone `.swift` file with the Playground macro).
- Choosing App creates a brand-new **untitled project**, "no questions asked" — flesh out the
  idea, then name & save it or discard it entirely. Great for blank-canvas prototyping.
- Opening a **standalone `.swift` file** gives a workspace window that shows **playground
  results and UI previews** even though the file isn't part of a project — easy to share
  lightweight ideas.

## 5. Coding agents in the editor (8:40)

Working with coding agents is "supercharged" for parallel tasks/conversations.

- The agent **transcript moved into the editor pane**, so it composes with other editors via
  tabs/splits.
- The editor shows what the agent **changed** plus any **artifacts** produced (files,
  screenshots) on the right-hand side, including how the app evolves as the agent drives the
  simulator and previews.
- The Toolbar's agent button kicks off a new conversation/task.
- **`/plan`** invokes the plan tool: specify details, the agent gathers context **without
  making changes yet**, and can **spawn parallel sub-agents** while exploring. You can give
  guidance mid-flight, then read the plan, give **inline feedback**, or approve implementation.
- A **coding assistant sidebar** lists other parallel conversations/tasks and flags which need
  input or have unread messages.
- Deep-dive: **"Xcode, agents, and you."**

## 6. Device Hub (9:37)

Running an app on a simulator now opens it as a **Device Hub** window.

- The window is **compact, sized to the device**; quick actions: home, screenshot, rotate.
- **Expand** for more space/controls; open the **Inspector** for evaluation tools.
- **Accessibility testing**: increase contrast, larger Dynamic Type sizes, dark appearance.
- **iPhone Mirroring** can be resized on **macOS 27**, so you can test the new resize mode at
  different aspect ratios and content sizes. (The demo's app handled it well thanks to standard
  SwiftUI views + prior resizable-window work for iPad/Mac.)
- A combined **sidebar lists simulators *and* physical devices** — a paired **iPad Pro** running
  the app can be seen and controlled directly in Device Hub.
- Also unlocks files, data containers, app-configuration evaluation, and more.
- Deep-dive: **"Get the most out of Device Hub."**

## 7. Localization via agents (13:13)

- Because the agent is an LLM, it's well-suited to suggesting translations.
- Ask the agent to **set up localization**: it reads the code, makes string literals
  **localizable**, and creates a **String Catalog** of every UI string. Started with Spanish.
- The agent then **batch-translates** every UI string; you can watch progress in the String
  Catalog or the conversation. It uses **full project context + Xcode language-specific style
  guidance**.
- Within minutes you get a localized build to run and test (catching truncation/layout issues).
- In the String Catalog, add a language with the `+` button, select it, and click the new
  **Generate Translations** button (demo: Simplified Chinese) — runs in the background.
- **TestFlight** is recommended for native-speaker translation feedback.
- Tips: ask the agent to make existing strings localization-ready; start with 1–2 languages;
  always test even languages you don't read.
- Deep-dives: **"Translate your app using agents in Xcode"**,
  **"Code-along: Explore localization with Xcode."**

## 8. Organizer (16:57)

The Organizer now helps you **act on** reports, not just collect them. Four new things:

1. **Redesigned Overview** — surfaces highest-impact issues first and puts **diagnostics and
   metrics on one screen** (a metric spike up top, the diagnostics that point at the code below).
2. **New metrics** —
   - **Storage**: how much space the app and its data use, broken into **documents, data, and
     binary size** (binary size affects cellular download + launch time). Demo: v1.0/v1.01 were
     large → compress images → App Size chart shows the win.
   - **Hitches** (animation): now beyond scrolling, covering Liquid Glass and SwiftUI views;
     catches animations the old metric missed. Demo flagged a bad hitch in v1.3.
3. **Metric Goals** — last year's launch-time recommendations expand into goals across **hang
   rate, disk writes, battery, storage, and hitches**, calibrated against **similar apps** (by
   what the app does + technologies used) and the app's **own historical baselines**.
4. **Generate Recommendations…** — runs a coding agent over the diagnostic data for guided
   performance analysis and iterative fixes (e.g. for hangs).

## 9. Instruments — Top Functions (21:07)

- New **Top Functions** view surfaces the most expensive code paths over a selected time range,
  ideal for expensive operations performed many times.
- Demo: reproduce the v1.3 hitch in the Simulator, select the busy CPU range, hit **Top
  Functions** → top function is **`paperPhysics`** in the animation pipeline. Open it via
  **Quick Open (CMD+Shift+O)**, find an accidentally-left-in over-iterating loop, reduce the
  count to 5 → hitch gone, and no app methods appear in Top Functions on the re-run.
- Processor trace and **performance-run comparison** are teased.
- Deep-dives: **"Debug and profile agentic app experiences with Instruments"**,
  **"Profile, fix, and verify: Improve app responsiveness with Instruments."**

## 10. Xcode Cloud (25:48)

- CI/CD built into Xcode for Apple platforms; builds + tests in the cloud in parallel across
  devices, Xcode, and OS versions.
- **Streamlined onboarding**: Get Started → confirm app/team → connect remote repo → Start First
  Build. Unit + UI tests then run on **every commit** to main/feature branches as a regression
  signal.
- Integrates with **TestFlight and the App Store** for delivery.
- Deep-dives: **"Build, deliver, and automate with Xcode Cloud"**,
  **"Extend your Xcode Cloud workflows."**
