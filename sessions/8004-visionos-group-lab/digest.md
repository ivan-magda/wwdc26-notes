---
title: "visionOS Group Lab — Full Digest"
session: WWDC26 · 8004
url: https://developer.apple.com/videos/play/wwdc2026/8004/
duration: 01:03:19
sources: transcript.md (whisper.cpp ggml-large-v3, VAD + -mc 0), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and context.
---

# WWDC26 · 8004 — visionOS Group Lab

> A 63-minute live developer Q&A with a six-person panel from the Vision Products
> Group. Not a feature talk — it's the unscripted layer underneath the WWDC26
> visionOS sessions: clarifications, the "why," current limits, and a near-constant
> ask to **file feedback with concrete use cases**. Most features discussed are
> first-version/beta, and the panel is openly using this hour to collect roadmap input.

## TL;DR

- **Front-facing/main camera access is *not* enterprise-only.** Standard Apple
  Developer Program accounts (typically business-attached) can apply by demonstrating
  a pro/enterprise use case. Common misconception, corrected on-air.
- **Debugging in an immersive space:** use **Mac Virtual Display inside the immersive
  environment** (developer setting, shipped last year). Hit a breakpoint and you stay
  put and safe instead of "squinting at Xcode through passthrough." There is
  deliberately **no caffeination** — Vision Pro has no lock button, so taking it off
  must lock it; keep-awake would break that security model. Set up Optic ID for fast
  re-entry.
- **Object tracking jumped from ~5 fps to 30 fps.** visionOS 2.0 tracked mostly
  stationary objects at ~5 Hz; **visionOS 27 adds a high-frame-rate mode up to 30 fps
  at much lower latency**, plus a smoothing change "a few weeks ago" that makes objects
  feel attached to your hand. Trained models now also run on **iOS 27**; CreateML gains
  an **extended training mode**; tracking updates now carry a **frame-time-aligned
  timestamp** (no ±1-frame guessing).
- **Spatial accessory spec is open.** Beyond last year's PS VR2 Sense controllers and
  Logitech Muse, third parties (**DF Robot, Micro E**) sell append-on accessories and
  DIY boards (3D-print a prop, attach a tracker). Tracking = **Bluetooth + onboard IMU
  + a constellation of IR emitters** the user can't see (works in low light). New
  **IR-feed debugging mode** in visionOS 27 shows what the headset's IR cameras
  actually see.
- **Foveated streaming** (started in **visionOS 26.4** with NVIDIA CloudXR, expanded in
  27) wirelessly streams offline-rendered content to Vision Pro, optimizing by
  *approximate* gaze region without the app ever learning where you look. It's
  **two-way** and **composes with native RealityKit content** in the same depth space.
- **Gaussian splat culling near your face is intentional** (field-of-view safety) and
  applies to *all* 3D models, not just splats. **OpenUSD "particle fields"** (the
  standardized splat, which Apple drove through AOUSD with Adobe and others) and
  **RealityKit splats** "didn't land at the same time" and aren't wired together yet.

## Panel

Host **Adarsh Pavani** (AVP evangelist, WWDR) with **Katie** (developer relations),
**Norman** (rendering/sim/gaming — RealityKit, RCP, USDKit, foveated streaming),
**John** (RealityKit engineer, new USDKit), **Matt** (Vision Products Group —
pro/enterprise, gaming, third-party), and **Travis** (Vision Products Group —
USD/OpenUSD standardization, media/streaming).

## Favorite visionOS 27 features (warm-up, 02:53–08:50)

- **Spatial Panos** — view iPhone panoramas in a new spatial way (Katie).
- **Agentic Xcode** — "fully embraced agentic development… happy to get away from the
  command line and back into Xcode" (Katie). She ends the hour having named *four*
  favorites — a running gag.
- **Physical Space Lighting API** (Norman) — the first year apps can **affect** the
  lighting in your real room, not just read it. ARKit has long understood light
  direction, color temperature, positioning, and environment maps; now a portal's
  flashlight can shine on your actual walls — "I almost had a sense I can feel the
  warmth on my face."
- **USDKit** (John) — far easier to pull USD into a 3D app and to export expressive USD
  scenes across Apple platforms.
- **On-device high-quality (un-foveated) capture** (Matt) — screen recordings are
  normally foveated (optimized where you look). visionOS 27 adds a **Settings toggle
  for up to a 3-minute fully un-foveated capture** — clean App Store/marketing footage
  **without the developer strap** + developer-capture rig.
- **Foveated Streaming framework** (Matt) and **Spatial Preview** (Travis, who notes
  Preview was once *his* product) — Mac and Vision Pro working together; USD's
  underrated strength is **collaboration** (manipulate an object in Vision Pro, sync it
  back to a Mac app).

