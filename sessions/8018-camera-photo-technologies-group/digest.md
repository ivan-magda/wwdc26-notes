---
title: "Camera and Photo Technologies Group Lab — Full Digest"
session: WWDC26 · 8018
url: https://developer.apple.com/videos/play/wwdc2026/8018/
duration: 00:59:31
sources: transcript.md (whisper.cpp ggml-large-v3, SD video audio), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and context. Where a name or API spelling is uncertain it is flagged.
---

# WWDC26 · 8018 — Camera and Photo Technologies Group Lab

> A 59-minute live developer Q&A with a five-engineer panel from Apple's Camera and
> Photos teams. It's the practical layer under the WWDC26 camera sessions rather than a
> feature talk. Which framework to reach for, what the APIs actually guarantee, what's a
> deliberate limitation versus a bug, and a steady "file a Feedback Assistant request,
> we actually read them." Heavy lean on existing sample code and on three or four
> recorded sessions from this same week.

## TL;DR

- **`.balanced` and `.quality` photo prioritization override your manual exposure/ISO.**
  In photo mode, only `.speed` preserves manual shutter/ISO. Quality and balanced run
  fusion (multiple exposures), so you are handing exposure decisions to the system. If
  manual settings *seem* respected at quality, "you're getting lucky." Corrected on-air.
- **ProRAW is sensor-agnostic; native Bayer RAW from a quad-Bayer sensor is binned.**
  ProRAW is already debayered, linearized RGB after a Photonic Engine multi-image merge,
  so it does not care whether the sensor is Bayer or Quadra and can output 48MP. Raw
  Bayer from a quad-Bayer sensor is the *binned* (quad-to-Bayer) resolution. Quad-Bayer
  RAW DNG is **not offered** because Apple would have to ship decode support across the
  ecosystem (CIRAWFilter etc.), and "debayering Quadra is a completely different beast."
- **AVCaptureSession syncs audio and video off the same clock** on modern iPhones; if you
  don't handle AV sync you can ship something that's fine until it runs on an iPad with an
  external camera and drifts. Attach both device inputs to one session and the PTSs come
  out on a shared timeline. If you must use a separate audio API (AU Remote IO), use
  `CMClock` / `CMSyncConvertTime`, and **sync video to the audio clock** because audio
  glitches are more perceptible than micro video timing shifts.
- **Two rookie camera-app mistakes, stated bluntly:** running `AVCaptureSession` on the
  main thread (it blocks during graph reconfig, gives you spinners and one-star reviews;
  use a dedicated serial queue), and not wrapping multi-step reconfiguration in
  `beginConfiguration` / `commitConfiguration` (described as an ATM transaction: do 1 or
  20 things, the graph re-evaluates only at commit).
- **AVProVideoStorage** (new this week) is the "niche" pick: a pre-allocated file giving
  deterministic write speeds so ProRes capture (e.g. 4K30) doesn't drop frames, even on an
  older phone with a fragmented disk. It's being **retrofitted to older phones**.
- **AI edits are tagged.** Clean Up and Spatial Reframe write IPTC + EXIF metadata, and the
  specific edit appears in the Photos info panel.
- **RAW 9** lands in the 27 OS: a new **ML-based RAW engine** for debayering and processing
  third-party-camera RAW, covered in the "Enhanced RAW image processing" Core Image session.

## Panel

Host **Sergey** (Developer Relations) with **Matt Decoff** (Photos Frameworks team),
**Brad Ford** (Camera Software, 25 years, "every single iPhone," AVFoundation capture),
**Ivan Cabero-Belabonde** (Camera Software, still-capture pipeline and depth; surname
uncertain in transcript), **Davide Conchon** (file format, compression, RAW; 19 years;
surname uncertain), and **Jake** (camera performance, 5 years). A behind-the-scenes team
triaged questions.

## Favorite camera/photos features (warm-up, 01:40–05:26)

- **Live Photos** (Matt) — a "slice of time" photo plus video and sound; the host calls it
  "the OG" feature; one panelist's mom didn't realize for years she was already shooting them.
