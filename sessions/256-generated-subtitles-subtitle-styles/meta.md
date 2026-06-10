# Session 256 — Discover generated subtitles and subtitle styles

- **URL:** https://developer.apple.com/videos/play/wwdc2026/256/
- **Duration:** 11m
- **Speakers:** James (AVFoundation engineer)

## Description

Apple AI-generated subtitles are created live, on-device, as media plays. This
session covers how those generated subtitles surface in your app (no work required —
they appear automatically during playback), the new subtitle style preview feature
that lets people customize subtitle appearance while watching a video, and how media
assets are authored so you understand where generated subtitles fit alongside
authored ones.

## Key topics

- **Generated subtitles** — on-device, live, two use cases: speech transcription (from source audio) and language translation (from existing subtitles)
- **Zero integration cost** — generated subtitles appear automatically during video playback; nothing to implement to turn them on
- **Playback scenarios** — HTTP Live Streaming (live streams + VOD) and file-based content (app-bundled or downloaded)
- **Content types** — professional (movies/series) and customer-created (iPhone camera capture, social media)
- **Availability** — iOS / macOS / tvOS / visionOS 27: English subtitles from English audio; iOS / macOS also do English-subtitle → multiple-language translation
- **Authored vs generated** — authored subtitles are preferred and unchanged; generated ones fill gaps and are marked with a sparkle + "Translated"
- **Subtitle selection UI** — `AVPlayerViewController` (iOS), `AVPlayerView` (macOS), `AVLegibleMediaOptionsMenuController`, or fully custom controls
- **Subtitle style preview** — change subtitle styling from the playback menu with a live preview; built-in + custom (Settings-app) styles
- **Style preview APIs** — `AVPlayerViewController` / `AVPlayerView` (full), `AVLegibleMediaOptionsMenuController`, `AVPlayerLayer` (preview API), `AVCaptionRenderer` (you render)
- **Profile IDs** — each system subtitle style has a profile ID (`MACaptionAppearance*` from `MediaAccessibility`)

## Related sessions to fetch (referenced in this talk)

- [ ] _(none explicitly named — this is a self-contained AVFoundation/MediaAccessibility talk)_

## Chapter summary (Summary tab)

- **0:00 Introduction** — Why people rely on subtitles (accessibility, comprehension, noisy environments); what the session covers — on-device AI-generated subtitles in your app, the subtitle style preview, and how media assets are authored.
- **1:10 Media authoring** — Traditional authoring: video, audio, and manually created ("authored") subtitles combined into the final media package; one subtitle track per language, multiple audio languages possible.
- **2:14 Subtitle generation methods** — Two use cases: speech transcription (source audio → on-device speech-to-text model) and language translation (source subtitles → on-device translation model). Authored subtitles are preferred and remain unchanged.
- **3:03 Availability and support** — Works automatically in HLS (live + VOD) and file-based content. iOS/macOS/tvOS/visionOS 27: English subtitles from English audio; iOS/macOS also translate English subtitles into multiple languages. Professional and customer-created content supported.
- **4:31 Presenting subtitles in your app** — Subtitle selection UI options: `AVPlayerViewController` (iOS, full controls), `AVPlayerView` (macOS), `AVLegibleMediaOptionsMenuController` (selection UI without player controls), or custom controls.
- **5:39 Subtitle style preview** — Implement a live style preview using `AVPlayerLayer` and `AVCaptionRenderer`, letting users customize subtitle appearance during playback.
- **8:55 Demo** — Both features together: switch a camping video to generated Italian subtitles (marked with a sparkle + "Translated"), then customize the style (Large Text, then a custom "Bold Yellow") for readability.
- **10:20 Next steps** — Best practices: explore generated subtitles, ensure your app has subtitle-selection UI, and implement the style preview.

## Code

See `code.md` — 1 snippet from the Code tab (subtitle style preview via `AVPlayerLayer` + `MediaAccessibility`).
