---
title: "Build, deliver, and automate with Xcode Cloud — Full Digest"
session: WWDC26 · 261
url: https://developer.apple.com/videos/play/wwdc2026/261/
duration: 14m
speakers: Tony (Xcode Cloud team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 261 — Build, deliver, and automate with Xcode Cloud

## TL;DR

Xcode Cloud's WWDC26 update is a "fundamentals" release: no new headline product, just
a smoother path through the whole build → test → distribute → automate loop, demoed end
to end on an office "coffee shop" app (iOS + a companion macOS app). Five things worth
remembering:

1. **Onboarding is a few clicks** — Report navigator → Cloud tab → Get Started; the
   onboarding assistant connects your repo and kicks off the first build. A default
   workflow is created for you.
2. **Second product, less friction** — onboard another app in the same workspace via
   Create Workflow with **no repeat repository-connect step** once access is granted.
3. **Distribution from inside Xcode** — Set Up Distribution creates the App Store
   Connect app record (name, Bundle ID, SKU, with taken-name detection inline) and a
   TestFlight internal distribution workflow. An alternate path adds an **Archive
   action** (required for TestFlight) via Manage Workflows.
4. **Webhooks** for build-lifecycle events (created / started / completed) → push
   payloads to your own dashboards and automations, with delivery history.
5. **Additional repositories** — pull in shared dependencies (a style framework split
   into its own repo) via Manage Repositories, no re-authorization needed.

Underlying privacy point: builds run on **ephemeral VMs**; source is fetched only at
build start, discarded afterward, never stored, and Apple cannot access it.

The framing nod: with **agents arriving in Xcode**, developers are writing more code
faster, so cloud-side parallel build/test is positioned as the "companion for quality"
that keeps pace.

---

## 1. Essential concepts (1:13)

Xcode Cloud is CI/CD built into Xcode and designed for Apple developers. The pitch this
year leans on a trend: with agent support arriving in Xcode, more code is being written
faster, and the question becomes how to scale quality and catch bugs and performance
regressions before they reach customer devices.

Versus local development, Xcode Cloud builds and tests **in the cloud, in parallel,
across multiple devices and OS versions** — then makes it simple to set up distribution
to TestFlight and the App Store. The running example is a part-time-barista engineer's
office coffee-ordering app whose growing unit + UI test suite (checkout flow must never
regress) is getting slow to run locally.

## 2. Getting started (2:07)

Onboarding an iOS app:

- Report navigator → **Cloud** tab → **Get Started…**
- The assistant lists products in the workspace; the Developer Team is pre-filled from
  Signing & Distribution settings → Next.
- Xcode Cloud needs source access; the onboarding assistant loads the repository and
  walks through connecting (steps vary by source provider). Deep dive: **"Connect your
  project to Xcode Cloud."**
- **Ephemeral build VMs:** source is fetched only when a build starts and thrown away
  when it finishes. None of it is stored; Apple has no way to access it.
- Next → done. A product and a **default workflow** are created; one more click starts
  the first build.

Onboarding a second product (a macOS app sharing a style framework, same workspace):

- Report navigator → **More** button (bottom-left) → **Create Workflow…**
- Pick the macOS app → Next. Because repo access was already granted, **there is no
  Connect Repository step again** → Setup Complete → start the first build.
- The Cloud section now shows builds and workflows for both apps.

For richer multi-platform / multi-scenario workflows, see **"Create practical workflows
in Xcode Cloud."**

## 3. Distribution (6:42)

Two paths, both staying inside Xcode.

**Path A — Set Up Distribution (iOS app):**

- Cloud navigator → secondary-click the iOS app → **Set Up Distribution…**
- Provide the properties needed to create the official **App Store Connect app record**:
  app name, **Bundle ID** (unique identifier), and **SKU**. Xcode Cloud flags
  already-taken values inline so you can fix them without leaving the assistant (the
  demo's app name was taken).
- It onboards to distribution in the background: create app record → verify → register
  Bundle ID and SKU. Result: a new **distribution workflow for internal TestFlight**,
  ready to run.

**Path B — Manage Workflows + Archive action (macOS app):**

- Secondary-click the app → **Manage Workflows…** → **+** in the workflow manager.
- Create an **Archive action** — required to distribute to TestFlight. When you attempt
  it, you're offered a Set Up… flow; review properties → Create.
- Same background onboarding process runs for the macOS app.

## 4. Webhooks (9:21)

Webhooks are the tool for advanced automations. When configured, Xcode Cloud
automatically sends a **payload with build information** to an endpoint of your choice.
Hooks exist for **every stage of the build lifecycle**: when a build is **created**,
when it **starts**, and when it **completes** — ideal for feeding a custom dashboard
(the demo's motivation: engineer coworkers tracking their own bug fixes manually).

Setup:

- Secondary-click the iOS app → **Manage Webhooks…** → add a webhook.
- Provide a **Name** ("Dashboard") and a **Payload URL** (a publicly resolvable
  endpoint) → Add. It appears in the webhook list with no delivery history yet.
- Run a build → the Webhooks view shows **three events** for one build (the lifecycle
  hooks), with green indicators for successful deliveries.

More in the webhooks documentation.

## 5. Additional repositories (11:22)

As an app grows, a common practice is to split shared functionality into separate repos
to keep the codebase modular. The demo splits its **style framework** into its own
repository, and Xcode Cloud has to stay in sync so builds get the right dependencies.

- Secondary-click the iOS app → **Manage Repositories…**
- The primary repository is shown at the top; the **Additional** section starts empty.
- **Add** → paste the **Git remote URL** for the style framework → Add. Because access
  to the remote provider was already granted, **no re-authorization** is needed.
- Builds now pull all the right dependencies. Net effect: every push is automatically
  built, tested, and deployed, the team is notified on any change, and every build has
  exactly the right dependencies.

## 6. Next steps (13:00)

Try it: revisit the concepts, onboard your app to build/test/distribute, then extend
with webhooks and additional repositories. Deeper dives: **"Extend your Xcode Cloud
workflows"** and **"Simplify distribution in Xcode and Xcode Cloud."**
