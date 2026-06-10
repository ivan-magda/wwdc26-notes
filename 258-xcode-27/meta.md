# Session 258 — What's new in Xcode 27

- **URL:** https://developer.apple.com/videos/play/wwdc2026/258/
- **Duration:** 28m
- **Speakers:** Jake (designer, Xcode team), Breckin (Xcode team)

## Description

A guided tour of Xcode 27, framed around a "Paper Airplanes" sample app and walked
through the full app lifecycle. The first half (Jake) covers the redesigned,
customizable workspace — new Toolbar, themes, subtle inline issues — plus instant
untitled projects, standalone Swift files with previews, coding agents living in the
editor pane, and the new Device Hub. The second half (Breckin) covers shipping and
post-launch: agent-driven localization, the redesigned Organizer with new metrics and
Metric Goals, Top Functions in Instruments, and a streamlined Xcode Cloud onboarding.

## Key topics

- **Workspace redesign** — Toolbar absorbs jump-bar controls (history nav, editor controls); activity/build progress under the window title; new coding-agent entry point center; 3-way editor-mode chooser (canvas previews/playgrounds, Assistant Editor, source-control review); branch picker moved to bottom bar; Toolbar fully customizable (add/remove/reorder items).
- **Themes** — new Appearance panel; two sliders (text-color intensity, background intensity → full color → vibrant gradient); custom tints; preset themes (e.g. "Neon Noir"); per-value overrides generated from a base palette with reset; font palette (code/prose/console) drives derived fonts; per-workspace themes; far more vibrant in dark appearance.
- **Inline issues** — predictive/"live" issues get a subtle theme-blended look while typing; upgrade to full-intensity warnings/errors only after a build (or dismissed if resolved).
- **New project workflows** — File > New starting points (App, macOS Command Line Tool, Swift package, Playground); instant untitled projects (save & name later or discard); opening a standalone `.swift` file gives a workspace window with playground results + UI previews even outside a project.
- **Coding agents in the editor** — agent conversation lives in an editor pane (tabs/splits); changes + artifacts shown on the right; `/plan` slash command (plan tool) scopes work and spawns parallel sub-agents before any code changes; inline feedback on the plan; coding assistant sidebar lists parallel conversations/tasks with unread/needs-input state.
- **Device Hub** — running on a simulator opens a compact device-sized window; quick actions (home, screenshot, rotate); expandable with Inspector; accessibility testing (increased contrast, Dynamic Type sizes, dark appearance); iPhone Mirroring resize testing on macOS 27; combined simulator + physical-device sidebar; control a paired iPad Pro directly.
- **Localization via agents** — agent sets up localization, makes string literals localizable, creates a String Catalog, batch-translates every UI string (started with Spanish); uses full project context + Xcode language-specific style guidance; per-language "Generate Translations" button in String Catalog runs in background; TestFlight for native-speaker feedback.
- **Organizer (4 new things)** — redesigned Overview surfaces highest-impact issues, diagnostics + metrics on one screen; new Storage metric (documents/data/binary size breakdown) and animation Hitches metric (beyond scrolling — Liquid Glass + SwiftUI views); Metric Goals (hang rate, disk writes, battery, storage, hitches) calibrated against similar apps + own historical baselines; agent-powered "Generate Recommendations…" for guided fixes.
- **Instruments — Top Functions** — new view that surfaces the most expensive code paths over a selected time range; demo finds a `paperPhysics` hot loop, fixed via Quick Open (CMD+Shift+O); processor trace called out; performance-run comparison teased.
- **Xcode Cloud** — streamlined onboarding (Get Started → confirm app/team → connect repo → Start First Build); runs unit + UI tests on every commit to main/feature branches; TestFlight + App Store delivery.

## Related sessions to fetch (referenced in this talk)

- [ ] Xcode, agents, and you
- [ ] Get the most out of Device Hub
- [ ] Translate your app using agents in Xcode
- [ ] Code-along: Explore localization with Xcode
- [ ] Debug and profile agentic app experiences with Instruments
- [ ] Profile, fix, and verify: Improve app responsiveness with Instruments
- [ ] Build, deliver, and automate with Xcode Cloud
- [ ] Extend your Xcode Cloud workflows

## Chapter summary (Summary tab)

- **0:07 Introduction** — Jake & Breckin; agenda previews workspace customization, new project workflows, coding agents, Device Hub, and post-launch tools.
- **1:01 Workspace & Toolbar** — redesigned Toolbar with new controls, a coding-agent entry point, and full customization (reorderable items).
- **2:13 Themes** — Appearance panel with sliders for colors/fonts, preset themes, and per-project themes for quick visual identification.
- **5:04 Inline Issues** — predictive issues use a subtle appearance while typing; upgrade to full-intensity warnings/errors only after a build.
- **6:08 New Project Workflows** — instant untitled projects or standalone Swift files with previews and playground results for on-the-fly prototyping.
- **8:40 Coding Agents in the Editor** — agent conversations live in the editor pane (tabs/splits) with a new `/plan` command to scope work before changes.
- **9:37 Device Hub** — unified window for running, inspecting, and evaluating apps across simulators and physical devices, incl. accessibility settings and iPhone Mirroring resize testing.
- **13:13 Localization** — coding agents set up localization, create String Catalogs, and generate translations for multiple languages.
- **16:57 Organizer** — redesigned Organizer surfaces high-impact issues first, adds storage and animation-hitch metrics, introduces Metric Goals, and generates agent-powered fix recommendations.
- **21:07 Instruments & Top Functions** — new Top Functions view identifies the most expensive code paths, cutting investigation time for performance regressions.
- **25:48 Xcode Cloud** — streamlined onboarding to get builds and tests running on every commit, with TestFlight and App Store delivery.
- **27:51 Next steps** — Xcode 27's end-to-end app-lifecycle support, from prototyping and agents to localization and post-launch issue resolution.

## Code

See `code.md` — no Code tab for this session; it lists the concrete APIs / shortcuts named in the transcript instead.