## Developer Q&A

### Camera & sensors
- **Front-facing camera access for indie devs (Wes Matlock).** Main-camera access
  shipped at launch (left main camera first), and **visionOS 26 added the stereoscopic
  pair**. Access is granted by demonstrating a pro/enterprise-oriented use case, but is
  **open to standard Developer Program accounts** (usually attached to a business) — *not*
  limited to the Apple Developer Enterprise Program. Blocked with a real use case → file
  feedback. (Matt)
- **Visual intelligence: does a third-party app need camera access? (Dilliam).** No —
  it's a **system-level, privacy-preserving** capability; nothing shared with third
  parties. (Katie) Example: "do these shoes fit my bag." New Siri representation in
  visionOS 27.
- **Visual intelligence: camera-only or depth sensors too? (Raymond Yeh).** "Magicians
  never reveal secrets," but it's a **blend of whatever sensors a given device has** —
  same philosophy as SLAM and hand tracking. Implementation differs per device and per
  query context.
- **Face/lips capture like ARKit's face tracking?** ARKit face tracking is **iOS-only**.
  On Vision Pro you can't get raw face data, but a third-party app can request a
  **matted Persona video feed** (your Persona over a background) for video conferencing
  — no lens-style face effects. (John)

### Debugging & dev workflow
- **Debugging inside an immersive space (Thomas Bastable).** Mac Virtual Display in the
  immersive environment; most of the team works in Vision Pro with MVD + immersive
  content side by side ("the best display you can have," ultra-wide), avoiding
  donning/doffing. (Katie)
- **Keeping the device awake on a desk (Thomas Bastable, again).** No sanctioned
  caffeination — by design (see TL;DR). Use MVD for fast build/iterate and Optic ID for
  instant re-entry. (Matt)
- **Agent skills relevant to visionOS? (JudoTree).** The most relevant shipped skill
  **resizes an existing iOS app's UI to visionOS**, adapting form factor and information
  density while keeping visionOS best practices. (Katie)
- **Agentic coding generated wrong visionOS syntax.** LLM output can be wrong — **be
  deliberate when prompting and trust-but-verify**; feed Apple's **sample projects** as
  context. Current models lag the newest APIs (an overlap period). Norman: a local LLM
  built a working RealityKit Tetris out of the box — "your LLM may vary." Samples called
  out: **Hello World, Petite Asteroids, Canyon Crosser**, and the new **Model
  Manipulator** sample (complex 3D hierarchy manipulation — exploded views,
  **cross-sectioning** [a new RealityKit feature], tab-down hierarchy), tied to session
  **284 (Collaborate on structured 3D models in visionOS)**.
- **Automated testing for visionOS/ARKit/RealityKit.** Use the **`RealityRenderer`
  class** to drive RealityKit rendering programmatically in tests. RealityKit is written
  in Swift, so existing Swift Testing / Xcode infra works out of the box. See **267
  (Migrate to Swift Testing)** and **260 (Get the most out of Device Hub)** — "a
  surprising amount of automated testing through Device Hub." (John)

