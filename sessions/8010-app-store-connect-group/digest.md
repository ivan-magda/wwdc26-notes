---
title: "App Store Connect Group Lab — Full Digest"
session: WWDC26 · 8010
url: https://developer.apple.com/videos/play/wwdc2026/8010/
duration: 01:01:56
sources: transcript.md (whisper.cpp ggml-large-v3, session SD video audio), meta.md
compiled: 2026-06-13
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from the SD video; no speaker diarization, so attributions below come from self-introductions and host routing, not from the cue stream. Asker handles are whisper renderings of forum usernames and may be misspelled.
---

# WWDC26 · 8010 — App Store Connect Group Lab

> A ~62-minute live developer Q&A with a six-person App Store Connect / App Store
> panel. Not a feature talk: it's the practical layer under the WWDC26 submission and
> analytics announcements. The same handful of answers recur because the same handful
> of mistakes recur: test on a real device before App Review, supply demo credentials,
> keep API keys server-side and least-privilege, and use TestFlight before you ship.
> The host closes by promising her team reads every Feedback Assistant submission, weekly.

## TL;DR

- **Enhanced submission now includes in-app purchases.** The "enhanced submission
  experience" finally brings the app, its content (in-app events), and its in-app
  purchases into one place: control what gets submitted and reviewed together, with
  IAP-specific App Review messaging. Called out repeatedly as the panel's headline change.
- **The biggest App Analytics update in 10+ years shipped this spring.** Over **100 new
  metrics**, mostly around in-app purchase and subscriptions, stitched into a full
  **download → purchase → renewal customer journey**, plus **benchmarks** that flag where
  you over- or under-perform. The data is in the API, but the **visualizations are
  web-UI-only**. Start with the benchmarks: they give you a roadmap of what to fix.
- **API keys are server-side only, and they don't expire.** No expiration date unless you
  revoke. Never hardcode keys in a repo or ship them in a client. Revoke compromised or
  stale keys, scope third-party keys tightly, and audit account access with the
  users-and-access APIs. A **team key reaches all apps**; you can't scope it to a subset.
  The workaround is a **per-user key** tied to that user's role and app access.
- **Auto-update vs manual-update split exists — but only in reports, not the dashboard.**
  Download the analytics report and read the column. Likewise, the **last-24-hours** view
  is **UI-only** (Sales and Trends); the API updates on a **daily cadence**, so it lags.
  Same data, different freshness.
