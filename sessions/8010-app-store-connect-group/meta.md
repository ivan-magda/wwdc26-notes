# Session 8010 — App Store Connect Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8010/
- **Duration:** 01:01:56 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + App Store Connect / App Store panel answering upvoted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so attributions below come from self-introductions and host routing, not from the cue stream.

## Panel

Names and roles are taken from the opening self-introductions (cues 2–29). Mid-sentence first names elsewhere in the transcript are unreliable (whisper has no diarization and garbles them), so attributions in the digest lean on host routing.

- **Laurel** — host; App Store Connect engineering team. Frames App Store Connect as "the developer gateway to the App Store."
- **Jeff** — engineering program manager on App Store Connect. Favorite this week: adding in-app purchases to the enhanced submission experience.
- **Lydia** — quality engineer on App Store Connect. Favorite: localization for background assets (customers download only the localizations they need).
- **Nick** — product manager for the App Store. Favorite: the AI-related work in Xcode that lowers the bar for non-coders to build and ship a high-quality app. Fields most of the analytics questions.
- **Shobi** — Apple engineer, works on TestFlight and App Store Connect apps. Favorite: custom images and videos for product page headers and search results. (Whisper also renders this name as "Shelby" / "Shobi" mid-transcript — same person.)
- **Dave** — App Store engineering, TestFlight and App Store Connect. Favorite: the Asset Library in App Store Connect for managing images across placements.

## Description

A roughly 62-minute live, on-demand App Store Connect group lab. After a round of "favorite feature announced this week," the panel works through a long queue of upvoted developer questions covering the App Store Connect API, app submission and review, TestFlight, analytics, subscriptions and retention messaging, product pages and discoverability, universal purchase, and metadata. The throughline is practical and repeated: **prepare thoroughly before App Review** (test on a real device, exercise IAP, supply demo credentials and review notes), **keep API keys server-side and least-privilege**, and **file feedback** — the host closes by saying her team reads every feedback submission weekly.

## Key topics

- **App Store Connect API** — most-overlooked capabilities: build uploads / build delivery (end-to-end automation), TestFlight feedback APIs + webhooks (auto-file tickets), Power and Performance API (storage/CPU reports).
- **API key security** — never hardcode or ship keys; keys are server-side only; no expiration on keys; revoke compromised or stale keys; principle of least privilege; per-user keys tied to a role; users-and-access APIs to audit who is on the account.
- **App Review prep** — test on a device (not just simulator), exercise IAP in sandbox, fix crashes, supply demo/login credentials in review notes, respond conversationally to rejections instead of just resubmitting.
- **TestFlight** — internal vs external; internal groups for QA / localization / leadership; up to 10,000 external testers after beta app review; testers must use the TestFlight app (trust + privacy); groups/streams for parallel build trains; Xcode Cloud to auto-distribute by branch.
- **Retention messaging** (the "I can change, baby" feature) — analytics on impressions, cancel clicks, continued subscriptions; supports A/B of messages and plans; localization supported and recommended.
- **App analytics** — biggest update in 10+ years: 100+ new metrics around IAP/subscriptions, full download-to-renewal customer journey, new benchmarks; auto-update vs manual split available in reports (not the dashboard); source-type filtering for organic vs ads.
- **Product pages & discoverability** — product page header (image/video at the top), custom product pages, product page optimization (A/B), feature nomination to the editorial team, shareable moments for App/Game of the Day and new versions.
- **Universal purchase & metadata** — name/subtitle/age rating/genre shared across platforms; description differs per platform; cannot share one product name across separate bundle IDs (unchanged); start with iOS and expand to Mac/visionOS.
- **Submissions this year** — enhanced submission experience now covers in-app purchases; Game Center entities (leaderboards) can be submitted; offer code APIs now cover consumables and non-consumables.
- **StoreKit 2 / migration** — `AppTransaction` original-app-version to honor v1 purchases when moving paid → freemium; sandbox via TestFlight or App Store Connect sandbox accounts.
- **Geospatial / location-dependent apps** — include a screen recording with the submission; use beta app review via external TestFlight.

## Related sessions (referenced on-air)

The panel pointed mostly to ongoing resources rather than numbered sessions. No WWDC session numbers were stated on-air, so none are listed here.

- Apple Developer **pathway** for getting a first app on the store (referenced, not numbered).
- **"Getting Started with TestFlight"** tech talk (referenced by title, not numbered).
- App Store Connect **help guides** and **developer.apple.com** documentation/videos (referenced generally; the panel noted a 2022 WWDC session is still useful but did not name it).
- New **analytics guide + video** for the spring App Analytics update (referenced, not numbered).
- Closing pointers: **developer.apple.com/forums** and **feedbackassistant.apple.com**.

## Chapter arc (no published chapters; rough timestamps from the transcript)

- **00:00–02:42** — Intros: host + five panelists, each naming a favorite feature announced this week.
- **02:42–04:33** — App Store Connect API: most-overlooked capabilities (build uploads, TestFlight feedback + webhooks, Power and Performance API).
- **04:33–06:02** — Accessibility nutrition label edge case (a game with no sound / no captions) and app description / preview best practices.
- **06:02–08:32** — Common subscription-app rejection reasons and how to prep and respond to App Review.
- **08:32–11:30** — Retention messaging analytics; API key security mistakes and key hygiene.
- **11:30–17:00** — Auto vs manual update analytics; scoping API keys to a few apps (per-user keys); universal-purchase metadata (name/subtitle vs description); retention-message localization.
- **17:00–22:28** — App description copy density; internal vs external TestFlight best practices and internal groups.
- **22:28–30:29** — API key vending best practices (reprise); shared product name across bundle IDs (still not possible); first-app prep with auth + third-party keys; paid → freemium migration and purchase restoration (StoreKit 2 / sandbox).
- **30:29–36:03** — How to publish your first iOS app (pathway, guides, TestFlight tech talk); increasing app visibility; how many binaries a universal app needs.
- **36:03–44:21** — Review notes language; new App Store Connect workflows worth revisiting (100+ analytics metrics, Game Center submission, offer code APIs, build delivery); common rejection reasons; validations that only run after upload and the push to surface them earlier.
- **44:21–50:22** — TestFlight build streams/groups for parallel development (Xcode Cloud); price-change propagation delay; merging country-specific app listings into one.
- **50:22–57:36** — Deep dive on the new App Analytics dashboard (customer journey, benchmarks); feature nomination tips; least-privilege roles for third-party tools (Sales role); API vs UI data freshness (last 24h).
- **57:36–59:19** — Measuring organic search CPP impact (source-type filtering); seeing phased-release rollout progress while prepping a new version.
- **59:19–01:01:56** — Final question: geospatial AR game (Shakespeare in Stratford-upon-Avon) and reviewing location-locked apps; wrap-up, forums + Feedback Assistant pitch.

## Code

See `code.md` — no Code tab for this session; this lab is verbal Q&A. API and feature names are captured in `digest.md`.
