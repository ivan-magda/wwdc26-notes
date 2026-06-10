---
title: "Discover generated subtitles and subtitle styles — Full Digest"
session: WWDC26 · 256
url: https://developer.apple.com/videos/play/wwdc2026/256/
duration: 11m
speakers: James (AVFoundation)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 256 — Discover generated subtitles and subtitle styles

## TL;DR

Two distinct, accessibility-driven subtitle features for video apps on the 27 OSes:

1. **Apple AI-generated subtitles** — created live, **on-device**, as media plays. Two flavors: **speech transcription** (audio → speech-to-text) and **language translation** (existing subtitles → another language). The headline: **you implement nothing.** They appear automatically during playback for HLS (live + VOD) and file-based content. Authored subtitles are always preferred and untouched; generated ones fill missing-language gaps and are flagged in the UI with a **sparkle + "Translated."**

2. **Subtitle style preview** — let people change subtitle styling (built-in or their custom Settings-app styles) **from the playback menu, with a live preview**, instead of digging into Settings. `AVPlayerViewController` / `AVPlayerView` give it to you for free; `AVPlayerLayer` + `MediaAccessibility` profile IDs let you build it into custom controls; `AVCaptionRenderer` is the bring-your-own-rendering path.

The thread tying both together: more people can understand and enjoy your content. The demo is a camping-trip-to-Italy video where the presenter switches to generated Italian subtitles and then bumps up the readability with a custom "Bold Yellow" style.

---

## 1. Why subtitles (Introduction)

People rely on subtitles for many reasons: they're vital for people who are deaf or hard of hearing or have other accessibility needs; some read them to help follow spoken dialogue; and others (the presenter's example) use them when they physically can't hear the audio — in a busy airport, say. Apple AI-generated subtitles can be created **live, locally on the device, as the media plays.**

The session covers three things: how generated subtitles appear in your app, the subtitle style preview feature, and how media assets are authored.

## 2. Media authoring (where generated subtitles fit)

The traditional media-creation journey:

- Film and edit **video** and **audio**.
- **Authored subtitles** — text representing the spoken words — are created **manually**, one track per language. Multiple audio languages are possible too.
- The final media package bundles **video + audio + authored subtitles** together.

The gap: even content with several audio and subtitle languages **might not include a language the viewer understands.** Generated subtitles fill that gap on the device, so the original content doesn't need to ship every language.

## 3. How generated subtitles are created (two use cases)

| Use case | Source | On-device model | Output | Name |
|---|---|---|---|---|
| 1 | Audio | Speech-to-text model | Subtitles | **Speech transcription** |
| 2 | Existing subtitles (e.g. English) | Translation model | Subtitles in another language (e.g. Italian) | **Language translation** |

Generated subtitles **add languages**; the **authored subtitles are preferred and remain unchanged.**

## 4. Availability & support

**No implementation required** — generated subtitles are available automatically during video playback.

**Playback scenarios:**
- **HTTP Live Streaming** — live streams (e.g. TV channels) and Video on Demand (movies, shows, travel videos, live events like sports).
- **File-based content** — app-bundled videos or downloaded media.

**Content types:** professional (movies, series) and customer-created (iPhone camera capture, social media videos).

**Devices & languages (starting in the 27 OSes):**

| Capability | Platforms |
|---|---|
| English subtitles from English audio (speech transcription) | iOS, macOS, tvOS, visionOS 27 |
| Multiple subtitle languages from English subtitles (translation) | iOS, macOS 27 |

## 5. Presenting subtitles in your app

Provide subtitle-**selection** UI during playback. Options, easiest → most custom:

- **`AVPlayerViewController`** (iOS) — fully implements subtitle selection *and* player controls. Nothing extra to do.
- **`AVPlayerView`** (macOS) — similar functionality.
- **`AVLegibleMediaOptionsMenuController`** — presents subtitle-selection controls and implements the behavior, **without** player controls. Good for adding selection UI to an *existing* player.
- **Custom controls** — implement your own media selection to match the rest of your app.

## 6. Subtitle style preview

Presentation matters as much as presence. The Settings app has long let people pick and create caption/subtitle styles (built-in plus custom — the demo uses a custom **"Bold Yellow"**: yellow text with extra border). But switching styles is easier and more accessible **while watching the video.** The style preview puts the same styles into the subtitle menu and shows a **live preview** of each.

Implementation options:

- **`AVPlayerViewController`** (iOS) / **`AVPlayerView`** (macOS) — full style preview + player controls, free.
- **`AVLegibleMediaOptionsMenuController`** — style-preview controls + behavior, for an existing player UI.
- **`AVPlayerLayer`** — has an API to show the preview (see below).
- **`AVCaptionRenderer`** — can provide the preview, but **you render it.**

### The `AVPlayerLayer` flow

Each system subtitle style has a **profile ID**. Steps:

1. **Fetch all profile IDs** and populate your UI with their names.
2. On selection, **show the stylized preview**. New subtitles render in the chosen style; any active subtitles are **automatically hidden** so they don't interfere.
3. Pass **`text: nil`** to show **localized system placeholder text**. Use **`position`** (an offset from the default location) to keep the preview clear of your controls.
4. Call again to switch styles — as many times as needed.
5. **Stop the preview** when done — removes preview text, restores active subtitles.
6. **Set the chosen style** — applies to all subtitles **system-wide**.

```swift
import AVFoundation
import MediaAccessibility

func updateProfileList() {
    subtitleStyleProfileIDs = MACaptionAppearanceCopyProfileIDs() as? [String] ?? []
}

func showPreviewStyle(subtitleStyleProfileID: String) {
    playerLayer.setCaptionPreviewProfileID(subtitleStyleProfileID, position: .zero, text: nil)
}

func stopPreviewStyle() {
    playerLayer.stopShowingCaptionPreview()
}

func setSubtitleStyle(subtitleStyleProfileID: CFString) {
    MACaptionAppearanceSetActiveProfileID(subtitleStyleProfileID)
}
```

## 7. Demo — both features together

Planning a camping trip to Italy, the presenter watches a camping video that has English subtitles but wants Italian to brush up. In the Subtitles menu → Language, several tracks are listed; some **authored**, some **generated**. The generated options are marked with a **sparkle symbol** and the word **"Translated."** Picking Italian generated subtitles immediately shows Italian text.

Then Subtitles menu → Style. Trying **Large Text** swaps the live subtitles for a placeholder message (in Italian) rendered in that style. Switching to the custom **"Bold Yellow"** style and dismissing the menu leaves the real subtitles in the custom style.

## 8. Next steps

- Explore generated subtitles — watch some travel videos and turn them on.
- Make sure your app has UI to **select** subtitles.
- Implement the **subtitle style preview** too — people appreciate this accessibility feature when they need to change their subtitle style.