- **Retention messaging** (the panel's running "I can change, baby" gag) ships with full
  analytics: impressions, cancel clicks, continued subscriptions, plus A/B of messages and
  plans. **Localization is supported and recommended** so the cancel flow matches the rest
  of the app's language.
- **You can't share one product name across separate bundle IDs — still.** Name, subtitle,
  age rating, and genre are shared *within* a universal-purchase record; **description is
  per-platform** (omit NFC on tvOS, keep it on iOS). The path Apple recommends is merging
  onto one record via Universal Purchase, not chasing a shared name across bundle IDs.

## Panel

Host **Laurel** (App Store Connect engineering) with **Jeff** (engineering program
manager, App Store Connect), **Lydia** (quality engineer, App Store Connect), **Nick**
(product manager, App Store — takes most analytics questions), **Shobi** (Apple engineer
on TestFlight and the App Store Connect apps; whisper also renders this name as "Shelby"),
and **Dave** (App Store engineering, TestFlight and App Store Connect). Roles are from the
self-introductions; mid-transcript first names are unreliable.

## Warm-up: favorite feature announced this week (00:36–02:42)

Each panelist named a favorite developer feature:

- **Jeff** — **in-app purchases in the enhanced submission experience.** "A long time
  coming": submit and review the app, its content, and its IAPs together, with messaging
  in one place.
- **Lydia** — **localization for background assets.** Developers add localizations;
  customers download only the ones they need.
- **Nick** — the **AI work in Xcode**, which lets someone who "maybe doesn't even know how
  to code very much" build a high-quality app and ship it.
- **Shobi** — **custom images and videos for product page headers and search results**, to
  boost an app's presence on the store.
- **Dave** — the **Asset Library in App Store Connect**, for managing images and reusing
  them across placements.

## Developer Q&A

### App Store Connect API: overlooked capabilities & this year's additions

- **Most-overlooked APIs developers should be using (E. Dorphy — recurring API asker;
  spelling unclear).** Top pick: **build uploads / build delivery** (shipped last year),
  which lets you automate the whole loop: upload a build, make it available to TestFlight
  testers, pull feedback via the public **TestFlight feedback API**, act on it, upload
  again. Combined with **webhooks**, developers can auto-generate tickets in their own
  systems, "something developers have wanted for a long time." Also under-used: the
  **Power and Performance API** for downloadable reports on how apps behave on-device
  (storage, CPU usage).
- **New workflows worth revisiting even with mature automation (E. Dorphy again).** If
  you live in the API you may be missing the web UI: the **100+ new analytics metrics**
  (data is in the API, but the visualizations are web-only). **Game Center entities**
  (leaderboards and so forth) **can now be submitted**. **Offer code APIs** are now
  available for **both consumables and non-consumables**. Last year's **build delivery
  API** lets teams move build submission off **Transporter**. How the APIs interact with
  IAP "will evolve."
- **Common security mistakes in API integrations (E. Dorphy).** Honest answer: "I don't
  feel like we see that many" — developers manage keys well. The advice that matters:
  don't hardcode keys into a repo, don't ship them in clients, manage the private key
  responsibly, and **revoke** if compromised or no longer needed. Be extra careful with
  **third-party integrations**: minimal permissions, don't over-share, revoke when you stop
  using the service. This bleeds into **user management** — the riskiest moment is when
  people join or leave the app. Use the **users-and-access APIs** to monitor who's on the
  account. (Dave led; panel agreed.)
- **Vending keys to tooling / using them in apps and scripts (E. Dorphy).** Keychain,
  least privilege, limiting app access — the asker already had the right instincts.
  Reinforced: **keys belong on your server only**, "we'll keep repeating that," because
  it's tempting to drop one into an app and let it make all kinds of calls. **No expiration
  date** on keys, so they last a long time. For a **team key**, think about its role — an
  **admin key can do quite a bit**; a read-only key is safer. Use throwaway **individual
  keys** while you're figuring out how the API works before committing a long-lived team key
  to a server.
- **Least-privilege setup for a third-party tool that takes users' ASC API keys (D.
  Robinin).** The asker noted there's no read-only, analytics-only, all-apps scope. The
  recommended low-privilege roles: the **Sales role** gives the lowest level (can't make
  the changes the asker worried about); the **Developer role** can upload builds and use
  internal TestFlight; the **Marketer role** can edit metadata, product page, and in-app
  events but **can't release** or ship a new version. Pick the role that matches what the
  key is for.
- **Fetching live / last-24-hours data via the API (D. Robinin).** Confirmed: **no
  API-vs-UI parity** for freshness. The **UI** (Sales and Trends) shows the **last 24
  hours**; the **API** fetches a day-old snapshot or weekly/monthly aggregates and updates
  on a **daily cadence**. Same data, different cadence — if you need the freshest numbers,
  use the UI. The panel scoped this to **sales/analytics data specifically**; other App
  Store Connect data updates piecemeal. (The panel acknowledged this reads as a feature
  request.)
- **Scoping a key to only a few apps from CI (Jonathan889).** Correct that a **general
  team access key reaches all apps**, and you **can't change which apps a general-access
  key sees**. The workaround: a **per-user key** tied to that user's role and app access
  (for example a marketing user with access to only a couple of apps). A real caution
  raised: in large companies, that per-user key is easy to forget — when that person leaves
  and loses access, automated systems silently break. Test it before depending on it.

### App Review: preparation, rejections, and the response loop

- **Why subscription apps get rejected on first review, and how to recover fast (Claire
  KCS).** Preparation is the whole game. **Test on a device the way users will**, exercise
  the functionality, fix crashes, verify **IAP** works, and make sure your **backend is
  production-ready**. Use the **App Review notes** field to tell reviewers how to use the
  app, and **include login / demo credentials** if anything sits behind a paywall — "some
  people forget that," and there's a dedicated section for it. Test IAP in the **sandbox**
  via Xcode and TestFlight first. To recover quickly from a rejection: you get a detailed
  message from App Review; **write a response and have a dialogue** rather than just
  resubmitting an update — explain how the app works and ask clarifying questions. The new
  **enhanced IAP submission flow** gives clearer App Review communication when something's
  wrong with a purchase.
- **Most common reasons apps get rejected (Artook... — handle unclear).** Same root cause:
  **not being ready for App Review.** Bugs and crashes (test on a real device, not just the
  simulator), incomplete information, missing demo credentials and review notes. Read the
  **App Review Guidelines** — there are **section-specific rules** for kids' apps (ages 4+
  gets a closer look), crypto, and other categories — and hit them before submitting. Keep
  the **feedback loop conversational**: answer the reviewer's questions, test bug fixes,
  and ask about anything unclear. A recurring theme: solo and brand-new developers can do
  almost everything at their desk, but **testing needs other people** — "talk to your
  friends, talk to your family," use **TestFlight public links** to cover corner cases the
  simulator and your one device won't.
