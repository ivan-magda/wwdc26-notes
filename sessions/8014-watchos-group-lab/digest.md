---
title: "watchOS Group Lab — Full Digest"
session: WWDC26 · 8014
url: https://developer.apple.com/videos/play/wwdc2026/8014/
duration: 01:00:06
sources: transcript.md (whisper.cpp ggml-large-v3), meta.md
compiled: 2026-06-13
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and host routing. Names that surface only mid-sentence are treated as unreliable; garbled API/feature names are flagged "(transcript unclear)."
---

# WWDC26 · 8014 — watchOS Group Lab

> A roughly one-hour live developer Q&A with a five-person panel: a Worldwide
> Developer Relations host and four watchOS engineers and managers covering
> workouts, frameworks, widgets/live activities, and system spaces. It is the
> unscripted layer underneath the WWDC26 watchOS sessions: PCC-vs-on-device
> clarifications, the constraints that define the platform, what's actually new in
> watchOS 27, and a steady ask to **file feedback with concrete use cases**.

## TL;DR

- **Foundation Models on the watch always hits the network.** It does **not** use the iPhone's on-device model, even on the same Wi-Fi network. It calls **Private Cloud Compute** (or any `LanguageModel`-conforming provider) over cellular or Wi-Fi. You don't need to be tethered to your iPhone, but you do need a network connection. Plan for availability checks, token/quota fallbacks, and network latency.
- **Official `LanguageModel` support for Claude and Gemini is "coming soon"** from those vendors; you can also conform to the protocol yourself. Either way it's a network call, never on-device on the watch.
- **Watchdog timeouts are strict by design.** The familiar iOS toolbox (NSURLSession, CloudKit, Swift async) works on the watch, but the system enforces tight runtime limits to protect all-day battery, and the core count is lower. Treat the constraints as a design problem, not an annoyance.
- **Xcode 27 + Device Hub debug directly to the watch.** Starting in Xcode 26 and continued in 27, the Mac connects **directly to the watch** instead of proxying through the phone, with "substantial improvements in both reliability and throughput." Recent Apple Watch hardware has a **5 GHz Wi-Fi chip**; make sure peer-to-peer is enabled on your network (corporate networks often block it). The Xcode team reworked this area and explicitly wants feedback + logs.
- **SceneKit is deprecated but not gone; RealityKit is not on watchOS.** The recommended modern path for 3D on the watch is **SwiftUI Canvas**, which runs on the GPU.
- **Liquid Glass is mostly automatic on watchOS 27.** Apps already on the watchOS 10 design language pick up the refinements (darker speculars, better scroll legibility, performance gains) for free. There is **no clear/tinted glass slider on the watch** because the UI is primarily dark; standard accessibility toggles (reduce transparency, increase contrast) are available.
- **You can have a watch presence with no watch app.** Live activities come to the watch automatically and can be customized for the Smart Stack; controls from your iPhone app are surfaced on the watch automatically with no code.
- **Two notable "new this year" widget wins:** **watch-connectivity-based widget refresh** (new in watchOS 27) joins last year's **APNS-based refresh** (watchOS 26), giving more paths to keep widgets fresh.
- **Workout zones is the long-requested "hidden gem."** Heart rate zones and cycling power zones with rich time-in-zone data, usable by non-workout apps too. Also called out: the new SwiftUI **reorderable** API, used internally to build Control Center, lets developers reorder containers on watchOS for the first time.

## Panel

Host **Leah Wommelsdorf** (Worldwide Developer Relations) with four watchOS team members:

- **Dan Keene** — manages the watchOS workout team.
- **Anne Hitchcock** — watchOS frameworks engineer (system spaces / system UI, connectivity; collaborated with the Foundation Models and App Intents teams this year).
- **Devin** — widgets and live activities, and their integration with system spaces (Smart Stack, watch faces). Introduced by first name only.
- **Matthew Koontz** — manages the watchOS system spaces team (app launch, notifications, look and feel).

