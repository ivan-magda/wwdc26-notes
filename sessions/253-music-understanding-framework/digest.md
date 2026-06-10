---
title: "Meet the Music Understanding framework — Full Digest"
session: WWDC26 · 253
url: https://developer.apple.com/videos/play/wwdc2026/253/
duration: 16m
speakers: Conner (Computational Music Team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 253 — Meet the Music Understanding framework

## TL;DR

A new **Music Understanding** framework gives every Apple platform **on-device,
offline musical intelligence** — no signal-processing or ML expertise required, and
no audio ever leaves the device. You hand it an `AVAsset` (or stream your own audio
buffers), call `analyze()`, and get back structured results across **six areas**:

1. **Key** — tonic + major/minor mode, as time ranges.
2. **Rhythm** — beat and bar timestamps plus a global BPM.
3. **Structure** — a three-level hierarchy of sections ⊃ segments ⊃ phrases.
4. **Pace** — how fast/energetic the music *feels* over time.
5. **Instrument activity** — which instruments play when, and how intensely.
6. **Loudness** — LUFS measurements (integrated / momentary / shortTerm / peak), with a streaming API.

Everything is `Codable`, so a full analysis serializes to JSON in two lines. Apple's
own **Final Cut Pro** team ships it: beat detection (rhythm + structure) on the
desktop, and the **montage** auto-sync feature (rhythm + pace + structure) on iPad.
The talk closes on a sample app — **Music Understanding Lab** — that visualizes every
result as a tile and builds a music-synced video from structure + pace.

---

## 1. What it is

Conner from Apple's **Computational Music Team** frames the pitch: the framework
"handles all the signal processing and model inference for you," so you don't need
expertise in DSP or machine learning. It runs **entirely on-device**, which means the
audio you analyze stays **private** and works **offline**.

The proof point is internal: the **Final Cut Pro** team built two features on it.

- **Beat detection** analyzes a song's **rhythm and structure** to reveal a beat grid, so editors can align cuts to parts, bars, and beats.
- On **Final Cut Pro for iPad**, the **montage** feature analyzes **rhythm, pace, and structure** to auto-synchronize clips to the music.

## 2. The six analysis areas (vocabulary)

The framework defines a clear musical hierarchy:

- **Rhythm** is the pulse — individual **beats** that build into **bars**. Beats per minute (**BPM**) is the count of beats in a minute.
- Bars form **phrases** ("musical sentences"); phrases combine into **segments**; segments build **sections** (a chorus, verse, intro, or bridge).
- **Instruments** (drum, bass, vocals, …) play at different times and intensities, around a common set of notes — the **key**.
- **Pace** captures how slower/faster a part *feels*, independent of the steady BPM.
- **Loudness** captures how the track gets louder or quieter over time.

## 3. Driving a session

Apps interact with a **`MusicUnderstandingSession`**, initialized from either an
**`AVAsset`** or a **custom audio provider**. Call **`analyze()`** and await results.
By default it analyzes **all** types; for performance, call **`analyze(for:)`** with
just the types you need so the framework skips the rest.

```swift
import MusicUnderstanding

.fileImporter(isPresented: $isPresented, allowedContentTypes: [.audio]) { result in
    switch result {
    case .success(let url):
        let asset = AVURLAsset(url: url,
                               options: [AVURLAssetPreferPreciseDurationAndTimingKey : true])
        let session = try await MusicUnderstandingSession(asset: asset)
        let results = try await session.analyze()
    }
}
```

Setting `AVURLAssetPreferPreciseDurationAndTimingKey` to `true` is called out as
important for the most accurate results.

The return type is a **`SessionResult`** with one optional field per feature:

```swift
public struct SessionResult: Codable, Sendable {
    public let instrumentActivity: InstrumentActivityResult?
    public let key: KeyResult?
    public let loudness: LoudnessResult?
    public let pace: PaceResult?
    public let rhythm: RhythmResult?
    public let structure: StructureResult?
}
```

With `analyze()` all fields populate; with `analyze(for:)` only the requested ones do
and the rest are `nil`.

## 4. The two time primitives

Every result hangs off one of two generic, time-aware containers:

```swift
// A value at a single instant
public struct TimedValue<Value>: Codable, Equatable, Sendable
where Value: Codable & Equatable & Sendable {
    public let time: CMTime
    public let value: Value
}

// A value over a span
public struct RangedValue<Value>: Codable, Equatable, Sendable
where Value: Codable & Equatable & Sendable {
    public let range: CMTimeRange
    public let value: Value
}
```

Both are nested under `MusicUnderstandingSession` (e.g.
`MusicUnderstandingSession.RangedValue<…>`).

## 5. Key

```swift
public struct KeyResult: Codable, Sendable {
    public let ranges: [MusicUnderstandingSession.RangedValue<KeySignature>]
}

public struct KeySignature: Codable, Hashable, Sendable {
    public let tonic: Tonic
    public let mode: Mode
}

@frozen public enum Tonic: String, Codable, Hashable, Sendable {
    case aFlat, aSharp, a, bFlat, b, c, cSharp, d, dFlat, dSharp, eFlat, e, f, fSharp, g, gFlat, gSharp
}

public enum Mode: String, Codable, Hashable, Sendable {
    case major, minor
}
```

A `KeyResult` maps a `KeySignature` (tonic + major/minor mode) to a time range, so the
key can change across the song. The demo song reads as **D-flat major**.

## 6. Rhythm

```swift
public struct RhythmResult: Codable, Sendable {
    public let beats: [CMTime]
    public let bars: [CMTime]
    public let beatsPerMinute: Float?
}
```

Beat and bar timestamps come back as `CMTime` arrays; `beatsPerMinute` is the overall
global tempo. **It's optional** — `nil` until the framework has processed enough audio
to find at least two beats.

## 7. Structure

```swift
public struct StructureResult: Codable, Sendable {
    public let sections: [CMTimeRange]
    public let segments: [CMTimeRange]
    public let phrases: [CMTimeRange]
}
```

Three parallel arrays of `CMTimeRange` express the hierarchy: each **section** spans
one or more **segments**, and each segment spans **phrases**. The Lab UI renders this
as three rows and highlights the current section/segment/phrase during playback.

## 8. Pace

```swift
public struct PaceResult: Codable, Sendable {
    public let ranges: [MusicUnderstandingSession.RangedValue<Double>]
}
```

Pace is the *perceived* energy of the music — faster/more energetic parts get higher
values. In the Lab it's drawn as bars: taller = higher energy.

## 9. Instrument activity

```swift
public struct InstrumentActivityResult: Codable, Sendable {
    public let ranges: [Instrument: [CMTimeRange]]
    public let activity: [Instrument: [MusicUnderstandingSession.TimedValue<Float>]]
}
```

Two views of the same thing:

- **`ranges`** — per-instrument presence as time ranges. Good for a simple "is this instrument playing?" check.
- **`activity`** — per-instrument intensity over time, as `TimedValue<Float>` in the range 0–1 (closer to 1 = louder in the mix). Conner calls this "a great source to drive audio-reactive animations."

## 10. Loudness

```swift
public struct LoudnessResult: Codable, Sendable {
    public let integrated: MusicUnderstandingSession.TimedValue<Float>
    public let momentary: [MusicUnderstandingSession.TimedValue<Float>]
    public let shortTerm: [MusicUnderstandingSession.TimedValue<Float>]
    public let peak: MusicUnderstandingSession.TimedValue<Float>
}
```

Measured in **LUFS** (Loudness Units Full Scale — the industry standard for perceived
volume):

- **integrated** — a single average loudness for the whole song.
- **momentary** — sampled every **100ms** over a **400ms** window; catches short, sudden spikes.
- **shortTerm** — sampled every 100ms over a **3s** window; a smoother trend.
- **peak** — the absolute maximum level, measured in **decibels**.

### Streaming loudness

For live/real-time use there's an `AsyncSequence` that delivers a `LoudnessResult`
for every 100ms of analyzed audio:

```swift
public var loudnessResults: some AsyncSequence<LoudnessResult, any Error> & Sendable
```

```swift
let audioProvider = AudioProvider()
let session = MusicUnderstandingSession(audioProvider: audioProvider)
await withThrowingTaskGroup(of: Void.self) { taskGroup in
    group.addTask {
        for try await result in await session.loudnessResults {
            updateAudioLevel(result.momentary.value)
        }
    }

    group.addTask {
        try await session.analyze(for: [.loudness])
    }
}
```

One task consumes results as they arrive; another kicks off the analysis.

### Custom audio provider

To feed your own audio, conform a type to `AsyncSequence` yielding
`AVReadOnlyAudioPCMBuffer`, ending with a `nil` to signal completion:

```swift
struct AudioProvider: AsyncSequence, AsyncIteratorProtocol {
   func makeAsyncIterator() -> Self { self }

   mutating func next() async -> AVReadOnlyAudioPCMBuffer? {
        // Return the next audio buffer, or nil to signal completion
    }
}
```

## 11. Export — everything is Codable

All results conform to `Codable`, so a full analysis serializes to JSON trivially.
The Lab's Share button does exactly this:

```swift
let session = try await MusicUnderstandingSession(asset: asset)
let results = try await session.analyze()

let encoder = JSONEncoder()
try encoder.encode(results)
```

This also enables the "pre-compute and bundle analysis data" workflow Conner mentions
for games.

## 12. The Video tile — composing structure + pace

The Lab's Video tile auto-builds a clip montage synced to the song:

1. Identify the **section** time ranges from structure.
2. For each section, use its **pace** to decide how many clips to show.
3. Since pace is an events-per-minute rate, divide by 60 to get **time per clip**:

```swift
let timePerClip = 60 / paceValue
```

Clips always start at the section boundary; within a section their length matches the
energy — longer/slower clips in calmer parts, shorter/faster clips in energetic parts.
This is a tidy worked example of two analysis types feeding one feature.

---

## Suggested uses (from the close)

- Sync visuals to **beat, loudness, or pace** for video-editing features.
- Organize a catalog by **tempo or key** to drive a DJ app.
- **Pre-compute and bundle** analysis data to animate a game to music.