- **First app with user auth + third-party API keys: what to get right (Alsant — handle
  unclear).** Thoroughly test the app and the third-party functionality; make sure your
  **services are ready for production scale** before submitting. If "third-party API keys"
  means **your users' keys**, App Review wants to see you **uphold privacy** when handling
  other people's keys. And, again, supply **demo account info** so reviewers can launch and
  exercise the app. (Jeff led.)
- **Review-notes language: English or native? (Lee Shuang Quan — spelling unclear).** Not
  required to be English. App Review has a **large set of language skills**, and there's a
  good chance a reviewer can be matched to your primary language. English "may not hurt" but
  is "definitely not an absolute requirement." The flat statement: **developers do not need
  to speak English to release on the App Store.**

### TestFlight: internal vs external, groups, and sandbox

- **Best practices for internal vs external testing (Hello Universe Help — handle
  unclear; routed to Shobi).** Start with **internal TestFlight**: internal people added as
  App Store Connect users, invited by email — ideal for a QA team getting regular builds.
  Once the app holds up internally, move to **external TestFlight**, which goes through
  **beta app review** (to keep content safe and trustworthy for testers), then opens to up
  to **10,000 testers** via a link. Finding external testers "isn't that easy" (a panelist
  pushed back on the "very easy" framing): post in communities where your app is relevant
  (e.g. gardening forums for a gardening app) and look for honest feedback, not sales.
  Testers must install the **TestFlight app** — that's the channel that gives testers the
  trust and privacy guarantee. Internally, use **separate groups** for QA (auto-receive
  every build), localization (selected builds), and leadership (a careful, specific build
  before going external). Apple itself ships every build to its own teams through internal
  TestFlight: "we test TestFlight through TestFlight."
- **Organizing builds into streams/groups for parallel development (Jonathan889).** Yes:
  **groups** in both internal and external TestFlight are organizations of testers + which
  builds they receive. Testers only see builds in their group. Run a **2.0 feature group**
  and a **1.1 bug-fix group** in parallel, each with its own builds and testers — you don't
  have to upload 2.0 every time. A long-horizon "3.0" effort can be its own group while 1.x
  and 2.x continue. A big part of this is your own **versioning and source-control
  discipline**; once builds land in TestFlight you select and match them to the right group.
  **Xcode Cloud** can automate the whole thing: wire GitHub branches (e.g. 2.0 vs 1.5) to
  auto-distribute to the matching groups.

### Subscriptions, retention messaging & migrations

- **Retention-message analytics (Brew Install Poppy — handle unclear; routed to Nick).**
  The retention messages API lets the app intercept a user about to cancel. ASC **will show
  the data**: how many saw the retention page, how many clicked cancel, how many stayed
  subscribed. You can **A/B different messages** and **offer different plans**. Framed as a
  way to win back users who haven't opened the app in a while.
- **Does the retention workflow support localization? (E. Dwarfy — same recurring asker,
  spelling unclear).** Yes, localization is supported, and you **should** localize to match
  the app's localizations — users in some regions report a "disjointed experience" jumping
  from a fully translated app into an English cancel flow.
- **Paid v1 → freemium v1.1, honoring previous purchases (Beloved Melody — handle
  unclear).** Use **StoreKit 2's `AppTransaction` API** to read the **original app version**
  a customer purchased, then grant service accordingly. Test in the **sandbox** via Xcode
  first, then **TestFlight** (default sandbox environment). A candid limitation surfaced
  mid-answer: **TestFlight won't actually test the paid-app purchase transition** — you can
  see which version you're on and confirm you're reading the original-version value
  correctly, but to exercise the paid→freemium upgrade you'd download the **real App Store
  paid version** on one account, then upgrade to the freemium test build. You can also
  create **sandbox accounts in App Store Connect** (set different countries, reset
  purchases) for more complex IAP testing beyond TestFlight.
- **Why a few-day delay on price changes, even for price drops? (Justin from
  Baldwinsville).** Not a policy gate: it's about making the price **go live everywhere at
  the same time**. "Based off the way the earth turns and the sun rises," the delay gives
  the change time to propagate and to line up across the world's time zones.

### App listings, metadata & universal purchase

