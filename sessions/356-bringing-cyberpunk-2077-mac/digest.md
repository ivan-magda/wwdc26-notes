---
title: "Bringing Cyberpunk 2077 to Mac — Full Digest"
session: WWDC26 · 356
url: https://developer.apple.com/videos/play/wwdc2026/356/
duration: 28m
speakers: Garrett Austin (Apple), Paweł Sasko (CD PROJEKT RED)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 356 — Bringing Cyberpunk 2077 to Mac

## TL;DR

CD PROJEKT RED's Paweł Sasko narrates the full arc of porting Cyberpunk 2077: Ultimate
Edition to Apple silicon Macs. Four headline themes:

1. **A quality bar, set first.** Before any code, the team defined "doing it properly"
   as three things — preserve the game's **visual fidelity/identity**, hit **stable
   performance** in the heaviest scenes, and deliver **native-feeling** macOS polish.
   Everything downstream is measured against that bar.
2. **Game Porting Toolkit as a planning instrument, not a shipping target.** They ran
   the Windows build in translation to gather CPU/GPU pressure signals and turn them
   into a production roadmap — explicitly *not* to read final performance numbers.
3. **A disciplined native bring-up.** Native macOS builds → parallel data pipeline →
   architecture bridge (validated by unit tests) → Metal rendering with **Metal Shader
   Converter** → **MetalFX Upscaling + Dynamic Resolution Scaling** for cross-device
   scaling. The Metal foundation was built unit-tests-first, then stationary scenes,
   then dynamic scenes.
4. **"For this Mac" + native polish.** A device-detecting graphics preset auto-tunes
   FPS/MetalFX/resolution/VSync/HDR per machine, plus a deep pass of macOS-native
   behaviors: NSNotification lifecycle handling, Game Mode, Game Controller framework,
   EDR-driven automatic HDR, head-tracked spatial audio for AirPods, and iCloud save
   sync.

The live demo loads the Black Market in Dogtown — one of the heaviest areas in the
game — at a locked 60 FPS on a MacBook Pro with M5 Max, out of the box.

---

## 1. What Cyberpunk 2077 is, and why it's a hard port (00:44)

An open-world RPG set in Night City: massive world, **no loading screens**, constant
data streaming. It's still used as an industry benchmark for new hardware because:

- **CPU pressure** — crowds, traffic, AI, physics, animation, quests, and systemic
  interactions all run in parallel.
- **Mixed lighting everywhere** — neon, signs, puddles, reflective surfaces, headlights,
  emissive lights, volumetrics, often in one scene.
- **Ray tracing and path tracing** modes that scale strongly with GPU capability.

A key enabler: the game was *designed to be scalable* across a range of hardware from
the start, which carried over to the Mac bring-up.

## 2. Why Mac, why now (02:57)

CD PROJEKT RED has a long history on Apple platforms. The trigger was Apple silicon
maturing enough to run a game of Cyberpunk's scale at a *serious quality level*, not
just "running it" — something they'd be comfortable putting their name on — plus the
reach of more players.

## 3. The quality bar (03:45)

Three components, used as the rubric for the whole port:

1. **Visual fidelity & identity** — the look, lighting, and materials are core to what
   Cyberpunk is.
2. **Stable performance** — evaluated specifically in heavy CPU+GPU scenarios: large
   crowds, screen-space reflections, driving through dense areas, combat.
3. **Native feel** — finishing touches that use platform capabilities to the maximum.

## 4. Game Porting Toolkit evaluation (04:50)

Before building the native path, they ran the **Windows build in a translated
environment** on macOS via Apple's **Game Porting Toolkit** to gather insight *before
writing any code*:

- Is the port feasible at the quality bar they set?
- Where is the pressure — CPU, GPU, or specific systems?
- What should they build first?

The goal was **information, not final performance numbers** — where frame time goes,
what the real-gameplay challenges are, and the native build order.

### The three-angle measurement approach (05:40)

For each predetermined hotspot sequence they read data from three angles:

- **Statistical frame-time data** from the in-engine profiler (consistent run-to-run
  comparison).
- **Metal HUD** to correlate scene events with the trace — loading, shader translation,
  known events like saving.
