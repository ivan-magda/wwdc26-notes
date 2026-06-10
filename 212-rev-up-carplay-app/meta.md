# Session 212 — Rev up your CarPlay app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/212/
- **Duration:** 16m
- **Speakers:** Chris (engineer, car experience team)

## Description

What's new in CarPlay for iOS 27. The session walks through new app categories
supported in CarPlay (notably video browsing and voice-based conversational apps),
CarPlay framework UI updates that apply to all apps (list enhancements, thumbnails
and overlays, a new now-playing MiniPlayer, a new Voice Control presentation), new
features specific to navigation apps (Map template panels and Route sharing), and
improvements to the CarPlay Simulator. The running example is Apple's "Landmarks"
sample app.

## Key topics

- **New app categories** — CarPlay video apps (browse + play video in cars supporting the "video in car" feature) and voice-based conversational apps; both layered on top of existing audio / communication / navigation categories plus Live Activities and widgets from any app.
- **Video apps** — require AirPlay video streaming + the CarPlay framework for browsing; gated by the CarPlay video entitlement; combine with the CarPlay audio entitlement so the app always appears; video auto-degrades to audio-only when the car says video playback isn't available.
- **List / card UI** — portrait & landscape image aspect ratios; `CPPlaybackConfiguration` for playback metadata and progress; thumbnail overlays (title badges, custom image badges, sports overlay with left/right teams + status); details header (prominent single item over a list).
- **MiniPlayer** — new now-playing presentation, automatic for all now-playing apps; opt out via `CPNowPlayingTemplate.shared.allowsMiniPlayer = false`.
- **Voice Control template** — now available to all app categories; prompt + animated state icon (both optional), up to two action buttons + leading/trailing nav-bar buttons; full-screen or overlay (via `CPInterfaceController`); URL actions via `CPTemplateApplicationScene`; audio-feedback guidance (`AVAudioSession` play-and-record, default mode, mixing disabled).
- **Navigation — Map template panels** — app-controlled primary interface area; panels compose trips, grids, route choices, route details, waypoints, list items + a button configuration ("Go" / "End").
- **Navigation — Route sharing** — app sends route segments (geographic coordinates) to the vehicle; driver-assistance features (auto lane changes, EV charging-stop suggestions) use the known route; vehicle can propose a charging waypoint back to the app; requires iOS 26.4+, a supported vehicle, driver approval at pairing, and per-trip opt-in/opt-out.
- **CarPlay Simulator** — connect via Mac; test screen sizes + vehicle configurations; now in Device Hub; route-sharing diagnostic tools; video-app testing via the Additional Tools for Xcode package.

## Related sessions to fetch

- [ ] _No other WWDC sessions are explicitly named in this talk._ (Referenced docs: the **CarPlay Developer Guide** for app categories and available templates.)

## Chapter summary (Summary tab)

- **0:00 Introduction** — CarPlay is the smarter, safer way to use iPhone in the car; iOS 27 brings new capabilities to all CarPlay app categories. Agenda: new categories, framework updates for all apps, navigation features, Simulator.
- **0:42 Apps in CarPlay** — existing categories (audio, communication, navigation) plus Live Activities and widgets from any app; new this year: voice-based conversational apps and, in iOS 27, video browsing in cars that support the "video in car" feature. AirPlay video already works when parked; now apps can also offer browsing. Video entitlement gates home-screen appearance; pair with audio entitlement; video degrades to audio-only on demand.
- **2:51 CarPlay framework** — works for both CarPlay and CarPlay Ultra. List enhancements (portrait/landscape images, thumbnails, `CPPlaybackConfiguration`, overlays incl. sports, details header), a new now-playing MiniPlayer (`allowsMiniPlayer`), and a new Voice Control template presentation (full-screen + overlay) available to all categories.
- **11:54 Navigation apps** — Map template **panels** give the app control over the primary interface area, composed from existing CarPlay objects + a button configuration. **Route sharing** sends route segments to the vehicle so driver-assistance / EV features work; vehicle can suggest charging waypoints; gated by iOS 26.4+, supported vehicle, driver approval, and per-trip opt-in.
- **15:19 CarPlay Simulator** — test by connecting to your Mac; supports screen sizes + vehicle configs; in Device Hub; route-sharing diagnostics; video testing via Additional Tools for Xcode. Closing call to action: add thumbnails, details header, voice control, video browsing, panels, and route sharing.

## Code

See `code.md` — 3 snippets from the Code tab (MiniPlayer opt-out, route-sharing opt-in, per-trip route-sharing disable), plus the concrete APIs named in the transcript.
