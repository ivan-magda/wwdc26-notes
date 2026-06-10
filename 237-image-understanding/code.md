# Code samples — Session 237

Extracted from the Code tab. Timestamps map to the transcript.

## ~4:15 — Tap-to-segment: generate and refine a mask

```swift
// Generate a segmentation mask of an object with a seed point
let handler = ImageRequestHandler(image)
let request = GenerateIterativeSegmentationRequest(seed: point)
let observation = try await handler.perform(request)
let mask = observation?.pixelBuffer

// Refine the mask with a new point
request.addIncludedPoint(newPoint)
let refinedObservation = try await handler.perform(request)
```

## ~6:40 — Image caption with Foundation Models

```swift
// Generate an image caption with Foundation Models
import FoundationModels

let prompt = Prompt {
    "Generate a caption for this image"
    Attachment(image)
}
let response = try await session.respond(to: prompt)
let caption = response.content
```

## ~9:52 — Image-based tool (ImageReference + history resolution)

```swift
// Create an image-based tool
struct PlantIdentifierTool: Tool {
    @SessionProperty(\.history) var history

    @Generable
    struct Arguments {
        var image: ImageReference
    }

    func call(arguments: Arguments) async throws -> String {
        let imageReference = arguments.image
        let transcript = Transcript(history)
        guard let imageAttachment = imageReference.resolve(in: transcript) else {
            throw AppError.imageNotFound
        }
        let image = try imageAttachment.pixelBuffer()
        return classifyPlant(image)
    }
}
```

## ~12:09 — Using built-in Vision tools (BarcodeReaderTool) with a labeled attachment

```swift
// Use Vision tools
import FoundationModels
import Vision

let session = LanguageModelSession(model: model, tools: [BarcodeReaderTool()])
let response = try await session.respond(generating: EventInfo.self) {
    "Get the date, location, and website from this flyer"
    Attachment(image)
        .label("flyer")
}
```

## ~13:53 — Saliency crop on watchOS

```swift
// Create a crop that highlights a prominent subject
func generateImageCrop(in image: CGImage) async throws -> NormalizedRect? {
    let request = GenerateObjectnessBasedSaliencyImageRequest()
    let observation = try await request.perform(on: image)
    let prominentObjects = observation.salientObjects
    return prominentObjects.first
}
```

---

## Useful API facts surfaced by the code

- **Tap-to-segment:** `ImageRequestHandler(image)` + `GenerateIterativeSegmentationRequest(seed: point)`; result `observation?.pixelBuffer` is the mask. Refine with `request.addIncludedPoint(_)` (and, per the demo, point exclusion) then re-`perform`.
- **Image input:** `Attachment(image)` inside a `Prompt {}` / `respond {}` builder; `.label("…")` is required when you want the model to make an image-based tool call.
- **Image tools:** argument type `ImageReference`; pull the conversation via `@SessionProperty(\.history)`, build a `Transcript`, `imageReference.resolve(in: transcript)` → `ImageAttachment` → `.pixelBuffer()`.
- **Built-in Vision tools:** `BarcodeReaderTool()` and an OCR tool, passed in the session's `tools:` array after `import Vision`.
- **Saliency:** `GenerateObjectnessBasedSaliencyImageRequest()` → `observation.salientObjects` (array, most-prominent first); each entry is a `NormalizedRect` bounding box.