- **System-wide video effects** (Brad) — Portrait, Studio Light, Center Stage, and newer
  **background replacement** and **gestures**, available free in any app on the Mac during
  meetings. Plus "all the new features on the iPhone 17 front-facing camera."
- **Opportunistic depth capture / "portrait in photo"** (Ivan) — his own feature from a few
  years back: when a person is in the scene a little "f" appears, you get depth on capture,
  and can add the portrait effect later with full controls. The release also made depth
  processing far more efficient, available at all zoom levels, and able to use deferred
  processing — all adoptable by third-party camera apps.
- **ProRAW** (Davide) — worked on it "for so long"; loves that it's the vehicle giving
  everyone (including third parties) the full spectrum of iPhone image quality plus latitude
  to edit to personal taste.
- **The new Tele on iPhone 17 Pro** (Jake) — the 40X zoom; shot people in the Yosemite valley
  from Glacier Point ("they kind of look like ants").

## Developer Q&A

### AI-edit provenance & Photos metadata
- **Does iOS 27 tag AI edits (Clean Up / Spatial Reframe) with metadata or content
  credentials so you can tell it was AI-edited? (Florent).** Yes. The file's **IPTC metadata
  (alongside EXIF)** is updated, and the IPTC value reflects *which* AI modification was done
  (Spatial Reframe vs Clean Up). In the **Photos app info panel** (swipe up at the bottom on
  iOS) the app surfaces which edit was used. (Davide for the metadata; Matt on the info panel.)
- **Keywords API for third-party apps? (asked, Matt notes the asker may be "Ben").** Keywords
  came to iOS Photos this year (long present on Mac); visible/editable in the info panel, with
  UI to manage and search by them, and reflected in **IPTC metadata on export**. But there is
  **currently no PhotoKit-level API** to fetch or query by keyword. (Matt)
- **`PHAsset.originalResourceChoice` — what's it for? "Not much documentation yet."** It's a new
  API for an existing RAW+JPEG feature (DSLR-style "RAW + JPEG" or HEIC-compressed-plus-RAW
  imports show RAW / R+J badges). It decides **which resource is "the original"**: do your edits
  and the smaller derivatives/thumbnails source from the RAW or the compressed image, and you can
  swap between them. Related: `PHContentEditingInputSource` to choose, and a **change request on
  the asset** to toggle which resource is original. (Matt)
- **`PHAsset` new `rating` property — does it need a PHLibrary change request?** Yes, it's a
  **PHAsset change request**; the `rating` is a new enum with **unset and 1–5**. (Matt)
- **Native way to get the original file type (PNG/JPEG) of a picked photo? It always comes out
  PNG.** Likely a **UTType issue** with `Transferable`: set the UTType explicitly rather than
  leaving the default image type. Sample code on developer.apple.com from an older PhotosPicker
  session covers this. Note the picker *can* convert some formats (e.g. RAW) if the user disabled
  captions/location for your app, but PNG output points at the UTType. (Matt)
- **Can you read the adjustment PList format Photos writes, and re-import exported adjustments?**
  No API for that; you cannot decode that PList. File a feedback request. (Matt)

### Capture performance, deferred start & responsive capture
- **Can deferred start cause the user to attempt a capture before the photo output is attached?
  (Ben).** Yes, that's the risk if you only defer init. The fix: set
  **`isResponsiveCaptureEnabled = true`** alongside deferring; the system adds buffering, so you
  launch fast, the shot gets queued even before the output is fully initialized, and you don't
  miss the moment. Deferred start (introduced **iOS 26**) tells your session outputs which to
  start immediately (usually preview) and which to defer. Covered in Jake's session
  **"Build a responsive camera app that launches quickly"** (with a dominoes demo). (Jake, Brad)
- **Deferred start vs `setPreparedPhotoSettingsArray` — do they collide? What does each reserve?**
  They're **orthogonal**. Deferred start just moves initialization from before preview to after
  (resolve preview branch first, let the photo/movie branches finish when they finish). The
  prepared-settings array (long-standing) tells the photo output the **worst case up front** so it
  can pre-allocate the still pipeline; you can re-prepare any time. They complement each other.
  Note photo-output **quality affects launch time** if you *don't* use deferred start (speed
  launches faster than quality because of the heavy allocations). (Jake, Brad)
