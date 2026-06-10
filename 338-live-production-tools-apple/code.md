# Code samples — Session 338

One snippet from the Code tab. Timestamp maps to the transcript.

## 13:09 — Signal Apple Immersive Video when writing a MOV video track

Set the new VideoToolbox projection-kind constant in the compression properties so
AVAssetWriter stamps the correct video extended usage (vexu) static metadata into the
file, marking it as Apple Immersive Video for other applications.

```swift
import VideoToolbox

let compressionProperties: [String: Any] = [
    // ...
    kVTCompressionPropertyKey_ProjectionKind as String: kVTProjectionKind_AppleImmersiveVideo
    // ...
]
```

## Spoken APIs

Concrete APIs / standards named in the transcript:

- **AVFoundation** — `AVAssetWriter` (writes ProRes video tracks, PCM audio tracks, and MEBX metadata tracks into the MOV)
- **VideoToolbox** — `kVTProjectionKind_AppleImmersiveVideo`, set via `AVVideoCompressionPropertiesKey`; writes **vexu** static metadata
- **Core Audio** / **AudioToolbox** — uncompressed PCM audio handling
- **Immersive Media Support (IMS)** — creates lens-calibration objects, camera IDs, and other metadata objects written into the MOV, synchronized with video/audio; reads/writes AIV metadata; previewing (introduced visionOS 26)
- **ProRes** — codec for the immersive video essence (see "Apple ProRes" developer docs)
- **ASAF** (Apple Spatial Audio Format) — uncompressed PCM tracks carrying high-order ambisonic beds + spatial audio objects (64+ channels)
- **SMPTE 2110** — `2110-22` (compressed video over IP, both eyes as two data essences in one stream), `2110-30` (audio), `2110-41` (user-defined metadata); multicast **RTP** transport
- **MOV / QuickTime** container — video tracks, audio tracks, **MEBX** (Metadata Box Exchange) tracks
