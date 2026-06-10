---
title: "Offer subscriptions to groups and organizations — Full Digest"
session: WWDC26 · 391
url: https://developer.apple.com/videos/play/wwdc2026/391/
duration: 8m
speakers: Andrew (Apple commerce team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 391 — Offer subscriptions to groups and organizations

## TL;DR

Auto-renewable subscriptions can now be sold to **groups and organizations**, not
just individuals. Two purchase paths:

1. **In-app group purchases** — a customer buys **multiple seats** through your
   app (same as an in-app purchase today) and shares an **invite link**; whoever
   accepts gets a seat. Built for small teams and social groups.
2. **Volume purchasing** — a **brand-new channel** where businesses and schools
   buy subscriptions inside **Apple Business Manager / Apple School Manager** and
   assign seats via a **device management service**, reusing the workflows they
   already use to distribute apps at scale.

Key constraints and knobs:

- **StoreKit 2 is required.** Feature applies to all auto-renewable subscriptions.
- **On by default** for most subscriptions; **opted out by default** when Family
  Sharing is enabled; fully **configurable per subscription in App Store Connect**
  (including a School-Manager-only mode for educational pricing, or off entirely).
- **Volume pricing**: up to **5 price bands** for per-seat bulk discounts.
- **Seat management**: use Apple's **included system** (invite links, acceptance
  tracking, lifecycle) or wire up **custom invitation flows** + **Group management
  endpoints** in the App Store Server API.

---

## 1. The two paths (Introduction · 0:00)

The motivation: subscribers increasingly want to bring their **social group, team,
or company** onto an app — a company outfitting a team of video editors, or a run
club keeping members accountable through an exercise app. Until now subscriptions
were sold one individual at a time.

Two ways to purchase and manage:

- **Group purchases (in-app)** — purchase happens in your app, just like an
  in-app purchase today, but the customer buys **multiple seats** and shares an
  **invite link**. Acceptance auto-assigns a seat. Best for small teams / social
  groups collaborating on an app.
- **Volume purchasing (ABM / ASM)** — a **new channel for subscriptions**. The
  organization buys through the App Store *inside* Apple Business Manager or Apple
  School Manager, then assigns seats with a **device management service** using the
  same workflows they use for app distribution. Best for larger orgs with
  management and identity requirements.

## 2. Availability (2:17)

- Available for **all auto-renewable subscriptions using StoreKit 2**.
- For most new and existing StoreKit 2 subscriptions, selling to groups and
  organizations is **on by default**.
- If a subscription has **Family Sharing enabled**, group/org selling is
  **opted out by default** — so you deliberately choose how Family Sharing and
  group/org selling coexist.
- All of this is configurable **per subscription in App Store Connect**:
  - Enable for **both** group purchases and volume purchasing (default).
  - Make a subscription available **only on Apple School Manager** — useful for
    plans with **specific pricing for verified educational institutions**.
  - **Turn off** selling to groups and organizations entirely. The subscription is
    then unavailable for volume purchasing and in-app group purchases, but you can
    still sell it to **individuals** on the App Store.

## 3. Pricing & volume pricing (3:24)

- **Default**: every seat sells at the subscription's **current price** in App
  Store Connect.
- **Volume pricing** is a new pricing configuration for **bulk discounts**:
  - Up to **5 price bands**.
  - Full control over the **quantity threshold** and **price** for each band.
  - Configured **directly in App Store Connect**.

Worked example from the talk — discounts kicking in over 20 and over 40 seats,
which needs **3 bands**:

| Band | Seat range | Price per seat / month |
|------|------------|------------------------|
| 1    | 1–20       | $19.99 (standard)      |
| 2    | 21–40      | $13.99                 |
| 3    | 41+        | $10.99                 |

For a **50-seat** purchase, the **average per-seat cost drops ~20%** from the base
price. The point: give buyers an incentive to **cover larger groups and
consolidate purchasing**.

## 4. Purchasing (4:43)

- **Volume purchasing** — Apple Business Manager and Apple School Manager
  **display your subscriptions and handle the purchase process**. Your only job is
  to make sure the subscription is **available to organizations**.
- **Group purchases** — **you build the in-app UI** to trigger the **StoreKit 2
  purchase flow**:
  - Merchandise the value of a group purchase in your app's flows to nudge
    customers toward buying for their team/social group.
  - Collect the **number of seats** from the customer and **pass that seat count
    into the StoreKit 2 purchase request**.

## 5. Seat management (5:25)

After purchase, seats are assigned and managed:

- **Volume purchasing** — the organization assigns seats to members the **same way
  it assigns apps today**, through a **device management service**. Scales well and
  keeps seats **owned and managed by the organization**.
- **Group purchases** — an **invitation link** is generated for the initial
  purchaser to share with members.
- For **either** path, once assignments complete the **App Store assigns a
  transaction for each member**, and you grant access off that transaction.

### Included vs. custom seat management

- **Included seat management system** (default for group purchases, no
  infrastructure to build): generates the invite link, tracks **member acceptance
  and assignment**, and handles **seat lifecycle** (e.g. cancellations). You just
  start a purchase request and Apple takes it from there.
- **Custom invitation flows** — if you already have an invitation/member
  management system, leverage it via **new App Store Server API endpoints**.

### Group management endpoints

For apps with **collaborative features or shared resources** across members of the
same subscription, the **App Store Server API Group management endpoints** let you:

- Get **all the groups a single customer is in**.
- Get **all the members in a group**.

Supported for **volume purchasing** and for **group purchases using the included
seat management flows**.

## 6. Next steps (7:05)

- Make sure you're on **StoreKit 2** — it's **required** for this feature.
- Reconsider **availability and pricing strategy** (group purchases, volume
  purchasing, volume pricing) for both new and existing subscriptions.
- Add or improve **collaborative experiences** for groups and organizations.