There is no separate warm-up round; introductions flow straight into the Q&A. The host frames the year up front: new frameworks reach the watch (Foundation Models, Vision framework, and a Core AI framework — name transcript-unclear), all on watchOS 27, plus Liquid Glass updates and new HealthKit APIs including a menopause API and workout zones.

## Developer Q&A

### Foundation Models on the watch

- **Does it run on the connected iPhone or via PCC, in a standalone watch app? (Emcro).** It does **not** run on the device and does **not** require a connection to your iPhone, but it **does require a network connection**. You can use Private Cloud Compute or anything conforming to the `LanguageModel` protocol. Official `LanguageModel` support for **Claude and Gemini is coming soon** from those vendors; you can also conform to `LanguageModel` yourself. Either way it's a network call. Great watch use cases called out: summarizing text to fit the small display, and turning dense text into more accessible text. (Anne, with Matthew adding excitement about the health-data angle.)
- **Onboarding and network fallbacks (host follow-up).** There are APIs in Foundation Models to **check whether it's available right now**, so check before making calls. If you use PCC, be aware it needs an **entitlement** (mentioned in the referenced session). Plan fallbacks for: feature unavailable, quota reached, token usage on a provider. Design with graceful degradation, especially on a non-cellular watch. (Anne)
- **Same-network on-device model? (Durkio).** No. It does **not** use the iPhone's on-device model even on the same network. It uses **PCC over the network** (cellular or Wi-Fi), or another `LanguageModel`-conforming provider, but always over the network. You always pay network latency, so think hard about **what** you call for, **when**, and **how many** calls you make, because every call is a network hop. (Anne)
- **Design fundamentals for the wrist (host thread through this section).** Whether or not an app uses Foundation Models, watch apps should be **glanceable, quick, concise, easy to digest**, and **personal/intimate** (you look at your wrist and put it down). Foundation Models can help make the content shown feel personal. It's still a watch with real power and performance constraints. File feedback and use the Apple Developer Forums. (Matthew, Devin, host)

### iOS-to-watchOS transition and background work

- **Architectural paradigms / hidden pitfalls for heavy off-main-thread work like CloudKit syncing, without iOS habits? (LazyVar).** Much of what you already use works: **NSURLSession, CloudKit, off-main-thread Swift async**, plus reusable Swifty code. The key difference: the platform enforces **strict rules** so everything running at once stays a good citizen and preserves all-day battery. Expect **watchdog timeouts**; they're strict for good reasons. Reason through background runtime like you do on phone, but know the timeouts are tighter. (Matthew)
- **Constraints as opportunity (Dan adds).** The watch has not just a smaller screen but **lower core count**, so be cognizant of work you kick off. Constraints are "an intellectual opportunity," and a watch app is often a different design problem than an iOS or macOS app. The panel points to a watchOS 10-era design video ("Design and build apps with watchOS," approximate title) for designing great watch experiences instead of porting an iOS layout.
- **Widgets/live activities tailored to the watch (Devin).** Coming from iOS there's a lot of similarity. The **runtime you get for widgets on watchOS is the same as iOS** apart from the smaller core count, so widget code typically transfers cleanly. An existing **lock screen widget should work out of the box** on watchOS, maybe with small layout tweaks. The watch-specific opportunity: **Smart Stack**, where the **widget relevance API** (heavily exercised on watchOS) gets your widget suggested at the right moment.
- **Health data opportunities unique to the watch (Dan).** The watch records a lot about you at the time it happens: **heart rate, calories**, etc., with the watch as the primary source. Surfacing heart rate creatively, even outside fitness contexts ("your heart rate was really high during this time"), is an opportunity tied to the personal/intimate nature of the device.

### Watch presence without a full app

- **How should an app on other platforms get onto the watch first? (LazyVar follow-up).** Two low-effort entry points:
  - **Live activities** come to the watch **automatically**; they show up on the Smart Stack as glanceable interactions, and you can **customize** them for a great look with **no watch app at all** (just test/validate).
  - **Controls** (since watchOS 26): controls from your iPhone app surface in the watch Control Center **automatically with no developer work and no customization** (the example given was unlocking a car). The system picks controls that make sense to bring to the watch.
  - The reasoning: spaces like the lock screen and Control Center already exist on the watch, so meeting users where they already are is a strong on-ramp. (Matthew, Devin)