- **Engine internal profiling broken down by thread** to see which CPU systems were
  active and when.

Once a native build existed, **Metal HUD became the primary frame-time capture tool**,
because it could collect comparable measurements across many devices on builds *without*
debug or profiling settings — the most reliable data.

### Signals (06:47)

- On **high-spec hardware, GPU time looked healthy this early** — a pleasant surprise
  and a realistic baseline for the performance targets once Metal was native.
- In heavy gameplay, **CPU pressure dominated**: city driving had a best case where the
  GPU was the limiter, but a hotspot case where CPU time rose sharply as the scene
  filled with traffic, crowds, and action.
- Two patterns were **translation-environment artifacts**, not real problems:
  frame-time oscillation from **live shader translation**, and **audio middleware**
  looking heavy. Both vanished on native binaries. Because GPTK flagged them early, the
  team knew to investigate them during native implementation.

## 5. From signals to a production roadmap (08:00)

The order of work:

1. **Make Mac a real target** — native builds/libraries, adjusted data pipelines.
2. **Get a playable build** — bring up rendering and shader paths with the Metal API
   and Metal Shader Converter.
3. **Make it shippable** — platform-native features, then optimization and polish.

### Making Mac a real target (08:37)

- **Native macOS builds** — Apple silicon builds with the macOS toolchain, for both the
  game executable *and* the internal development tools.
- **Data pipeline** — macOS added as another parallel platform in the existing build
  pipeline; platform-specific outputs (archives, shader cache) generated the same way.
- **Architecture bridge** — the engine carried years of assumptions from other CPU
  architectures; the team validated what works via **unit testing** and identified
  required changes for Apple silicon.

### Shader pipeline with Metal Shader Converter (09:39)

Treated as a loop:

1. Integrate Metal Shader Converter into the shader build so Metal output is generated
   in normal builds.
2. Validate repeatable scenes, watching for differences in lighting response, materials,
   and post effects.
3. Refine the smaller set of advanced shaders / edge cases where converted output didn't
   match.
4. Repeat as part of the build-and-test pipeline.

### Native Metal rendering foundation (10:35)

Built up in increasing complexity:

- **Unit tests** — bring the Metal backend up piece by piece, checking base output.
- **Stationary in-game scenes** — validate what unit tests miss: lighting stacks, post
  effects, scene-level behavior.
- **Dynamic scenes** — camera movement, streaming, and gameplay expose real edge cases.
- **Ray tracing & path tracing** — optimized while validating that visual output matches
  other platforms to preserve identity.

### Scaling across Macs with MetalFX (11:32)

- **MetalFX Upscaling** renders at a lower internal resolution and reconstructs a higher
  output in less time — more headroom in heavy scenes without lowering quality across
  the board.
- **Dynamic Resolution Scaling** keeps performance stable under load across the Mac
  lineup; as a temporal upscaler it holds the image together in motion (fast traversal,
  VFX-heavy scenes).

At this point: full game, working build, predictable rendering, playable.

## 6. "For this Mac" preset (13:16)

A **device-based graphics preset** that detects each Mac's hardware and auto-configures
optimal settings, so any supported Mac gives a stable, enjoyable starting point on first
launch.

What it sets per device:

- **Target FPS** — 30 or 60.
- **MetalFX Upscaling with Dynamic Resolution Scaling**, with min/max internal
  resolution boundaries tuned to hit the target FPS.
- **Video settings (unique to this preset):** final output resolution (the bound DRS
  operates within), **VSync** for frame pacing, and **HDR** based on display
  capability.

The team then hand-tuned every setting per Mac, validating across a set of consistent
scenes that stress CPU, GPU, and streaming differently, in a collect → refine →
revalidate loop. Sasko notes other developers have since started adopting "For this
Mac" presets in their own games.

### Live demo — Dogtown Black Market (15:21)

- MacBook Pro with **M5 Max**, **Ultra** preset as a base. "We didn't require a lot of
  tweaks for M5 Max — it's quite a capable chip."
- MetalFX + DRS targeting **60 FPS**, rendering **50–80% of target output resolution**.
- Video: **60 FPS lock with VSync**, internal-display resolution **2336×1460**, **HDR on**
  by default (HDR-capable display).
