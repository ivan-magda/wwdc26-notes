---
title: "Enhance your presence on the App Store — Full Digest"
session: WWDC26 · 205
url: https://developer.apple.com/videos/play/wwdc2026/205/
duration: 8m
speakers: Ruhi (App Store Connect)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 205 — Enhance your presence on the App Store

## TL;DR

Apple is opening up two new visual placements on the App Store that sit *outside* your
existing screenshots and previews:

1. **Product Page Header** — the first visual element someone sees when they land on
   your product page. Images or videos, your call. This is brand/identity real estate,
   not feature screenshots.
2. **Search Results media** — replace the default screenshots in search with one
   impactful image or video so your app stands out in the list.

Both are available on **iOS 27 and iPadOS 27**, work across **all app and game
categories**, and can be reused for **Apple Ads**, **Custom Product Pages**, and
**Product Page Optimization** A/B tests.

The plumbing for all of this is a new **Asset Library** in App Store Connect: one
centralized home for screenshots, preview videos, in-app event media, and the new
**"creative assets."** The standout workflow is **standalone creative-asset review** —
get assets approved ahead of time, then **swap them live** (summer hiking → winter
hiking) with no new app submission. Automatable via the **App Store Connect API** and
the **Apple Ads Platform API**.

---

## 1. Recap — how apps appear today

Today the product page leads with **screenshots and previews** to give a glimpse of the
experience or key features, and **Search Results** show those same screenshots/previews
by default. The new placements add a layer on top of that, without replacing it.

## 2. New asset placements

### Product Page Header

- A new **Header** on the product page — the **first visual element** people see when
  they land on your app.
- Freedom to use **images or videos** that are *not* your app screenshots/previews.
- Intended for **visual identity / brand** — a compelling asset to spark interest.
- Video lets you **control the narrative** of how the app is perceived.
- Header + app icon + screenshots are meant to **work together** for a clearer picture
  of what the app is about.

### Search Results

- Instead of the default screenshots, surface **one impactful image or video** so the
  app stands out in the result list.
- Choose assets that **clearly communicate core value/features** and encourage a tap.

### Reusing the same assets elsewhere

- **Apple Ads** — use these images/videos to create ads on the **Today tab** or
  **Search Results**.
- **Custom Product Pages (CPP)** — tailor the header to match marketing visuals. The
  Yoga example: a website banner links to the app's CPP, the header reuses the same
  website visuals, and downloads **deep-link straight into the Yoga offering** — a
  consistent path from discovery to installation.
- CPP can also show a **different asset per search keyword** for more relevant results.
  Recommendation: keep the **header asset and the search-result asset consistent** so
  the page and search look the same to customers.
- **Product Page Optimization** — A/B test different visuals (logo vs. core value vs. a
  new feature) to see which your audience responds to best.

### Reach

- Works for **apps and games across all categories** — outdoor apps showing
  aspirational imagery, travel apps promoting destinations, games showcasing gameplay
  or characters.
- Available on **iOS 27 and iPadOS 27**.

## 3. Setting it up in App Store Connect — two submission flows

### Flow A — version page (the familiar one)

1. On your **version page**, upload the assets you intend to use.
2. Use the new **Preview** functionality to see how your app looks on the App Store
   with the new assets — across **iPhone and iPad**, **different orientations**, and
   **languages**.
3. Submit the version for review as you do today.
4. Once approved and released, the Product Page Header and Search Results assets go
   **live** along with the version.

### Flow B — Asset Library (new, standalone)

1. Upload your **creative assets** directly to the Asset Library.
2. Submit them for review **standalone** — *without* updating your app version and
   *without* declaring where you'll use them later.
3. Assets can be approved **either** as part of a version submission **or** through the
   Asset Library flow; once approved, all of them live in the Asset Library.

## 4. Meet Asset Library

- A **centralized place** to manage all of an app's assets across **platforms, sizes,
  and placements**.
- Includes existing **screenshots, preview videos, in-app event media**, plus the new
  marketing **images and videos** — the latter are called **creative assets** in App
  Store Connect.
- **Big advantage:** approved assets are **ready to use** across Product Page Header and
  Search Results **without additional review**.
- **Live swap example:** a Product Page Header currently shows a summer hiking asset; a
  pre-approved **winter hiking** asset is selected to replace it, publishing the change
  **directly to the App Store with no new submission**. Getting assets approved ahead of
  time gives you **real-time** control over header and search visuals.

## 5. Automation

- **App Store Connect API** — upload and submit to the Asset Library programmatically.
- **Apple Ads Platform API** — automate **ad setup** flows; ships with **open-source
  client libraries for Swift and more**.

## 6. Next steps (Apple's)

1. Prepare the images and videos that showcase your app.
2. Upload them to the **Asset Library** for use on Product Page, Search Results, and more.
3. **Preview** before submitting for review.
