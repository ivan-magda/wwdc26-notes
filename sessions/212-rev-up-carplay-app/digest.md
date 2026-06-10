---
title: "Rev up your CarPlay app — Full Digest"
session: WWDC26 · 212
url: https://developer.apple.com/videos/play/wwdc2026/212/
duration: 16m
speakers: Chris (car experience team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 212 — Rev up your CarPlay app

## TL;DR

What's new in CarPlay for iOS 27, demoed end-to-end with Apple's **Landmarks** sample
app. Four headline themes:

1. **Two new app categories** — **video apps** (browse + play video on the CarPlay
   display in cars that support the "video in car" feature) and **voice-based
   conversational apps**. Both sit alongside the existing audio / communication /
   navigation categories, plus Live Activities and widgets from any app.
2. **Richer list & playback UI for all apps** — portrait/landscape list images,
   **thumbnails** with overlays (title badges, custom image badges, and a **sports
   overlay**), `CPPlaybackConfiguration` for progress + playback state, and a
   **details header** for featuring one item above a list.
3. **A now-playing MiniPlayer** (automatic, opt-out) and a **Voice Control template**
   that's now available to every category and can render full-screen or as an overlay.
4. **Navigation upgrades** — Map template **panels** (app-controlled primary interface
   area) and **Route sharing** (your route's coordinates go to the vehicle so driver
   assistance / EV charging features work with third-party nav). Plus a beefed-up
   **CarPlay Simulator** in Device Hub.

---

## 1. Apps in CarPlay — new categories

CarPlay's existing categories: **audio** (music, podcasts), **communication**
(messages, calls), and **navigation** (turn-by-turn). On top of that, CarPlay shows
**Live Activities and widgets from any app**, even apps not specifically designed for
CarPlay. New since this year: **voice-based conversational apps**, and new in iOS 27:
**video apps**.

### Video apps

- Let people **browse** their favorite videos from iPhone on the CarPlay display —
  aimed at parked moments (waiting at the airport, at a charging station).
- AirPlay video streaming to the car display already works when parked (select the
  car's display from iPhone playback); this adds **browsing** on the CarPlay display.
- Requirements: support **AirPlay video streaming** + use the **CarPlay framework** for
  the browsing UI. Only works in cars that support the **video in car** feature.
- When the car reports video playback isn't available, the video **degrades to
  audio-only** — great for continuing a podcast or sports broadcast while driving.
- **Entitlements:** the **CarPlay video** entitlement only puts the app on the home
  screen if the car supports video. If content suits both video and audio, ship **both**
  the CarPlay audio and CarPlay video entitlements so the app **always** appears.

## 2. CarPlay framework — UI for all apps

Works for both **CarPlay** and **CarPlay Ultra**. iOS manages layout across screen
resolutions and input hardware (touchscreens, knobs, touch pads) via templates, so the
app doesn't hand-tune layouts. The Landmarks demo checks `CPSessionConfiguration` for
video support and conditionally adds a **videos tab**.

### Lists, thumbnails, overlays

- Lists can now show images in **portrait or landscape** aspect ratios.
- **Card / thumbnail** elements add overlays, playback progress, and sports info painted
  right on the image.
- Overlays: a **title overlay** (e.g. "newly added" / "live streaming") and **custom
  image** badges.
- **`CPPlaybackConfiguration`** carries playback metadata: elapsed time + duration
  (rendered as a progress bar) and a **playback action** (play / pause / replay).
  Set **`preferredPresentation`** to `.video` or `.audio`. **Keep it updated on every
  playback state change** so the thumbnail stays accurate.
- **Sports overlay** — left team, right team, and event status.

### Details header

A prominent single item at the top of a list (current episode above its episode list,
or a movie summary above bonus content). Combines a single thumbnail + title + body
text + playback configuration + action buttons. In Landmarks: a thumbnail overlay, an
unplayed playback configuration, and **Play** + **Add to playlist** buttons — the
first action button **auto-combines with playback progress**. Update its playback
configuration as state changes so it's correct when the header reappears.

### MiniPlayer (now playing)

New in iOS 27. Makes it easy to see what's playing; on larger displays you can play /
pause / skip. **Automatic for all now-playing apps.** To opt out:

```swift
// Disable the MiniPlayer
CPNowPlayingTemplate.shared.allowsMiniPlayer = false
```

With it disabled, the now-playing **icon** appears in the navigation bar instead.
Apple's guidance: the MiniPlayer is the best now-playing option in CarPlay.

### Voice Control template

Launched earlier this year for conversational apps; **in iOS 27 it's available to all
CarPlay categories**. If your app has its own voice features, it can answer questions
and perform actions in the car.

- Includes a **prompt** and an **animated state icon** — both optional.
- Up to **two action buttons**, plus **leading and trailing nav-bar buttons**.
- Good pattern: when answering about a destination/contact, offer action buttons to
  **start navigation** or **place a call**. Trigger those by opening a URL with
  **`CPTemplateApplicationScene`**.
- Available **full-screen or as an overlay** on top of another template (e.g. the Map
  template in a nav app) via **`CPInterfaceController`**. For the overlay, supply
  **shorter text variants** to fit the smaller space.
- **Audio feedback** guidance: play waiting sounds while preparing the conversation and
  processing sounds while preparing a response. Configure **`AVAudioSession`** with the
  **play-and-record** category, **default** mode, and **mixing disabled**.

Apple also expanded availability of existing templates to more app categories — see the
**CarPlay Developer Guide** for the category/template matrix.

## 3. Navigation apps

### Map template panels

In iOS 27, a nav app can show **panels** to build its own UI in the **primary interface
area** of the Map template, independent of the trip/route-options flow. Panels can
include multiple UI elements and are composed from objects you already use: **trips,
grids, route choices, route details, waypoints, and other list items**. A **button
configuration** sets the actions at the bottom (e.g. **Go** / **End**). By building up
and pushing panels, the app shows more content and controls **while keeping the map
visible**.

### Route sharing

Some vehicles' driver-assistance systems work best when the **intended route** is
known — e.g. automatic lane changes, guidance tuned to the shown route, or (for EVs)
suggested charging stops based on remaining range. **Route sharing** makes those
features work even with a third-party CarPlay nav app.

- The app provides the route as an **array of route segments** (geographic coordinates)
  sent to the vehicle **whenever the trip changes**.
- **Requires iOS 26.4+ and a supported vehicle.**
- Demo flow (camping trip: coffee stop → campground): the app builds a two-segment trip
  → route sharing sends it → the EV estimates energy use and decides a **charging stop**
  is needed → the vehicle finds the ideal charging station and sends that **proposed
  waypoint** back to iOS → the nav app receives it via the **Map template**.
- The app then **chooses** how to handle the suggested waypoint:
  - **Return updated travel estimates** to the Map template → it **auto-prompts** the
    driver to accept the waypoint.
  - **Don't return estimates** → the app **manages confirmation** itself.
  - Either way, once accepted, the app **adds a new route segment** and **re-shares** the
    updated trip.

**Consent & control** — both the driver and the app gate route sharing:

- At pairing, the driver is prompted to **approve** route sharing for that vehicle;
  approval applies to **any** nav app on that vehicle.
- The app **opts in** via the Map template:

```swift
// Enable route sharing
func mapTemplateShouldProvideRouteSharing(_ mapTemplate: CPMapTemplate) -> Bool { true }
```

- The app can **disable route sharing for an individual trip** when a trip isn't
  eligible:

```swift
// Disable route sharing for this trip
trip.routeSegmentsAvailableForRegion = false
```

## 4. CarPlay Simulator

- Test by **connecting to your Mac** — works for any CarPlay app category.
- Test **different screen sizes and vehicle configurations** (the demo configured a
  vehicle that supports video, which is what surfaces the video-only app on the home
  screen).
- Now available in **Device Hub**.
- New **diagnostic tools for route sharing** for nav-app developers.
- Video-app developers get the Simulator via the **Additional Tools for Xcode** package.

The video-player demo also flagged player affordances: **subtitle and audio-language
menus** (so ship subtitles + extra languages when available) and a **Do Not Disturb**
button so notifications can be suppressed without leaving the video.
