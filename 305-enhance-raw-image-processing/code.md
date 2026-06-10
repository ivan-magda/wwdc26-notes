# Code samples — Session 305

Extracted from the Code tab. Timestamps map to the transcript.

## 10:52 — Export CIContext (no caching, raised memory limit)

```swift
let exportCtx = CIContext(options : [
  .cacheIntermediate : false,
  .memoryLimit : 512 ])
```

## 11:50 — CIImageProcessor with explicit output tile sizes

```swift
import CoreImage

class MyProcessor: CIImageProcessorKernel {
    override class func roi(forInput input: Int32,
                            arguments: [String : Any]?,
                            outputRect: CGRect) -> CGRect { return outputRect }

    override class func process(with inputs: [CIImageProcessorInput]?,
                                arguments: [String : Any]?,
                                output: CIImageProcessorOutput) throws {
        guard let input = inputs?.first,
              let iBuffer = input.pixelBuffer,
              let oBuffer = output.pixelBuffer else { return }

        let iRegion = input.region
        let oRegion = output.region // controlled by Core Image

        // MyCopyBuffer(iBuffer,iRegion, oBuffer,oRegion)
    }
}

let extent = inImg.extent
let tileSize = 512.0 // whatever tile size you want
var tiles: [CIVector] = []
for y in stride(from: extent.minY, to: extent.maxY, by: tileSize) {
    for x in stride(from: extent.minX, to: extent.maxX, by: tileSize) {
        let tile = CGRect(x: x, y: y,
                          width: min(tileSize, extent.maxX - x),
                          height: min(tileSize, extent.maxY - y))
        tiles.append(CIVector(cgRect: tile))
    }
}

let result = try MyProcessor.apply(withTiledExtent: tiles, inputs: [inImg], arguments: [:])
```

## 13:45 — CIImageProcessor with recyclable temporary buffers

```swift
import CoreImage

class MyProcessor: CIImageProcessorKernel {
    override class func process(with inputs: [CIImageProcessorInput]?,
                                arguments: [String: Any]?,
                                output: CIImageProcessorOutput) throws {
        guard let input = inputs?.first,
              let srcPixelBuffer = input.pixelBuffer,
              let dstPixelBuffer = output.pixelBuffer else { return }

        // Get a scratch buffer from Core Image's cache
        guard let scratch = output.temporaryPixelBuffer(identifier : "myScratch",
                   format: kCVPixelFormatType_64RGBAHalf,
                   width: Int(output.region.width),
                   height: Int(output.region.height),
                   pixelBufferAttributes: nil) else { return }

        // Step 1: copy input CVPixelBuffer → scratch
        // Step 2: process pixels in scratch
        // Step 3: copy scratch → output CVPixelBuffer
    }
}
```

---

## Spoken APIs (named in transcript, not in Code tab)

- `CIRAWFilter` — load a RAW file for advanced editing.
- `CIRAWFilter.supportedDecoderVersions` — check it contains `.version9`.
- `CIRAWFilter.decoderVersion` — set to `.version9` to opt in (not enabled by default).
- `CIRAWFilter.supportedCameraModels(...)` — class method returning the array of camera models supported for a given version.
- Editing properties: `exposure`, `luminanceNoiseReductionAmount`, `sharpnessAmount`, `contrastAmount` (top four of 20 calibrated properties).
- Retired in RAW 9: `colorNoiseReductionAmount` (no effect), `detailAmount`, `moireReductionAmount` (unsupported). Guard with the `isSupported` property checks.
- `CIRAWFilter.scaleFactor` — render at reduced size for interactive editing.
- `CIContext` options: `cacheIntermediates`, `memoryLimit`.
- `CIContext.heifRepresentation(...)` / `CIContext.jpegRepresentation(...)`.
- Extended Virtual Addressing entitlement (more caching memory).
