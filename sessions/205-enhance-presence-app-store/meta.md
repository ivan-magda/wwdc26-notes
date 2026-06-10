# Session 205 — Enhance your presence on the App Store

- **URL:** https://developer.apple.com/videos/play/wwdc2026/205/
- **Duration:** 8m
- **Speakers:** Ruhi (engineering manager, App Store Connect team)

## Description

New ways to use images and videos to shape the first moments someone spends with
your app on the App Store. Two new placements arrive: a **Product Page Header** (the
first visual element on the product page, where you can use brand imagery/video
beyond screenshots) and **Search Results** media (an impactful image or video instead
of the default screenshots). Both are managed through a new centralized **Asset
Library** in App Store Connect, with a Preview flow and a standalone "creative assets"
review path that lets approved assets be swapped live without a new submission.

## Key topics

- **Product Page Header** — new top-of-page placement for images or videos, separate from screenshots/previews; express brand/visual identity
- **Search Results media** — replace default screenshots with an impactful image or video to stand out
- Reuse these assets for **Apple Ads** (Today tab / Search Results), **Custom Product Pages**, and deep-linking from website → CPP → in-app offering
- **Product Page Optimization** (A/B test visuals) to learn which assets convert best
- Available on **iOS 27 and iPadOS 27**
- Two submission flows: (1) familiar **version page** flow with new **Preview** (per device, orientation, language); (2) new **Asset Library** standalone "creative asset" review
- **Asset Library** — one place for screenshots, preview videos, in-app event media, and new "creative assets" across platforms/sizes/placements
- Pre-approved assets can be **swapped live** (e.g. summer → winter hiking) with no additional review
- Automation: **App Store Connect API** for upload/submit; **Apple Ads Platform API** for ad setup (open-source Swift client libraries)

## Related sessions to fetch

- [ ] (No other WWDC sessions explicitly named in this talk)

## Chapter summary (Summary tab)

- **0:06 Introduction** — how images and videos shape the first moments on the App Store; what's changing — new placements on the product page header and search results, and how to set them up.
- **0:52 New asset placements** — Product Page Header and Search Results placements let you use images and videos beyond your screenshots — the first visual element people see — to express your brand and shape how your app is perceived.
- **4:24 Meet Asset Library** — a centralized place for all your app's assets — screenshots, preview videos, in-app event media, and new creative assets — across platforms, sizes, and placements.
- **7:35 Next steps** — prepare the images and videos that showcase your app, upload them to Asset Library for use across product page and search results, and preview before submitting for review.

## Code

See `code.md` — this session has no Code tab (it's a product/process walkthrough). The only APIs named are the App Store Connect API and the Apple Ads Platform API.
