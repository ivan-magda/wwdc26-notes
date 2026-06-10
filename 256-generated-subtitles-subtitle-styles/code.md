# Code samples — Session 256

Extracted from the Code tab. Timestamps map to the transcript's `AVPlayerLayer`
walkthrough ([07:39]–[08:47]).

## 5:39 — Implement subtitle style preview (AVPlayerLayer + MediaAccessibility)

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

---

## API notes surfaced by the code + transcript

- **`MACaptionAppearanceCopyProfileIDs()`** (`MediaAccessibility`) — returns every subtitle/caption style profile ID in the system. Each built-in *and* user-created (Settings app) style has a profile ID. Use the IDs to populate your style-selection UI with names.
- **`AVPlayerLayer.setCaptionPreviewProfileID(_:position:text:)`** — shows a live stylized preview using the chosen profile.
  - Existing/active subtitles are **automatically hidden** during the preview so they don't interfere.
  - Pass `text: nil` to show **localized system placeholder text** instead of custom text.
  - `position` is an **offset from the default preview location** — use it to keep the preview clear of your UI controls. `.zero` = default location.
  - Safe to call repeatedly to switch between styles.
- **`AVPlayerLayer.stopShowingCaptionPreview()`** — removes the preview text and restores any active subtitles. Call when style selection is done.
- **`MACaptionAppearanceSetActiveProfileID(_:)`** (`MediaAccessibility`) — commits the chosen style. It applies to **all subtitles system-wide**, not just this player. (Note the type mismatch in the sample: `Copy` returns `String`s while `Set` takes a `CFString`.)
- **`AVCaptionRenderer`** — an alternative path mentioned in the talk for the same preview, but *you* are responsible for rendering it (no Code-tab sample provided).
