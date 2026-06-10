---
title: "What's new in image understanding — Full Digest"
session: WWDC26 · 237
url: https://developer.apple.com/videos/play/wwdc2026/237/
duration: 16m
speakers: Megan Williams
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 237 — What's new in image understanding

## TL;DR

Four new ways to understand images on Apple platforms this year, spanning Vision and
Foundation Models:

1. **Tap-to-segment (Vision)** — interactively isolate *any* object in an image with a
   point tap, bounding box, lasso, or scribble, then refine the mask by adding or
   subtracting points. New `GenerateIterativeSegmentationRequest`.
2. **Image inputs for LLMs (Foundation Models)** — pass an image straight into the
   prompt builder as an `Attachment` for captioning, scene understanding, recipes,
   interior design, and more.
3. **Image-based tool calling** — give an LLM a tool that takes an `ImageReference`
   argument, plus two **built-in Vision tools** (`BarcodeReaderTool` and an OCR tool)
   that hand the model capabilities it can't do on its own.
4. **Vision on watchOS** — Vision is now available on the watch; the demo uses
   saliency to auto-crop a wildlife photo to its subject for the tiny screen.

The framing throughout: Vision is a fast, fixed set of task-tuned computer-vision APIs;
Foundation Models is a versatile LLM. Tool calling is the bridge — let the LLM call
Vision for the things models are bad at (barcodes, dense OCR), and you get the best of
both.

---

## 1. Tap-to-segment

Vision already had segmentation (e.g. person segmentation isolates all the people in an
image), but you couldn't pick an arbitrary object. Tap-to-segment fixes that — choose a
flower vase, a board game, a piece of clothing, even the floor.

**Selection methods shown in the demo (cafe photo: cup, plate, croissant):**

- **Point** — tap a single point on the object (e.g. the coffee cup) to isolate it. Good for simple objects.
- **Bounding box** — draw a box around several objects to capture them together (cup + plate).
- **Lasso** — draw a loop around an object (the croissant).
- **Scribble** — scribble across multiple objects to segment them all at once.

**Refining a mask:** add points to include more (tap the plate to add it to the cup) or
subtract points to exclude regions (exclude the cup to keep just the coffee).

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

The mask is a `PixelBuffer` marking which pixels belong to the segmented object.

**Things to keep in mind:**

- **Normalized coordinates**, origin in the **lower-left** corner; point values between **0 and 1**, normalized to image width and height.
- **Lasso stroke width** must be wide enough — at least **1% of the total image width**. Thin strokes give poor results.
- **Model download required** before the first segmentation on a device. Kick it off with the `downloadAssets` API and check `assetStatus` to see if the model is ready.

## 2. Image inputs for Foundation Models

The cold open is the hook: the presenter's agenda slide is "missing," so she photographs
her sticky notes and asks an LLM to generate an agenda from the photo. That's the new
capability — **Foundation Models supports image inputs this year.**

Other example tasks named: captions for images in your app, interior-design suggestions
for a living room, and a recipe generated from a photo of your fridge. Models are
especially good at descriptive tasks.

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

The pattern: a text prompt with instructions on how to process the image, then the
image included as an `Attachment` in the prompt.

### Vision vs Foundation Models — when to use which

- **Foundation Models** leverages an LLM — versatile, "can do almost anything you ask."
- **Vision** is a fixed set of computer-vision APIs, **fine-tuned for specific tasks** and **fast** — often fast enough for **real-time video frames**.
- You don't always have to choose: **tool calling** lets you combine Vision's expertise with the LLM's versatility (next section).

## 3. Image-based tool calling

**Refresher (weather example):** with tool calling the model can invoke external code to
get a result it couldn't produce itself. A weather question → model makes a tool call,
generating the arguments the tool needs (the date) → tool returns the forecast → model
answers. (Deep dive: *"Deep dive into the Foundation Models framework."*)

**New this year: tool calls support image arguments.** Example: a plant photo. If the
model can't identify the plant itself, your plant-identifier tool can. Crucially, the
model **passes a *reference* to the image, not the whole image**, and the tool resolves
that reference back into pixels.

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

Key mechanics:

- Argument type **`ImageReference`** signals the model to pass a reference to an existing image from the current chat session.
- An `ImageReference` is only valid in the context of **the transcript it was generated from**. Access that transcript through the **`history` session property**.
- Resolve: `imageReference.resolve(in: transcript)` → `ImageAttachment` → `.pixelBuffer()` for analysis.

### Built-in Vision tools

For common tasks Vision ships ready-made tools, so you don't have to write them:

- **`BarcodeReaderTool`** — barcodes and QR codes. Demo: an event flyer; the model finds the date and location on its own but **can't read the QR code** until the barcode tool is enabled — then it returns the registration website.
- **OCR tool** — for **fine or dense text**; reads **over 30 languages**.

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

- `import Vision`, then add the tools to the session.
- **Label your attached images** when you want an image-based tool call — the **label is how the model identifies which image to pass** to the tool.

### Vision is broad

You can also build your own Vision-backed tools. Vision supports **30+ types of image
analysis**, including segmentation, facial analysis, pose estimation, detection, image
classification, trajectory analysis, and object tracking. (Full list:
*"Discover Swift enhancements in the Vision framework."*)

## 4. Vision on watchOS

Vision is now available on watchOS. The demo is a wildlife app for hikes: you pick an
animal and it shows a photo — but the watch screen is small and the subject is hard to
see. **Saliency analysis** finds the subject of interest, and the app crops to feature
it prominently.

```swift
// Create a crop that highlights a prominent subject
func generateImageCrop(in image: CGImage) async throws -> NormalizedRect? {
    let request = GenerateObjectnessBasedSaliencyImageRequest()
    let observation = try await request.perform(on: image)
    let prominentObjects = observation.salientObjects
    return prominentObjects.first
}
```

`observation.salientObjects` returns bounding boxes of detected salient objects; take
the most prominent (first) and crop to it for a zoomed-in view that reads well on the
watch.

## Recap

- **Tap-to-segment** — interactively segment any object in an image.
- **Image inputs** — Foundation Models LLMs now analyze images in ways that weren't possible before.
- **Tools** — wire frameworks like Vision into your LLM to sharpen image analysis.
- **Vision everywhere** — including watchOS.
- Downloadable **sample apps** for tap-to-segment and watchOS Vision are on the developer website.