### Tooling and debugging

- **Speeding up physical watchOS debug loops, minimizing Xcode install delays and system-symbol sync? Can we force a local Wi-Fi tunnel or pre-cache symbols? (LazyVar).**
  - **Be nearby and ensure good Mac connectivity; keep the OS up to date; install Xcode 27.** The Xcode team put significant work into this over recent releases and added diagnostics; if you still hit issues, **file feedback with logs**. (Dan, Anne)
  - **Device Hub gives a direct Mac-to-watch connection** (Xcode 26 onward, continued in 27). Previously the connection proxied through the phone; going direct yielded "substantial improvements in both reliability and throughput." (Anne)
  - The asker's instinct about Wi-Fi is right: Apple is using a direct Wi-Fi connection. Be on a network where **devices can talk to each other (peer-to-peer enabled)** — corporate networks sometimes block this. **Recent Apple Watch hardware has a 5 GHz Wi-Fi chip** that helps throughput. (Anne)
  - Still **test across the full range of Apple Watches** your users own (not just the latest); **Device Hub** is a good way to validate across hardware configurations. (Anne)

### Favorite watchOS 27 features (PearlJam66)

A round-robin of favorites:

- **Workout buddy insights** (Dan): personalized insights like speed over time vs previous weeks/months/years; the asker's example was cycling getting better being "uplifting."
- **Foundation Models on watchOS** (Anne).
- **Updating widgets with watch connectivity** (Anne): emphatically called out as new and long-awaited, lets the iOS app and watch companion stay in sync and push to the wrist.
- **Widget performance/reliability improvements and new Smart Stack suggestions** (Devin): better, more timely widget loads and more update runtime; example of a handy new suggestion was a reminder of a friend's birthday; delightful holiday/significant-event surprises in the Smart Stack.
- **Siri AI / new intelligence features** (Matthew): "not out in beta one, but something that will be coming."
- **HealthKit updates** (host): heart rate zones, cycling power zones, and the perimenopause/menopause API.

### 3D rendering after SceneKit (Zev Eisenberg, NYT)

- **SceneKit was deprecated in watchOS 26 and RealityKit isn't on watchOS — what's the modern way to render 3D? (Zev Eisenberg).** SceneKit is **deprecated but not gone**, so you may keep using it. The recommended path forward for the watch is **SwiftUI**, specifically **SwiftUI Canvas** (around for a couple of years, a well-featured drawing system appropriate for the watch). **Canvas is fast and runs on the GPU.** If something can't be done in Canvas, talk to **DTS**, file feedback, or post in the forums; Apple wants to know what's not possible. (Matthew, with Anne on Canvas/GPU.)

### Lesser-known watch opportunities (Prechaya)

- **What can developers do on watchOS that they might not know about? (Prechaya, who mentioned wanting to use the watch as a tennis racket sensor for a visionOS game).** The watch exposes **Core Motion** sensor data, so you can track device motion for things like a tennis racket peripheral for visionOS, or sensing how steady a hand is while forming pottery. There's room for novel experiences using motion plus the watch's health data. (Dan; the host noted "core motion" after the transcript rendered the abbreviation.)
- **Proactive experiences (Devin, Matthew).** The greatest watch experiences are often the proactively delivered ones: live activities and relevant widgets surfaced when the time is right, which is the promise of the Smart Stack.
- **What makes a great live activity / relevant Smart Stack item?** Register a widget's relevance against **location, date, or semantic locations** (at work, at home) so it matches the user's real-world context. **Don't occupy the top Smart Stack spot too long** or users start dismissing your app; be judicious about timing and confidence. (Devin, then more from the panel.)
- **Alerting discipline.** The "mango cart" / produce-delivery **live activity from a WWDC25 session ("Bring your live activity to Apple Watch")** is the reference: show delivery progress, then **remove it once delivered** rather than lingering for hours. Live activities support **notifying vs non-notifying updates**: alert for "your delivery arrived," passively track small status changes. The sports team's work (bringing live activities to the Sports app) is the canonical example: they **don't alert on every score** because scoring means different things per sport — **basketball alerts at the quarters, soccer alerts every score**. A new **noise suggestion** this year gives a soft Smart Stack reminder before the usual high-noise notification. (Matthew, Devin)
- **Best resource for these decisions?** The developer website, but the panel is deliberately **not prescriptive**: each tool serves a unique purpose, and developers know their app and domain best. WWDC is a valued feedback loop that informs which API wrinkles get ironed out. (panel)

