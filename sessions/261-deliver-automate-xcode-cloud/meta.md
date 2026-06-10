# Session 261 — Build, deliver, and automate with Xcode Cloud

- **URL:** https://developer.apple.com/videos/play/wwdc2026/261/
- **Duration:** 14m
- **Speakers:** Tony (engineer on the Xcode Cloud team)

## Description

Xcode Cloud is a continuous integration and delivery service built into Xcode and
designed expressly for Apple developers. This year focuses on refining the
fundamentals — building, testing, and distribution. The session walks through
onboarding a new iOS app (and a companion macOS app) to Xcode Cloud with the
onboarding assistant, setting up TestFlight distribution from inside Xcode, and
extending workflows with webhooks and additional-repository management.

## Key topics

- Essential concepts: cloud builds + parallel tests across devices/OS versions; quality companion as agent-assisted coding accelerates code volume
- Getting started: Report navigator → Cloud tab → Get Started; onboarding assistant connects the source repo and starts the first build
- Ephemeral build VMs: source fetched only at build start, thrown away after, never stored — Apple cannot access it
- Onboarding a second product (macOS app) in the same workspace via Create Workflow — no repeat repository-connect step
- Distribution: Set Up Distribution creates the App Store Connect app record (name, Bundle ID, SKU), then a TestFlight (internal) distribution workflow
- Second distribution path: Manage Workflows → add an Archive action (required for TestFlight), prompted to set up distribution inline
- Webhooks: build-event payloads (created / started / completed) sent to an endpoint for custom dashboards and automations; delivery history with success indicators
- Additional repositories: Manage Repositories to add shared dependencies (e.g. a style framework split into its own repo) without re-authorizing the provider

## Related sessions to fetch (referenced in this talk)

- [ ] Connect your project to Xcode Cloud
- [ ] Create practical workflows in Xcode Cloud
- [ ] Extend your Xcode Cloud workflows
- [ ] Simplify distribution in Xcode and Xcode Cloud

## Chapter summary (Summary tab)

- **0:00 Introduction** — Tony, Xcode Cloud team. What's new this year (refined fundamentals) and the agenda: essential concepts, getting started, distribution, workflow automation.
- **1:13 Essential concepts** — Builds and tests in the cloud in parallel across multiple devices and OS versions, catching bugs and performance issues before they reach customers; delivers builds to TestFlight and the App Store. Framed against more code being written with agents arriving in Xcode.
- **2:07 Getting started** — Onboard an iOS app via the Report navigator's Cloud tab and the onboarding assistant; connect the source repository; start the first build. Then onboard a companion macOS app in the same workspace via Create Workflow. Ephemeral VMs; source never stored.
- **6:42 Distribution** — Set Up Distribution creates the App Store Connect app record (name, Bundle ID, SKU) and a TestFlight distribution workflow. Second path via Manage Workflows + an Archive action for the macOS app.
- **9:21 Webhooks** — Configure webhooks to receive build-event payloads (created/started/completed) and integrate Xcode Cloud into custom dashboards and automation services; inspect delivery history.
- **11:22 Additional repositories** — Add additional Git repositories (Manage Repositories) so builds access shared dependencies like a framework split into its own repo. No re-authorization needed.
- **13:00 Next steps** — Try it yourself; deeper sessions on practical workflows, extending automations, and simplifying distribution.

## Code

See `code.md` — this is a demo-driven, UI-walkthrough session with no Code tab.