- **Different name/subtitle per target on a universal-purchase app, e.g. omit NFC on
  tvOS? (LazyVar).** **Name and subtitle are shared** across all platforms in the record —
  and so are **age rating** and **genre** (the logic: once you have it on one Apple One
  platform you can get it on the rest, so the shared fields are "what makes your app your
  app"). But the **description is per-platform**: the tvOS description can drop NFC while
  the iOS one keeps it. That's the right place for platform-specific functionality copy.
- **Same product name across separate bundle IDs (Mac vs Universal iOS+visionOS) (Josh
  D.).** **Still not possible**, unchanged. Recommendation: add the Mac app to the iOS /
  visionOS record and use **Universal Purchase** to get the shared experience plus other
  benefits. Naming uniqueness matters more now that an **iPad app can be downloaded on
  Mac** — you need to disambiguate. Apple is "building to benefit universal purchase apps"
  and expects more Xcode features supporting **unified development on one bundle ID**.
- **How many binaries does a universal app need? (Jeff Bash — handle unclear; routed to
  Lydia).** Not the full set — it depends which platforms you distribute to (and on Apple
  Silicon eligibility, which can give you some platform reach for free). You want **distinct
  metadata per platform** and at least an iOS binary, a macOS binary, a watchOS binary as
  applicable. You can **start with one and add others when ready**: take an iOS app to Mac
  or visionOS first, then later build a platform-native version that revs independently. The
  honest summary: "It depends."
- **Merging country-specific listings into one (panel-posed).** For an app split as
  per-country builds (e.g. a US app and a Japan app with slightly different features): pick
  the **flagship app to persist**, update the others to a **final release** that tells users
  the app is moving and to download the new one, give users time to migrate, then eventually
  do a **forced-upgrade** wind-down. Apple did this itself this year, consolidating into
  **Creator Studio** (and historically with Numbers/Pages). Don't delete the old code right
  away — you may need a security or bug fix for stragglers. Track the migration with
  **sessions-by-app-version** analytics.

### Discoverability, product pages & featuring

- **App description best practices / dense copy (SixCupsOfCoffee).** Keep the **user front
  of mind**; stuffing keywords can make the app less enticing to someone evaluating it. Use
  **custom product pages** to drive different audiences to different copy, and **product
  page optimization** to A/B-test whether a description is too verbose. The new **product
  page header** (a compelling image or video at the very top) is the first thing people see;
  "a picture is worth a thousand words," so let the visual carry meaning and keep the
  description more factual.
- **Signaling accessibility when the nutrition label doesn't fit (D. Fabulich — spelling
  unclear).** A game with **no sound** can't honestly claim captions, yet is fully
  accessible to hearing-impaired users. The panel admitted this is **genuinely hard** —
  "it's hard for us to know exactly what is the right accessibility question to answer for
  someone else's app." Guidance: don't claim a feature you don't have; answer the labels as
  best you can; and use **other surfaces** (the **app description** and **app previews**) to
  tell specific audiences directly that the app is accessible to them.
- **Increasing app visibility (Rustam06).** Lead with **creative assets** (more
  screenshots, previews). Then the organic toolkit: **in-app events**, **custom product
  pages tied to keywords** for relevance, and **feature nomination** to the **editorial
  team** (a strong app can land on the **Today tab**). With the App Store Connect app, **App
  of the Day / Game of the Day** generates a **shareable moment** card to advertise — and you
  can also create a shareable moment for a **new version**. Drive traffic from off-store
  channels back to the store.
- **Tips for a successful feature nomination (Drummer — handle unclear).** Be
  **interesting and ideally novel** — crowded categories are less likely to get nominated.
  **Great art** separates you from the crowd (the new product-page assets feed the featuring
  placement too). Nominations aren't only for first launch — nominate **updates**,
  roadmaps, and the **story behind the app** (sometimes featuring is about the **developer**
  themselves). You can **try more than once** if you don't get it the first time.

### App analytics deep dive

- **Auto-update vs manual / organic update split (Stigler Farmers — handle unclear).**
  Yes — but the breakdown lives in **reports only**, not the dashboard. Download the
  analytics report and read the **auto-vs-manual column**.
- **The new App Store dashboard / analytics (S. McCoy — handle unclear; routed to Nick).**
  The **biggest App Analytics update in 10+ years**: **100+ new metrics**, mostly IAP and
  subscriptions, tying **App Store search / Browse / Today-tab** acquisition through to
  later subscription purchases so you can see the **full customer journey** (download →
  convert to paid → continued payment → renewal). Visualizations show conversion percentages
  over time so you can spot **soft spots** and target them. New **benchmarks** show whether
  you're over- or under-performing; the panel advises **starting with the benchmarks**
  because they hand you a roadmap. There's a **new guide (with images) and a video**. "If
  you're a subscription app or using IAP, this one's for you."