- **Biggest mistakes building camera-heavy apps?** (1) Running `AVCaptureSession` on the main
  thread — it blocks during reconfiguration; use a dedicated serial queue. (2) Not wrapping
  multi-step reconfiguration in **`beginConfiguration` / `commitConfiguration`** (the graph
  otherwise re-evaluates on each disruptive property set). (3) Using **video data output to render
  preview** when you don't need the buffers: you can drop frames doing heavy work in the callback.
  Use **`AVCaptureVideoPreviewLayer`** — it's a highly optimized path you won't beat unless you
  need to inspect/draw on/meter the buffers. (Brad, Jake)
- **Must-have recommendations for an app that "lives and dies by its capture experience"?** A
  spread of answers: **performance first** (fast launch, responsive capture, deferred processing —
  "if you miss the shot you can't get the moment back"); **make it pro** (add pro capabilities);
  and a counterpoint that "lives or dies by capture" is **vague** — a social-video app's optimal
  experience is nothing like a pro photography app's, and fun apps (the Hipstamatic example) are
  valid too. Shared takeaway: **AVFoundation is huge** ("second-largest framework in iOS after
  UIKit"), so **start from the sample code, not from scratch** (a common bug is the main-thread
  session). And lean on PhotoKit: by default your app has no library access; request **save-only**
  first (most users accept), then upgrade to read access via prompts. (Jake, Davide, Brad, Matt)

### Depth, ProRAW & Bayer RAW
- **Best way to get the depth map + image for a live viewfinder and the captured image, for a 3D
  bokeh effect? (Yelion).** Two paths. **Preview:** enable depth data output on the preview
  stream and use the **AVCaptureDataOutputSynchronizer** (transcript "AVCaptureSynchronizer") to
  keep RGB and depth aligned; or the shortcut — turn on **cinematic video capture** (introduced
  last year), set `isCinematicVideoCaptureEnabled` on the device input with a video preview layer,
  and you get the live depth effect for free. **Still:** on `AVCapturePhotoOutput`, set
  **`depthDataDeliveryEnabled = true`** in the photo settings, then composite the blur with a Core
  Image filter. For DIY depth, use video data output + depth data output + a data output
  synchronizer so RGB and depth land in one callback at the same timestamp. (Ivan, Brad)
- **Can you capture 24MP with depth on the front camera? (Joshua Arzensek).** Yes. **24MP
  processing requires opting into deferred processing**, and you must opt into **max photo
  dimensions** (and confirm the selected device supports 24MP). With quality prioritization +
  `depthDataDeliveryEnabled`, you get 24MP captures with depth. Covered in this year's
  high-resolution-capture session. (Ivan)
- **Why does ProRAW support 48MP from a quad-Bayer sensor while native Bayer RAW is limited to
  the binned resolution? (user "from you number 16," transcript unclear).** Because **ProRAW is
  already debayered/linearized RGB** (after the Photonic Engine multi-image merge), so it's
  sensor-format-agnostic and can be linear/48MP. Native Bayer RAW from a quad-Bayer sensor is the
  **binned** result (quad pixels binned to Bayer). A quad-Bayer RAW would be *quad-Bayer*, not
  Bayer, and Apple can't ship that DNG without shipping decode support (CIRAWFilter etc.) — an
  **ecosystem** lift, and "debayering Quadra is a completely different beast" than 25 years of
  Bayer debayering. Not available today; **file a feedback request** if you want it. (Davide, Ivan)
- **Linear scene-referred preview stream for Bayer RAW capture via video data output / preview
  layer, without tone mapping, to match a linear CIRAWFilter DNG conversion? (with a linked
  Feedback Assistant).** Today the **only way to get scene-referred linear data from camera
  capture is the log format** — **Log** and **Log2** (Log2 added last year, improved gamut). Raw
  frames from camera capture (e.g. ProRAW) carry metadata that is **not compatible** with what
  CIRAWFilter needs to render. The ask isn't available today, but "I like the idea" — worth
  exploring; thanks for the feedback request. (Davide)