### Liquid Glass on watchOS 27 (Simon, Helix)

- **What's new for Liquid Glass specifically on watchOS? (Simon, Helix).** watchOS 26 brought Liquid Glass and the new design language to the watch; watchOS 27 refines it: the updated look with more **dark edges / dark speculars**, nicer **content separation as you scroll** to preserve legibility, and **performance improvements**. **Interactive/reactive glass** (dragging buttons) is fun and comes to many apps automatically. Apps already on the **watchOS 10 design system** get the Liquid Glass updates from last year and this year largely for free. (Matthew, Anne)
- **The 26-cycle feedback loop (host).** Developer Relations met with developers around the world about Liquid Glass across all the platform 26 releases; that feedback informed the direction for iOS and watchOS 27. The **"What's New in SwiftUI" session** this year adds tools to customize Liquid Glass and bring it to the watch.
- **Follow-up 1: anything special developers must do to use Liquid Glass on watchOS 27?** For the most part, **no** — and that's framed as a good answer; apps on the watchOS 10 design language get it automatically.
- **Follow-up 2: is the iOS clear/tinted glass preference present on the watch?** **No watch-level slider is provided.** Because the watch UI is primarily dark and the screen is small, the team felt they could balance legibility and the glass feel without a slider. File feedback if you want one. Accessibility settings (**reduce transparency, increase contrast**) are available on the watch.

### Hidden gems

- **Workout zones (Dan).** Unlike Foundation Models (the big headline feature), **zones has been requested for years**. The API is flexible and easy to use, with time-in-zone richness rather than "just the boundaries" people initially asked for; you can choose different zones per workout app. **Heart rate zones and cycling power zones** both called out. Crucially, **you don't have to be a workout app** to use the workout zones API: there's the in-workout "what zone, how hard," and the after-workout angle ("how long was someone in a high heart rate zone this week"). (Dan, host)
- **The reorderable API (Anne).** New in SwiftUI this year (mentioned in the State of the Union) and **available on the watch**: collections developers can drag to reorder. Apple **used it to implement Control Center**. This is the **first time developers can reorder containers on watchOS, ever**.

### Widget update budgets (Medium Fidelity)

