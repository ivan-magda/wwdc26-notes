# Session 391 — Offer subscriptions to groups and organizations

- **URL:** https://developer.apple.com/videos/play/wwdc2026/391/
- **Duration:** 8m
- **Speakers:** Andrew (Apple commerce team)

## Description

Sell auto-renewable subscriptions to groups and organizations, not just
individuals. There are two purchase paths: in-app **group purchases** (a customer
buys multiple seats and shares an invite link with their social group or team)
and **volume purchasing** through Apple Business Manager and Apple School Manager
(an organization buys seats and assigns them through a device management service).
The session covers availability defaults, volume pricing bands, the purchase
flow, and how seats are assigned and managed afterward.

## Key topics

- Two new sales paths for subscriptions: in-app **group purchases** and **volume purchasing** in Apple Business Manager / Apple School Manager
- Requires **StoreKit 2** and **auto-renewable** subscriptions
- On by default for most subscriptions; **opted out by default** when Family Sharing is enabled; configurable per subscription in App Store Connect (including School-Manager-only for educational pricing, or fully off)
- **Volume pricing** — up to **5 price bands** with reduced per-seat pricing for larger purchases; configured in App Store Connect
- Purchase flows: ABM/ASM handle volume purchasing; you build your own in-app UI and pass a requested **seat count** into the StoreKit 2 purchase request for group purchases
- **Seat management** — org assigns seats via device management (volume purchasing) or invite links (group purchases); App Store assigns a transaction per member
- **Included seat management system** (invite link generation, acceptance/assignment tracking, lifecycle/cancellations) vs. **custom invitation flows** via new App Store Server API endpoints
- **App Store Server API Group management endpoints** — query all groups a customer belongs to and all members in a group (for collaborative/shared-resource features)

## Related sessions to fetch

- [ ] _None explicitly named in this talk._

## Chapter summary (Summary tab)

- **0:00 Introduction** — Sell subscriptions to groups and organizations through two paths: in-app group purchases, and volume purchasing in Apple Business and Apple School Manager.
- **2:17 Availability** — Available for all auto-renewable subscriptions using StoreKit 2, on by default for most, opted out for Family Sharing subscriptions, and configurable per subscription in App Store Connect.
- **3:24 Pricing** — By default each seat sells at the current price; volume pricing lets you set up to five price bands with reduced per-seat pricing for larger purchases.
- **4:43 Purchasing** — Apple Business and Apple School Manager handle volume purchases; for in-app group purchases, you build the UI and trigger the StoreKit 2 purchase flow with the requested seat count.
- **5:25 Seat Management** — How seats are assigned after purchase — through a device management service for volume purchasing, or invitation links for group purchases, with an included or custom seat management system.
- **7:05 Next steps** — Start thinking about how your application can take advantage of group purchases and volume purchasing.

## Code

See `code.md` — no Code tab for this session; the talk shows App Store Connect
configuration and conceptual flows rather than source snippets.