- **Generating/writing ISO-conformant gain maps + gain-map metadata for HEIC and JPEG?** Three
  routes: **Core Graphics** and **Core Image** both handle gain-map / ISO gain-map data (a
  colleague's WWDC talk "two years ago" walks the exact APIs and input prep), and third, the
  **gain-map spec is in both the HEIF and JPEG specs** (Apple helped add it), so you can read the
  spec to understand the SDR-vs-HDR-addition split directly. Core Image can control the output
  look, headroom, and how data is merged. (Davide)

### Thumbnails, pan/zoom & Core Image
- **Optimal way to load thumbnails for a lazy grid with a matched-geometry transition to a detail
  view? (sjk_27).** Two framework answers. **Core Graphics:** open the image with the
  **"open image with thumbnails"** option (transcript-approx) — CG uses an embedded thumbnail if
  present, else decodes/scales the main image as fast as possible. **Core Image:** request a
  **scale factor** when opening, as small as your UI needs, which instructs CI to scale down as
  early as possible so everything downstream is faster. (Davide)
- **Close-to-native zoom/scroll/pan of photos up to max resolution without pixelating?** Use
  **Core Image** — built for exactly this, caching across the decode pipeline; for a zoom it
  decodes only the visible rectangle and pans the rectangle around. The **region-of-interest (ROI)
  management** is where CI shines: with heavy operations applied and a deep zoom, it recomputes
  only the visible area even on a much larger image. **CIRAWFilter** has the same caching, so even
  a 100MP RAW pans and zooms smoothly. There's a talk/doc on the developer portal showing how to
  set up the CI filter for this. (Davide, Brad)

### Exposure, AV sync & rotation
- **With photo quality prioritization `.quality`, the device overrides manual exposure/ISO. Can
  you find out the final exposure before capture? (Eric).** It's not just `.quality` — **both
  `.balanced` and `.quality` override manual controls** because they run fusion (capturing under-
  and normally-exposed frames). **Manual controls are only honored with `.speed`.** If quality
  *seems* to respect your settings, "you're getting lucky"; there's no guarantee. You're "handing
  off the decision-making" to the system. For movie/video formats that skip fusion you may still
  get a speed capture that preserves manual settings, but the rule for **photo mode**: quality and
  balanced override; use **speed** if you need exact shutter/ISO. Well covered in this year's
  high-resolution-capture session. (Ivan, Brad)
- **Optimal way to stream video + audio simultaneously without drift?** Don't rely on coincidental
  sync. Modern iPhones sync video and audio off the **same clock**, but only if you let
  **AVCaptureSession** do it: attach a camera device input and a mic device input to one session;
  audio/video data outputs come out with **PTSs already on the same timeline**. If you must use a
  different audio API (**AU Remote IO**), the two sources sit on different **`CMClock`s** — use
  **`CMSyncConvertTime`** (transcript-approx) to convert, and synchronize **video to the audio
  clock** (keep audio time; don't rate-convert audio). Over a network, rely on the timestamps you
  set before sending; handle playback sync on the receiving side with AVPlayer /
  `AVSampleBufferDisplayLayer`. (Note: **PTS = presentation timestamp**, asked on-air.) The reason
  to favor the audio clock: people perceive audio sample-rate changes more readily than micro
  video-timing adjustments — "a tiny glitch in audio and people will hear it." (Brad)
- **When does the camera auto-handle rotation? Orientation differs across iOS/macOS/iPadOS and is
  always a pain. (Michael Rowan).** "Geometry is hard." Apple deliberately **won't** rotate for
  you because it can't know intent (gravity suggests "up," but an always-portrait app would break).
  Tools from the last ~3 years: **`AVCaptureDeviceRotationCoordinator`** gives you the correct
  rotation degrees for preview or for horizon-level upright; or set **`videoRotationAngle`** on the
  video capture connection and the framework rotates for you. How it rotates depends on the output:
  **video data output physically rotates the buffers**; **photo output writes EXIF** to rotate on
  playback (same for movies). The **iPhone 17 front camera is oriented differently** than prior
  iPhones, which has caused confusion — Tracy's session **"Support the Center Stage front camera in
  your iOS app"** has a section on rotation done right so your code stays correct against future
  hardware changes. (Brad)

### Siri camera, Spatial Reframe & multi-cam pro video
- **How many times can I use the (Siri) camera/intelligence feature, and does it change picture
  quality?** No usage limit on-device. Those captures **don't go to the user's photo library** —
  they save to the **Siri app** — and they're **screen-resolution / aspect ratio**, not full photo-
  mode quality. Don't use it to get beautiful photos; it's for **having a conversation with Siri
  about the photo**. There may be some limit on how many conversational things you can ask about a
  given photo. (Matt, panel)
- **Can developers access the Spatial Reframe 3D-reconstruction pipeline via API, or is it locked
  to Photos?** **No developer API** for the reframe capability. ARKit has pieces for 3D scene
  capture/reconstruction/manipulation but not an out-of-the-box reframe. For live depth you have
  the **LiDAR depth camera** AVCaptureDevice on Pro phones (fused LiDAR + RGB) and the front
  **TrueDepth** camera (infrared depth). Good feature to request via Feedback Assistant. (Matt)
- **Can you stream from multiple iPhones? What APIs build a multi-camera streaming app?** Yes — the
  lab itself is shot on iPhones in tripods. Last year ("the **year of the selfie**" internally,
  also their biggest year ever for **pro video**) added features ideal for multi-cam: **locked
  frame duration** (guarantees exactly 29.97 / 24 / 25 / 60, with audio synced — setting equal
  min/max frame rate isn't enough); **GenLock** (an external sync source multiple iPhones lock to;
  plug a third-party **Blackmagic ProDoc** in and feed an external genlock generator, so all
  recordings line up in Final Cut with no tearing); and **time-code generation** (pull time code
  from an external source as an AVCaptureSession output and write a **time-code track** to the
  movie). (Brad, Jake)
- **Any niche new API that isn't talked about much?** **AVProVideoStorage** (released this week). A
  **pre-allocated file** giving **deterministic write speeds** so ProRes capture (e.g. 4K30 writing
  a lot of data) doesn't drop frames — even on an old phone with a fragmented disk. It's being
  **retrofitted to older phones**, has a nice **settings UI** for users to choose how much disk to
  dedicate, and is covered in Jake's session this week. Honorable mention: **RAW 9**, a new
  **ML-based RAW engine** in the 27 OS for debayering and third-party-camera RAW, covered in David
  Hayward's Core Image session **"Enhanced RAW image processing"** (RAW 8 vs RAW 9). (Jake, Davide)

## Unconventional facts & takeaways (the live-Q&A gold)

- **Quality/balanced silently override manual exposure — and "respecting" them is luck.** A real
  gotcha most slides won't state: only `.speed` preserves manual shutter/ISO in photo mode.
- **Quad-Bayer RAW is a decode-ecosystem problem, not a capture one.** ProRAW dodges it by being
  already-debayered RGB; offering raw quad-Bayer DNG would require shipping new decode support
  everywhere. "Debayering Quadra is a completely different beast" vs 25 years of Bayer.
- **Scene-referred linear from capture = Log/Log2 only.** ProRAW frame metadata is **not**
  compatible with CIRAWFilter's render path — a candid limitation, with an open feedback request.
- **Sync video to the audio clock, not vice versa** — because humans catch audio sample-rate
  changes faster than micro video-timing shifts. A perceptual-science rationale you won't find
  in API docs.
- **The two named "rookie mistakes":** main-thread `AVCaptureSession` (blocking reconfig → spinners
  → one-star reviews) and skipping `beginConfiguration`/`commitConfiguration` (the "ATM
  transaction" analogy).
- **AVFoundation is "the second-largest framework in iOS after UIKit,"** which is why the panel
  keeps pushing **sample code over docs-from-scratch**.
- **AVProVideoStorage is being retrofitted to older phones** — an explicitly backward-compatible
  reliability feature, not just new-hardware sugar.
- **Cultural color:** the running "make it pro" vs "maybe *not* pro / Hipstamatic was fun"
  arm-wrestle between panelists; "the year of the selfie" internal nickname; "quitting your day
  job" as the joke answer to biggest mistakes; one engineer drawing smiley faces on paper and
  flipping them to reason about rotation; and the repeated, sincere "we actually do read" Feedback
  Assistant reports.
