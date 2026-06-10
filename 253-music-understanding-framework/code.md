# Code samples — Session 253

Extracted from the Code tab of the session page. Snippets map to the API walkthrough.

## Selecting a file and running analysis

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

## SessionResult — one optional field per feature

```swift
import MusicUnderstanding

public struct SessionResult: Codable, Sendable {
    public let instrumentActivity: InstrumentActivityResult?
    public let key: KeyResult?
    public let loudness: LoudnessResult?
    public let pace: PaceResult?
    public let rhythm: RhythmResult?
    public let structure: StructureResult?
}
```

## TimedValue — a value at a CMTime

```swift
import MusicUnderstanding

public struct TimedValue<Value>: Codable, Equatable, Sendable
where Value: Codable & Equatable & Sendable {
    public let time: CMTime
    public let value: Value
}
```

## RangedValue — a value over a CMTimeRange

```swift
import MusicUnderstanding

public struct RangedValue<Value>: Codable, Equatable, Sendable
where Value: Codable & Equatable & Sendable {
    public let range: CMTimeRange
    public let value: Value
}
```

## KeyResult

```swift
public struct KeyResult: Codable, Sendable {
    public let ranges: [MusicUnderstandingSession.RangedValue<KeySignature>]
}
```

## KeySignature — tonic + mode

```swift
public struct KeySignature: Codable, Hashable, Sendable {
    public let tonic: Tonic
    public let mode: Mode
}
```

## Tonic — the chromatic pitches

```swift
@frozen public enum Tonic: String, Codable, Hashable, Sendable {
    case aFlat, aSharp, a, bFlat, b, c, cSharp, d, dFlat, dSharp, eFlat, e, f, fSharp, g, gFlat, gSharp
}
```

## Mode

```swift
public enum Mode: String, Codable, Hashable, Sendable {
    case major, minor
}
```

## RhythmResult — beats, bars, tempo

```swift
import MusicUnderstanding

public struct RhythmResult: Codable, Sendable {
    public let beats: [CMTime]
    public let bars: [CMTime]
    public let beatsPerMinute: Float?
}
```

## StructureResult — sections / segments / phrases

```swift
import MusicUnderstanding

public struct StructureResult: Codable, Sendable {
    public let sections: [CMTimeRange]
    public let segments: [CMTimeRange]
    public let phrases: [CMTimeRange]
}
```

## PaceResult — perceived energy over ranges

```swift
import MusicUnderstanding

public struct PaceResult: Codable, Sendable {
    public let ranges: [MusicUnderstandingSession.RangedValue<Double>]
}
```

## InstrumentActivityResult — presence ranges + intensity over time

```swift
import MusicUnderstanding

public struct InstrumentActivityResult: Codable, Sendable {
    public let ranges: [Instrument: [CMTimeRange]]
    public let activity: [Instrument: [MusicUnderstandingSession.TimedValue<Float>]]
}
```

## LoudnessResult — LUFS measurements

```swift
import MusicUnderstanding

public struct LoudnessResult: Codable, Sendable {
    public let integrated: MusicUnderstandingSession.TimedValue<Float>
    public let momentary: [MusicUnderstandingSession.TimedValue<Float>]
    public let shortTerm: [MusicUnderstandingSession.TimedValue<Float>]
    public let peak: MusicUnderstandingSession.TimedValue<Float>
}
```

## Streaming loudness — AsyncSequence property

```swift
import MusicUnderstanding

public var loudnessResults: some AsyncSequence<LoudnessResult, any Error> & Sendable
```

## Consuming streamed loudness with a custom audio provider

```swift
import MusicUnderstanding

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

## AudioProvider — an AsyncSequence of audio buffers

```swift
import MusicUnderstanding

struct AudioProvider: AsyncSequence, AsyncIteratorProtocol {
   func makeAsyncIterator() -> Self {
        return self
    }

   mutating func next() async -> AVReadOnlyAudioPCMBuffer? {
        // Return the next audio buffer, or nil to signal completion
    }
}
```

## Exporting results as JSON

```swift
import MusicUnderstanding

let session = try await MusicUnderstandingSession(asset: asset)
let results = try await session.analyze()

let encoder = JSONEncoder()
try encoder.encode(results)
```

## Video tile — time-per-clip from pace

```swift
let timePerClip = 60 / paceValue
```

---

## Useful API facts surfaced by the code

- Session init: `MusicUnderstandingSession(asset:)` (throwing, async) or `MusicUnderstandingSession(audioProvider:)`.
- Set `AVURLAssetPreferPreciseDurationAndTimingKey: true` on the `AVURLAsset` for accurate timing.
- `analyze()` returns all six results; `analyze(for:)` takes an array of analysis types (e.g. `[.loudness]`) and leaves the rest nil.
- `RangedValue` / `TimedValue` are nested under `MusicUnderstandingSession` (`MusicUnderstandingSession.RangedValue<…>`).
- `beatsPerMinute` is optional — nil until at least two beats are detected.
- Loudness windows: momentary = 400ms, shortTerm = 3s; momentary/shortTerm sampled every 100ms.
- Everything is `Codable` → `JSONEncoder().encode(results)` for export.