- **Measuring organic-search CPP impact without keyword-level data (Edward Quong —
  spelling unclear).** Confirmed: **no keyword-level data in ASC.** Workaround: open the CPP
  in **App Analytics**, add a **filter by source type**, and select **App Store Search**.
  If you run **no Apple ads**, that's purely organic; if you run ads it's blended, so
  consider **two custom product pages** (one for ads, one for organic) to tease the
  difference apart.

### Submission validation & release management

- **Validations that only run after upload — can more move into Xcode / local pre-upload?
  (Nanachi — handle unclear; routed to Lydia).** Apple **validates throughout the
  pipeline** and is "always looking at ways to get feedback as early as possible." Can't
  enumerate every validation ("there are a lot"), but the intent is to surface issues
  earlier. **Warnings** (not just errors) already help, and you can **validate your app in
  Xcode before delivery**. The honest framing: "we're always trying to move things further
  up the development workflow… there's probably more that could be done." And **upload
  frequently** — continuous delivery surfaces issues sooner.
- **Seeing rollout progress of the current release while prepping the next (Jonathan889).**
  This is about **phased release**: in App Store Connect, open the **prepare-for-distribution
  version**, click into the **phased release rollout**, and see which day of the phased cycle
  you're on. You can have **up to two versions per platform** (the ready-for-distribution one
  that's rolling out, plus the next one you're building), so you can watch the older version's
  phased release while working on the new one.

### Getting started

- **Step-by-step to publish your first iOS app (Hello Universe Help — handle unclear).**
  The developer website has **tutorials, videos, documentation, and WWDC sessions** (older
  ones still useful — a panelist noted watching a video that referenced a **2022 WWDC
  session**). There's an Apple Developer **pathway** for first-time submission, **detailed
  App Store Connect help guides** for every page and action, and a **"Getting Started with
  TestFlight" tech talk** that walks build → test → submit → live. All on
  **developer.apple.com**.

### Final question: geospatial / location-locked apps

- **Reviewing a geospatial AR game tied to one place (Louis Courtney — a Shakespeare AR
  game in Stratford-upon-Avon).** Building a full working demo for review "will be
  incredibly difficult, if not impossible." Answer: if review must happen in a **specific
  location**, you can **include a screen recording** with the submission. But also build in
  **functionality for users outside that location** (not everyone visiting Stratford lives
  there). And use the **whole TestFlight pipeline** — internal then external — because
  **beta app review** gives you early signal on whether the geo experience passes. A
  panelist asked to be told if the game ships playable outside Stratford so they can play it.

## Unconventional facts & takeaways (the live-Q&A gold)

- **API keys never expire.** Stated plainly: there's no expiration date on App Store Connect
  API keys, so a forgotten key lingers indefinitely — revocation and key hygiene are the
  only controls.
- **A team key can't be scoped to a subset of apps.** General-access team keys reach **all**
  apps; the only narrowing mechanism is a **per-user key** tied to that user's role and app
  access — with the trap that it breaks when that user loses account access.
- **API freshness genuinely lags the UI.** The **last-24-hours** sales/analytics view is
  **UI-only**; the API serves day-old snapshots or weekly/monthly aggregates on a daily
  cadence. Same data, different cadence, and the panel conceded it reads as a feature request.
- **TestFlight can't test the paid→freemium purchase transition.** A candid limitation: to
  exercise honoring a v1 paid purchase you must download the **real App Store paid version**
  and upgrade to the freemium build; TestFlight only confirms you're reading the right
  original-version value.
- **The auto-update vs manual split is reports-only**, not in the dashboard — easy to miss.
- **Apple eats its own dog food:** every internal build ships through **internal TestFlight**
  ("we test TestFlight through TestFlight"), and Apple consolidated its own duplicate apps
  into **Creator Studio** this year using the same listing-merge playbook it recommended.
- **The price-change delay isn't policy** — it exists so a price goes live worldwide at the
  same wall-clock moment across time zones.
- **"Developers do not need to speak English to release on the App Store."** App Review
  matches reviewer language skills to your primary language; English review notes are
  optional, not required.
- **Accessibility-label honesty over completeness:** the panel openly couldn't fully answer a
  no-sound-game captions case and fell back on "don't claim what you don't have; use the
  description and previews."
- **Cultural color:** the running **"I can change, baby"** joke for the retention-message
  feature (and the matching advice to localize even the joke); the recurring **E. Dorphy**
  API asker the panel kept welcoming back; the host's closing promise that her team reads
  **every** Feedback Assistant submission **weekly**.