- HDR **auto-calibrated via Apple's EDR APIs** — no calibration screen for the player.
- Loads into the **Black Market** in Dogtown — dense geometry, lights, reflections,
  characters; one of the heaviest areas — and traverses smoothly (no loading screens) at
  **60 FPS with out-of-the-box settings**.
- Easter egg: the founders **Marcin Iwiński and Michał Kiciński** are scanned into this
  scene and do their own Polish voice acting across all localizations.

## 7. Native macOS polish (13:05 / 18:12)

Adopted across windowing/app-switching, input, display, audio, and cloud saves.

### Windowing & app lifecycle via NSNotification (18:29)

macOS broadcasts events as `NSNotification`s the game listens for:

- **Background activity reduction** — listen for
  `NSWindowDidChangeOcclusionStateNotification`, then check `NSWindow.occlusionState` to
  pause or resume rendering. When not visible, skip rendering to save CPU/GPU.
- **Display settings change** — on
  `NSApplicationDidChangeScreenParametersNotification`, fetch the new screen resolution
  and resize the game window.
- **Window moved to a new display** — on `NSWindowDidChangeScreenNotification`, collect
  Display ID, resolution, mirror mode, screen name, and update the window.
- **Cursor management on focus changes** — `NSWindowDidResignKeyNotification` → show the
  system cursor / hide the custom game cursor (e.g. when the Game Overlay opens);
  `NSWindowDidBecomeKeyNotification` → reverse it. The game uses its own cursor to match
  its style.

### Game Mode (21:21)

A system feature that gives games higher-priority CPU/GPU access and lowers
background-task impact. It also **doubles the Bluetooth sampling rate**, cutting latency
for wireless controllers and AirPods audio. **Auto-enabled** for apps categorized as
games; CD PROJEKT RED's validation confirmed the benefit.

### Input (22:04)

- **Game Controller framework** — native support for a range of third-party controllers,
  including advanced features like **touchpad** and **adaptive triggers**, bridging the
  game's existing implementations.
- **Magic Mouse / trackpad** — every Mac laptop has a trackpad, so control options adapt
  to the current input: they auto-detect and enable toggle aiming and a modifier-key +
  click alternative to the middle mouse button.

### Display / HDR via Extended Dynamic Range (22:53)

HDR implemented through Apple's **EDR pipeline**, which exposes display info for
**automatic calibration** — no manual HDR calibration screen on Apple displays.

- Poll `maximumExtendedDynamicRangeColorComponentValue` for the current max EDR value and
  feed it to the tone mapper; it changes dynamically with hardware/conditions, so driving
  the tone mapper from it always gives the best HDR.
- Auto-enable HDR by default when
  `maximumPotentialExtendedDynamicRangeColorComponentValue` > **2.0** (e.g. Apple's XDR
  displays).

### Audio — head-tracked spatial audio (24:35)

Cyberpunk's soundscape is designed for spatial audio. The audio middleware implements
Apple's spatial audio APIs via **`AVAudioEngine`**; head tracking for AirPods is enabled
by setting `AVAudioEnvironmentNode.listenerHeadTrackingEnabled = true`. On by default,
no setup.

### Cloud saves (25:25)

**iCloud Drive** integration transfers save files between Apple devices; combined with an
in-house **cross-progression** solution, players continue a save across platforms — start
anywhere, continue on Mac, and back.

## 8. Outcome (25:53)

A native Cyberpunk experience across Apple silicon Macs with platform features
integrated to the quality bar. Stated results: **35M copies sold across all platforms**
plus **10M of Phantom Liberty**, favorable App Store reviews, and **Mac Game of the Year**
in Apple's **2025 App Store Awards**.

## 9. Next steps (26:59)

- Try **Game Porting Toolkit's evaluation environment** (no code, fast setup).
- Use **Metal HUD** to evaluate performance within GPTK.
- Plan a great **first-launch experience**: optimized settings + native features like EDR
  and head-tracked spatial audio with AirPods.
- Related: **"Speedrun your game port with agentic coding"** (agentic skills in Game
  Porting Toolkit 4) and **"Find and fix performance issues in your Metal games."**
