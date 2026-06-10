# Session 237 — What's new in image understanding

- **URL:** https://developer.apple.com/videos/play/wwdc2026/237/
- **Duration:** 16m
- **Speakers:** Megan Williams (Vision framework team)

## Description

An overview of the new image understanding capabilities across Vision and Foundation
Models this year: Vision's tap-to-segment API for interactively isolating any object
in an image, image inputs for large language models via Foundation Models,
image-based tool calling (including built-in Vision tools), and Vision arriving on
watchOS.

## Key topics

- **Tap-to-segment** — `GenerateIterativeSegmentationRequest`: isolate any object by point tap, bounding box, lasso, or scribble; refine masks by adding/subtracting points; normalized lower-left coordinate system; lasso stroke width ≥ 1% of image width; on-device model must be downloaded first (`downloadAssets` / `assetStatus`)
- **Image inputs for Foundation Models** — pass an image as an `Attachment` in the prompt builder for captioning, scene understanding, recipe-from-fridge, interior design suggestions, agenda-from-sticky-notes
- **Vision vs Foundation Models** — LLMs are versatile (do almost anything you ask); Vision is a fixed set of task-tuned CV APIs, and fast enough for real-time video frames
- **Image-based tool calling** — tools conforming to `Tool` with an `ImageReference` argument; resolve the reference via the `history` session property → `Transcript` → `ImageAttachment` → `pixelBuffer()`; label attached images so the model knows which to pass
- **Built-in Vision tools** — `BarcodeReaderTool` (barcodes/QR) and OCR tool (fine/dense text, 30+ languages); `import Vision`, add to the session's tools
- **Vision on watchOS** — saliency (`GenerateObjectnessBasedSaliencyImageRequest`) to find and crop the salient subject for the small watch screen
- **Vision breadth** — 30+ analysis types: segmentation, facial analysis, pose estimation, detection, image classification, trajectory analysis, object tracking

## Related sessions to fetch (referenced in this talk)

- [ ] Deep dive into the Foundation Models framework
- [ ] Discover Swift enhancements in the Vision framework
- [ ] What's new in the Foundation Models framework (session 241 — already in workspace)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Megan Williams; the "missing agenda" cold open uses Foundation Models image input to build an agenda from a photo of sticky notes. Agenda: tap-to-segment, image inputs for LLMs, image-based tool calling, Vision on watchOS.
- **1:36 Segment images with tap-to-segment** — Vision already has person segmentation; new tap-to-segment lets you isolate any object via point/box/lasso/scribble and refine masks. `ImageRequestHandler` + `GenerateIterativeSegmentationRequest(seed:)`; normalized coordinates (lower-left origin, 0–1); lasso width ≥ 1% of image width; download the model first.
- **5:50 Image inputs for Foundation Models** — pass images directly to the LLM via `Attachment` in the prompt builder; captions, scene understanding, recipes, interior design; Vision-vs-Foundation-Models comparison (versatility vs speed/specialization).
- **7:57 Image-based tool calling** — refresher on tool calling (weather example); image arguments via `ImageReference`; resolve through `history` transcript; built-in `BarcodeReaderTool` and OCR tool; label images for tool calls; Vision supports 30+ analysis types.
- **13:09 Vision on watchOS** — wildlife watch app; saliency analysis crops the photo to its main subject for the small screen via `GenerateObjectnessBasedSaliencyImageRequest`.
- **14:39 Next steps** — recap of all four capabilities; downloadable tap-to-segment and watchOS sample apps; pointers to the Vision enhancements and Foundation Models sessions.

## Code

See `code.md` — 5 snippets extracted from the Code tab.
