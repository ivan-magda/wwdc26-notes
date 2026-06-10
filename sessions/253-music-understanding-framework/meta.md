# Session 253 — Meet the Music Understanding framework

- **URL:** https://developer.apple.com/videos/play/wwdc2026/253/
- **Duration:** 16m
- **Speakers:** Conner (Computational Music Team)

## Description

The Music Understanding framework brings on-device, offline musical intelligence to
all Apple platforms. It handles the signal processing and model inference for you, so
no signal-processing or ML expertise is required. Because everything runs on-device,
the audio you analyze stays private and works without a network. Apple's own Final Cut
Pro team uses it for beat detection and the iPad montage feature. The session covers
what the framework analyzes, how to drive a `MusicUnderstandingSession`, and walks
through the `Music Understanding Lab` sample app.

## Key topics

- Six analysis areas: **key, rhythm, structure, pace, instrument activity, loudness**
- `MusicUnderstandingSession` initialized from an `AVAsset` or a custom audio provider
- `analyze()` (all types) vs targeted `analyze(for:)` (only requested types; rest nil)
- Two time-association primitives: `TimedValue<Value>` (point in `CMTime`) and `RangedValue<Value>` (`CMTimeRange`)
- `KeyResult` → `KeySignature` (`Tonic` + `Mode`); `RhythmResult` (beats/bars as `CMTime`, optional `beatsPerMinute`)
- `StructureResult` three-level hierarchy: sections ⊃ segments ⊃ phrases (all `CMTimeRange`)
- `PaceResult` (perceived energy, ranged `Double`); `InstrumentActivityResult` (ranges + per-instrument activity)
- `LoudnessResult` in LUFS: integrated / momentary (400ms window) / shortTerm (3s window) / peak (dB); samples every 100ms
- Streaming loudness via `AsyncSequence` (`loudnessResults`); custom `AudioProvider` yielding `AVReadOnlyAudioPCMBuffer`
- All results are `Codable` → one-line JSON export
- Real-world use: Final Cut Pro beat detection + FCP for iPad montage; sample app builds a music-synced Video tile from structure + pace

## Related sessions to fetch (referenced in this talk)

- [ ] (none — this is a standalone "Meet the framework" talk; no other sessions named)

## Chapter summary (Summary tab)

- **0:00 Introduction** — On-device, offline audio analysis across all Apple platforms; no signal-processing/ML expertise needed; audio stays private. Final Cut Pro uses it for beat detection and (on iPad) the montage feature.
- **1:39 Musical features** — The six analysis areas: key, rhythm, structure, pace, instrument activity, loudness. Defines the rhythm/structure vocabulary (beats → bars → phrases → segments → sections), key, pace, and loudness.
- **3:19 Framework integration** — Apps talk to a `MusicUnderstandingSession`, initialized with an `AVAsset` or custom audio provider; call `analyze()` (all types) or `analyze(for:)` (targeted) and await results.
- **3:55 Music Understanding Lab** — Sample app on developer.apple.com that visualizes every analysis type as a tile (Key, Rhythm, Structure, Pace, Instrument Activity, Loudness), with a playhead, JSON export, streaming loudness, and a music-synced Video tile built from structure + pace.

See `code.md` for the extracted API snippets.
