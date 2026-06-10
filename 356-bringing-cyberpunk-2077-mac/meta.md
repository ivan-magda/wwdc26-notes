# Session 356 — Bringing Cyberpunk 2077 to Mac

- **URL:** https://developer.apple.com/videos/play/wwdc2026/356/
- **Duration:** 28m
- **Speakers:** Garrett Austin (Apple Game Performance), Paweł Sasko (Associate Game Director, CD PROJEKT RED)

## Description

How CD PROJEKT RED brought Cyberpunk 2077: Ultimate Edition — an open-world RPG set
in Night City — to Apple silicon Macs with strong performance across the device
lineup. Paweł Sasko walks through the team's three-part quality bar (visual fidelity,
stable performance, native feel), the Game Porting Toolkit evaluation that informed
the production roadmap, the native Metal rendering bring-up with Metal Shader
Converter and MetalFX Upscaling, the device-aware "For this Mac" graphics preset, and
the macOS-native polish (windowing/app-switch notifications, Game Mode, Game
Controller framework, EDR-based HDR, head-tracked spatial audio, iCloud save sync).

## Key topics

- Why now: Apple silicon matured enough to hit a serious quality bar for a game of
  Cyberpunk's scale; CD PROJEKT RED's long history on Apple platforms.
- Three-part quality bar: visual fidelity/identity, stable performance under heavy
  CPU+GPU load, native-feeling finishing touches.
- Game Porting Toolkit (GPTK) evaluation of the Windows build in translation before
  writing native code — feasibility, CPU/GPU pressure signals, focus areas.
- Profiling stack: in-engine statistical frame-time profiler, Metal HUD, per-thread
  engine profiling. Metal HUD became the primary cross-device capture tool on native.
- Translation-only artifacts (frame-time oscillation from live shader translation;
  heavy audio middleware) flagged by GPTK and resolved by native binaries.
- Production roadmap: native macOS builds/toolchain → parallel data pipeline (archives,
  shader cache) → architecture bridge (validate engine assumptions via unit tests) →
  Metal rendering + Metal Shader Converter → MetalFX Upscaling + Dynamic Resolution
  Scaling → platform features → optimization/polish.
- Metal Shader Converter loop: integrate into shader build, validate repeatable scenes,
  refine advanced/edge-case shaders, repeat.
- Native Metal foundation bring-up order: unit tests → stationary scenes → dynamic
  scenes; ray/path tracing validated to match other platforms.
- "For this Mac" preset: device-detection auto-configures target FPS (30/60), MetalFX +
  DRS, min/max internal resolution, output resolution, VSync, HDR — per-device tuned.
- macOS-native features: NSNotification handling (occlusion/background pause, screen
  parameter + screen change, key/resign-key cursor swap), Game Mode, Game Controller
  framework (adaptive triggers/touchpad), Magic Mouse/trackpad input adaptation,
  EDR-driven automatic HDR calibration, AVAudioEngine head-tracked spatial audio for
  AirPods, iCloud Drive save transfer + in-house cross-progression.
- Outcome: 35M copies across platforms + 10M Phantom Liberty; Apple 2025 App Store
  Awards Mac Game of the Year.

## Related sessions to fetch

- [ ] Speedrun your game port with agentic coding (agentic skills in Game Porting Toolkit 4)
- [ ] Find and fix performance issues in your Metal games

## Chapter summary (Summary tab)

- **0:00 Introduction** — Garrett Austin (Apple Game Performance) introduces Paweł Sasko
  (CD PROJEKT RED); Cyberpunk 2077: Ultimate Edition on Mac with great performance
  across many devices.
- **0:44 What is Cyberpunk 2077** — open-world RPG in Night City; industry benchmark for
  dense scene complexity/streaming, realistic lighting/materials, demanding ray/path
  tracing.
- **2:57 Why bring it to Mac** — CD PROJEKT RED's Apple history; Apple silicon maturity to
  hit a serious quality bar; reaching more players.
- **3:45 How we brought it to Mac** — three-part quality bar, then execution: native
  builds, adapted data pipeline, architecture bridge, Metal Shader Converter, native
  Metal rendering, MetalFX Upscaling.
- **4:50 Game Porting Toolkit evaluation** — evaluate the Windows build in translation
  before writing code; CPU/GPU pressure signals; profiling, Metal HUD, per-thread
  breakdowns → production roadmap.
- **13:05 What we did to stand out** — make the working build feel native: windowing/app
  switching, input, display, audio, cloud saves.
- **13:16 "For this Mac" preset** — device-based graphics preset auto-configuring target
  FPS, MetalFX + DRS, resolution bounds, VSync, HDR; demoed live in Dogtown.
- **26:59 Next steps** — try GPTK evaluation, analyze with Metal HUD, plan a great
  first-launch experience; related sessions on agentic coding and Metal performance.

See `code.md` for the concrete APIs named in the talk (no Code tab was published for
this session).