- **Does watchOS 27 change guidance for keeping widgets up to date? Roughly 50 widget/complication updates per day, regardless of background app refresh vs APNS? (Medium Fidelity).** No major new guidance for watchOS 27; reinforced good practices:
  - Expect a widget to update **every 15–20 minutes when actively used**. (Devin)
  - **Watch face vs Smart Stack matters.** Watch-face widgets are viewed all day, sit in the **top budget tier**, and are favored by the scheduler. Smart Stack widgets get background refresh through the day, but **not necessarily in the 15–20-minute bucket** — it depends on how often the user actually looks (analogous to a widget a few pages away on the iOS home screen: looked at once a day → roughly refreshed once a day). (Devin)
  - **Update paths:** invalidate the widget from your app when you have novel info, or rely on the **timeline's reload policy / expiration** (more deterministic; a stale timeline may be held briefly, then a stronger refresh guarantee). (Devin)
  - **Reference:** the developer-website article **"Keeping Your Widget Up To Date"** (which the asker's question text already cited) and the new **"Widget Kit Foundations"** WWDC26 video. (host)
- **Picking the right tool (Matthew, Anne).** The first thing reached for isn't always the right tool. **Timeline widgets** suit data that updates throughout the day (canonical: **weather** with a forecast; **calendar**). **Relevant configuration** suits something more immediate/timely (relevant at a place/time/location → runtime to update in that moment). **Live activities** suit a session with an obvious beginning and end (sports game, ride share): substantially more runtime for substantially more updates for a fixed time, then runtime goes away. **Controls** suit performing a quick action. Internally the team **pivots between technologies** while iterating on a design (e.g., starting with a timeline widget and switching to relevant configuration); that iteration is part of the fun of the constraints.
- **Refresh signals recap.** Last year (watchOS 26) shipped **APNS-based widget refreshes** (server push); this year adds **watch-connectivity-based refresh**. Plus background app refresh and timelines. Combine them to fit your data model; file feedback (with use case) if no combination fits.

### Standalone first-launch asset fetching (LazyVar, final question)

- **Best practices for heavy asset fetching on a standalone watchOS app's first launch without watch connectivity, given the radio scheduler limits? (LazyVar).**
  - **Bundle what you instantly need.** You have **no time before first launch**, so you'll be in the background fetching on that first launch; **background URL** can fetch the rest. (Anne)
  - **Design for the wait.** Provide something usable immediately so users can start doing something and **don't stare at a spinner** while other assets download. Separate "must download now" from "can show a useful experience meanwhile." (Anne)
  - **Design for fully offline.** A first launch may happen **off the grid** (back-country on an Ultra or Series watch with no connectivity). Decide what the app shows with nothing available. This is framed as "a canonical Apple Watch challenge" and a chance for something genuinely delightful and forward-thinking. (panel, Anne)

## Unconventional facts & takeaways (the live-Q&A gold)

- **Foundation Models on the watch never uses the iPhone's on-device model**, even on the same Wi-Fi network. It's always a network call to PCC or another `LanguageModel` provider. This is stated explicitly and twice, contradicting an intuitive "use the nearby phone's model" assumption.
- **`LanguageModel` is an open protocol on the watch**: official Claude and Gemini support is "coming soon" from those vendors, and you can conform yourself. PCC use needs an **entitlement**.
- **Widget runtime on watchOS equals iOS** apart from the smaller core count, and an existing **lock screen widget should work out of the box** on the watch.
- **Device Hub now connects the Mac directly to the watch** (Xcode 26 onward), replacing the old proxy-through-the-phone path, with measurable reliability/throughput gains. **Recent Apple Watch hardware has a 5 GHz Wi-Fi chip.** Peer-to-peer must be enabled on the network (a common corporate-network gotcha).
- **SceneKit is deprecated but still usable on the watch; RealityKit is not available on watchOS.** SwiftUI **Canvas runs on the GPU** and is the recommended 3D/drawing path.
- **There is intentionally no clear/tinted Liquid Glass slider on the watch** because the UI is primarily dark and the screen is small; the team judged it unnecessary for legibility.
- **The reorderable API is the first time ever** that developers can reorder containers on watchOS; Apple used it to build **Control Center**.
- **Workout zones is usable by non-workout apps**, and was a multi-year developer request; Apple "used it internally for a little bit first" before shipping the flexible time-in-zone API.
- **Controls cross to the watch automatically with zero developer work** (example: unlock-the-car control); live activities likewise arrive automatically and only need customization for a polished Smart Stack look.
- **Sports-app alerting is sport-specific by design**: basketball alerts at the quarters, soccer alerts every score. A new **noise "suggestion"** softly precedes the high-noise notification.
- **Cultural color:** the "Dan / David undercover" opening bit; the running "stealing each other's favorite answers" gag in the features round; "only public gems" after the hidden-gems detour; the closing watch-band show-and-tell ("Lume," "Unity Bloom," "matchy matchy"); and "we all love watchOS / who else loves it" call-and-response. "File feedback with your use case" is the lab's refrain, as is treating the watch's constraints as a delightful design opportunity.
