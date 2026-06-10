---
title: "Integrate on-device AI models into your app using Core AI — Full Digest"
session: WWDC26 · 326
url: https://developer.apple.com/videos/play/wwdc2026/326/
duration: 24m
speakers: Carina (Core AI team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 326 — Integrate on-device AI models into your app using Core AI

## TL;DR

Core AI is Apple's new stack for running advanced AI models **entirely on device** —
no server, no cost per token, no cloud latency, and user data never leaves the device.
The session is built around one end-to-end demo: a Mandarin vocab-learning app that
turns "point your camera at an object + a prompt" into a finished vocab card. Five
takeaways:

1. **Compose small, task-specific models.** Instead of one big model, the app pairs
   **SAM 3** (a vision-transformer for promptable image segmentation, the card graphic)
   with **Qwen 0.6B** (a 119-language reasoning LLM for translation + example
   sentences). Both target **under 1B parameters** to fit iPhone storage/memory.
2. **The `coreai-models` repo is the fast path.** Popular models come with ready-made
   export recipes that emit optimized `.aimodel` files, plus a Swift package of runtime
   libraries (`CoreAILM`, `CoreAISegmentation`) that hide tensor pre/post-processing.
3. **It reuses the Foundation Models API.** Once a Core AI model is loaded, you drive it
   through the same `LanguageModelSession` you already know — `respond(to:)`, streaming,
   and `@Generable` structured output all work, just with *your* model underneath.
4. **Specialization is the latency gotcha.** The first load of a model compiles/caches a
   device-specific version ("specialization"). The new **Core AI Instruments** template
   surfaces it; **ahead-of-time compilation** (`coreai-build`) plus a deliberate
   deployment strategy (first-run UX + Background Assets) keep it out of the hot path.
5. **The same code is multiplatform.** Identical Swift runs on macOS, where more memory
   lets you step up to **Qwen3 8B**, batch-process photo folders, and use longer context
   for pinyin and full curriculum generation.

---

## 1. The app concept (1:16)

The starting point is a hand-curated Mandarin vocab deck — word, translation, example
usage — that doesn't scale and would have to ship statically. The AI version: a student
points their camera at something in their garden or on the street, asks the app to pull
it out of the scene, and gets a generated vocab card in the language they're learning.
Everything runs locally; every card features something from the student's own life and
the collection grows with them.

The plan: (1) identify models, (2) write the integration code, (3) handle deployment
considerations, (4) bring it to macOS by reusing the same code with larger models.

## 2. Model discovery (2:52)

The app's pipeline: **picture + user prompt → segmented cutout (card graphic)** and
**native-language text → translation + natural example usage + English meaning.**

Three selection requirements:

- **Content** — real-world settings (kitchens, streets, offices).
- **Languages** — multilingual architecture from the start; initial release scoped to
  Mandarin Chinese.
- **Device constraints** — on-device on iPhone, so keep storage *and* memory small; be
  deliberate about model size and **how many** models ship.

The conclusion: **decompose into two small models.**

- **SAM 3 (Segment Anything Model 3)** — vision-transformer for promptable image
  segmentation. The student's prompt isolates the object precisely and yields a clean
  cutout; the prompt also supplies an English label for the LLM.
- **A multilingual reasoning LLM** — needs four properties: multilingual (accurate
  translations), reasoning (contextual example sentences, not just translations),
  structured output (typed fields), and compact (fits alongside the vision model).
  **Qwen** stood out — **119 languages/dialects**, a reasoning model, and a **0.6B**
  variant. Both models target **<1B parameters** each.

Rationale for two models over one: better per-task quality, smaller individual sizes,
and the ability to **upgrade each model independently.** Models and docs were found on
**HuggingFace and GitHub.**

## 3. Getting models into the app (7:13 / 7:40)

Two paths:

- **Convert from PyTorch** with the **Core AI PyTorch extensions** package, optionally
  adding compression via the **Core AI optimization** package. (Deep dive: *"Dive into
  Core AI model authoring and optimization"* — which even shows converting SAM 3.)
- **The `coreai-models` repo** — the easier path for popular models. Layout:
  - `models/` — the catalog; browse, pick a model, follow its **export recipe**, get an
    optimized `.aimodel` with optional platform-specific variants.
  - `python/` — reusable export primitives/utilities.

  SAM 3 and the Qwen family were both found here and exported via their recipes.

## 4. Integration: inspecting `.aimodel` and adding the package (8:37)

Export produces `.aimodel` files. Inspected in **Xcode**, the SAM 3 model shows:

- Size **623 MB**; platform targets **iOS 27.0 / macOS 27.0**; metadata.
- A **Functions** tab exposing the model's interface — SAM 3 has **three functions**.
  - `imageEncode` — input is a **tensor** with a specific shape and data type (not just
    "an image"); output is a dense feature embedding.
  - `detect` — takes image features + a text prompt; outputs **raw masks, bounding
    boxes, and confidence scores**.

Using the raw model directly would mean writing all the pre-processing (camera frame →
tensor) and post-processing (raw tensors → meaning). Instead, the `coreai-models` repo
ships a **Swift package of runtime libraries** that handle text encoding in and mask
extraction/labeling out. Add the `coreai-models` URL as a Swift Package and select the
**`CoreAILM`** and **`CoreAISegmentation`** library products for the app target.

## 5. Writing the Swift integration code (10:55)

### Segmentation (SAM 3)

```swift
import CoreAIImageSegmenter

// Load
let segmenter = try await ImageSegmenter(resourcesAt: sam3ModelURL)

// Use
let response = try await segmenter.segment(image: inputImage, prompt: "flower")
let mask = response.segments.first?.mask
```

Load from disk, run text-prompted segmentation on a prompt like `"flower"`, extract the
best mask.

### Language model (Qwen) — one-line load, familiar session API

```swift
import FoundationModels
import CoreAILanguageModels

// Create model instance
let model = try await CoreAILanguageModel(resourcesAt: qwen3ModelURL)

// Create session using the model
let session = LanguageModelSession(model: model)

// Generate response
let response = try await session.respond(to: "...")
```

The single `CoreAILanguageModel(resourcesAt:)` line abstracts **asset loading, engine
creation, and tokenizer setup**. The key reuse story: `import FoundationModels`, then a
standard `LanguageModelSession` — the **same API that backs Apple's on-device model**,
now passed your own model. Same `respond(to:)`, same streaming, same structured output.

### Guided generation with `@Generable`

```swift
import FoundationModels
import CoreAILanguageModels

@Generable
struct VocabCard {
    let chineseWord: String
    let englishMeaning: String
    let exampleSentence: String
}

let model = try await CoreAILanguageModel(resourcesAt: modelURL)
let session = LanguageModelSession(model: model)
let response = try await session.respond(
    to: "Create a vocab card for flower",
    generating: VocabCard.self
)
let card: VocabCard = response.content
```

The `@Generable` macro pins the output to typed fields (word, translation, example
sentence) instead of free-form text.

## 6. Diagnosing specialization latency (13:05)

In the demo, the first inference stalls on a spinner. A trace with the **new Core AI
Instruments** template shows a model-load event with a large **specialization**
sub-event. Specialization prepares a Core AI model for on-device execution; on load the
runtime checks whether the model has already been specialized and **cached**. It can take
significant time for large models. **Future loads hit the cache and are fast** — only the
first time needs planning. Running it mid-experience is bad UX, so it should move out of
the interactive flow.

## 7. Deployment strategy (14:40)

Goals: the feature ships as an **update** to an existing app, **discoverable but not
required** — opt-in users get a great experience, everyone else is unaffected.

- A **first-run experience** is a natural place to explain the feature and absorb the
  first-launch model-loading/specialization cost.
- Bundling the models would add **over 1 GB** to the download — hitting *everyone* who
  updates, including people who never use the feature.
- So the feature intro screen has a button that **only triggers the model download if
  the user opts in**, using **Background Assets** (deep dive: *"Discover Apple-Hosted
  Background Assets,"* WWDC25). It requests assets, shows download progress, then kicks
  off specialization.

## 8. Ahead-of-time (AOT) compilation (17:00)

Even off the hot path, specialization is slow. It has two transformations: a **set of
compilation steps** (the expensive part), then **executable artifact generation**
(artifacts are tied to the specific device + OS version). The Core AI toolchain lets you
do the **compilation ahead of time on your dev machine** via `coreai-build`:

```bash
$ xcrun coreai-build compile MyModel.aimodel --platform iOS
```

It produces one or more compiled models targeting specific **device architectures**. The
compiled model still specializes on the user's device, but with **much less work**, so it
finishes significantly faster. In the app: create a **Background Asset per compiled
model**, add a small amount of code to **detect the device architecture** and request the
matching asset. (Details: *"Compiling Core AI models ahead of time"* on developer.apple.com.)

### iOS demo (18:03)

With AOT done, model preparation is a fraction of the earlier time. SAM 3 segments
real desk objects (rocks, a piece of wood, a sunflower); Qwen generates Mandarin vocab
cards with example usage that the user saves to a collection. **Subsequent inferences use
the cached model asset** and are seamless.

## 9. Multiplatform — same code, bigger models (19:57)

The iOS build (SAM 3 + Qwen 0.6B) is reused **unchanged** on macOS. On the Mac the use
case shifts from learning one word at a time to **curation**:

- Add a **batch processing** layer to generate cards for a whole folder of trip photos in
  one go — segmentation is **parallelized** across photos, and one photo can yield
  multiple cards.
- Step up to a **larger variant of the same model — Qwen3 8B** — for better reasoning and
  higher-quality output: richer prompts, multiple example sentences, **pinyin** (the 8B
  model visibly "thinks" and checks pinyin correctness, which is easy to get wrong).
- Use **longer context** to go beyond single cards: hand the model a whole category and
  have it build a **curriculum** — sequence simple→complex, group into lessons, and write
  examples that reuse earlier vocab. One prompt → a structured lesson plan, plus cards to
  distribute back to the iOS app.

"The same code, calling the same API, just a more capable model underneath."

## 10. Wrap-up (23:06)

Core AI gives you everything to build private, multi-platform on-device AI: no server, no
cost per token, no cloud latency. "The models are ready. The tools are ready."