### Spatial accessories & object tracking
- **Tracking accessories outside direct view — e.g., foot trackers (Eric).** Spec opened
  in visionOS 27 (was just PS VR2 Sense + Logitech Muse). Append-on accessories and DIY
  boards from **DF Robot / Micro E**. Tracking combines **Bluetooth + a high-frequency
  onboard IMU + an IR-emitter constellation** seen by the headset's IR cameras (invisible
  to users; robust in low light). If occluded, the **IMU takes over** (like a PS VR2
  controller behind your hand). New **IR-feed debugging mode** verifies visibility at a
  given position. Apple explicitly wants the **foot-tracking use case** explained
  (lower-body breakthrough isn't provided today). See **283**. (Matt)
- **Object tracking speed/latency.** ~5 fps in visionOS 2.0 (stationary) → **up to 30 fps
  high-frame-rate mode** in visionOS 27 at much lower latency; recent smoothing makes
  objects feel hand-attached even with a fully passive (no-electronics) physical model.
  Trained models now run on **iOS 27** too; **CreateML extended training mode** for
  higher accuracy; **frame-time-aligned timestamps** remove ±1-frame ambiguity. Demo:
  spin a globe and the tracked orientation holds through the spin. (Matt, Norman)
- **Controllers in the long-term interaction model.** Eyes (gaze as the cursor — "my
  eyes led me to the cup before the neurons fired"), hands, and voice stay primary;
  controllers/accessories add **precision, haptics, or quick menus** (Logitech Muse).
  PS VR2 + Muse integrate via **Game Controller framework + ARKit** and are wired in at
  the **system level** (launcher, window chrome). Look-to-scroll in Safari noted as a
  delight. Input should match the experience. (Norman, Matt)

### Gaussian splats & USD
- **Splats culled when you move your face into them (Eric).** No way to disable today;
  the **vignette is intentional** for an unobstructed field of view and applies to all
  3D models. File feedback **with distance/context, a sample project, and a screen
  recording**. Beta 1 — room to course-correct. (John, Matt, Travis)
- **Splat rendering on platforms beyond visionOS? (Ethan).** Should work across **iOS and
  visionOS**; if docs/symbols don't match, file feedback (beta). (John)
- **Splats inside a USDZ with other meshes, for a website environment?** Complicated:
  **OpenUSD "particle fields"** (generic splats) exist in the open standard, and
  **RealityKit** has its own splat support, but they **landed at different times and
  aren't hooked together yet** — Norman and Travis were discussing closing the gap "moments
  before this show." Apple drove particle fields through **AOUSD** (with Adobe and
  others). USD *can* hold both in one scene. (Travis, Norman)
- **USD export workflow (Sismagia).** Export **straight from your 3D DCC**, not via an
  intermediate FBX/GLB that stamps "format fingerprints." Use **USDA vs USDC** loose
  files to iterate single files without repackaging the scene. The **USD core spec**
  recently shipped, so you can now lean on an **LLM** to understand/transform USD. (Travis,
  Norman)

### Device sharing, streaming & accessibility
- **Why no Mac-style user profiles? (Oliver Colon).** Vision Pro is intentionally a
  **personal device** (closer to iPhone/iPad than Mac). Sharing friction is being chipped
  away: **save enrollment to your iPhone (iOS 26+)** and re-enter any guest-mode device by
  glancing at an **app-clip code** on your phone; **guest user with nearby device** lets
  someone in without your passcode and mirrors their view via AirPlay. (Matt)
- **Mac Virtual Display tied to iCloud blocks shared/enterprise Macs (Christopher).** True
  today — MVD requires both devices on the same iCloud. But **Spatial Preview is *not*
  iCloud-bound** (USD-compliant preview to other devices), and last year's **remote
  immersive space** hosts rendering on the Mac and streams stereoscopically to Vision Pro,
  also without shared iCloud. **Foveated streaming** brings PC-pipeline Gaussian-splat
  content in, and is **two-way** (send data back; host SharePlay/SwiftUI input natively).
  Example: a **pilot sim** rendering the cockpit natively as a RealityKit entity while
  streaming the out-the-window view from a cloud server, all depth-composited with the
  real world. (Matt, Norman)
- **More frameworks (PersonaKit, Core IK) for accessibility telepresence?** "We don't
  speak to future plans" — but accessibility is central; **file feedback**. Noted: the
  **magnifying glass**, and the **wheelchair feature powered by Vision Pro** announced
  around **Global Accessibility Awareness Day**. (panel)

### WebXR
- **WebXR immersive AR mode in Safari? (limited to immersive VR).** WebXR is supported but
  **immersive-only** (per the WebXR standard). Consider native platform APIs instead —
  this year's **`<model>` tag ships on all platforms**, and a web page can act as a
  personal environment: position a USD asset in-page and **pull it out into your room**.
  Apple is exploring the web/AR path differently than WebXR. (Norman, Travis)

## Unconventional facts & takeaways (the live-Q&A gold)

- **The Enterprise-Program myth, busted:** camera access ≠ enterprise membership.
- **Numbers you won't find in a slide:** object tracking ~5 fps → 30 fps; un-foveated
  capture capped at **3 minutes**; foveated streaming origin = **visionOS 26.4**.
- **Candid internal-misalignment admission:** OpenUSD particle fields and RealityKit
  splats "trains don't run on the same clock" — not yet integrated.
- **Intentional-not-a-bug:** near-face splat/model vignette is a deliberate FOV-safety
  choice.
- **Frame-time-aligned object-tracking timestamps** — a small but real correctness win
  for AR compositing.
- **Apple dogfoods Vision Pro** to design its own products and retail stores; the Model
  Manipulator sample is productized from those internal workflows.
- **Feedback is the actual API roadmap.** The panel asks for **sample project + screen
  recording + distance/context** on nearly every answer; it's the lab's refrain.
- **Cultural color:** Katie's escalating "favorite features" (ends at four); Travis's
  recurring train/transportation analogies for standards-vs-OS release cadence; the
  "trust but verify" mantra for LLM-generated visionOS code.
