# Session 309 — Explore Retention Messaging in App Store Connect

- **URL:** https://developer.apple.com/videos/play/wwdc2026/309/
- **Duration:** 15m
- **Speakers:** Tori (engineer, App Store server team)

## Description

Retention Messaging is a new feature that lets you reach subscribers at the moment
they're about to cancel an auto-renewable subscription, giving you a chance to remind
them of the subscription's value or present an offer to keep them subscribed. The
session covers two flavors: **Retention Messaging in App Store Connect** (configure
once, the App Store decides what to show) and **real-time Retention Messaging** (your
server decides per customer via a server-to-server API). It walks through setup in
App Store Connect, the Retention Messaging API, sandbox testing, fallback behavior,
and a side-by-side comparison to help pick the right fit.

## Key topics

- Retention Messaging appears in the subscription **cancellation flow** when a customer is about to cancel
- Three view types: **message only**, **message + image**, **message + offer**
- Reported impact: avg save-rate increase **+1.4 points (≈82%)**; promotional-offer messages **+5.5 points (≈223%)** — "varying results across developers"
- **App Store Connect** path: configure retention messages, map to subscriptions, pick images from **Asset Library**, attach **retention offers**; also configurable via **App Store Connect API**
- **Retention offers** — a new offer type; signed transaction / renewal info carries **`offerType: 5`**
- **Real-time Retention Messaging** — your endpoint answers a server-to-server HTTP request from the App Store with a message preference
- **Retention Messaging API** (`api.storekit.apple.com/inApps/v1/messaging`) — manage URL, messages, default messages, images; sandbox **performance test** required before production
- Real-time adds a **switch-plan** view (offer a different plan in the same subscription group, e.g. annual)
- Three real-time response formats: **message**, **alternateProduct** (switch plan), **promotionalOffer**
- **Fallback chain**: real-time response → App Store Connect preference → API default messaging
- Supports the new **monthly-with-12-month-commitment** billing plan type as a switch plan (`billingPlanType`)
- Fully testable in **sandbox**; real-time requires an **interest form** for access

## Related sessions to fetch (referenced in this talk)

- [ ] Enhance your presence on the App Store (Asset Library) — WWDC26
- [ ] What's new in Apple In-App Purchase (monthly + 12-month commitment) — WWDC26

## Chapter summary (Summary tab)

- **0:00 Introduction** — Retention Messaging reaches subscribers as they're about to cancel; an opportunity to save the subscriber at a critical moment. Three views (message / message+image / message+offer). Save-rate stats cited.
- **2:38 Retention Messaging in App Store Connect** — set up messages that are automatically delivered when a customer is about to cancel; use Asset Library images and retention offers to add value; App Store Connect API supported. Demo of creating a Yoga message; sandbox testing; `offerType: 5`.
- **6:38 Real-time Retention Messaging** — set up an endpoint; App Store makes a server-to-server call to get your real-time message preference. Retention Messaging API endpoints; switch-plan view; three response formats; performance test; fallback chain; sandbox→production flow; 12-month-commitment switch plan.
- **11:46 Retention Messaging comparison** — compare the two to determine the right fit: decisioning (App Store vs you), configuration mechanism, offer types (retention vs promotional), available views (ASC: 3, real-time: 4 incl. switch plan). Real-time builds on top of ASC; always set up ASC for fallback.

## Code

See `code.md` — signed-transaction sample, the Retention Messaging API endpoint map,
and the real-time request/response payload shapes (message / alternateProduct /
promotionalOffer) from the Code tab.
