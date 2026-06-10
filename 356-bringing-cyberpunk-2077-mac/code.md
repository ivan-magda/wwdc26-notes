# Code samples — Session 356

_No Code tab for this session._ This is a developer-story session with no published
snippets; the talk names concrete APIs verbally rather than showing fenced code.

## Spoken APIs

Concrete Apple APIs / technologies named in the transcript, grouped by area.

### Tooling & rendering

- **Game Porting Toolkit (GPTK)** — evaluate a Windows build in a translated environment
  on macOS before writing native code. ([04:50])
- **Metal HUD** — frame-time / trace capture; primary cross-device measurement tool on
  native builds. ([06:00], [06:23])
- **Metal API** — native rendering backend. ([08:17])
- **Metal Shader Converter** — convert existing shaders to Metal for broad coverage
  during bring-up. ([08:22], [09:48])
- **MetalFX Upscaling** — render at lower internal resolution, reconstruct higher output;
  temporal upscaler. ([11:39])
- **Dynamic Resolution Scaling (DRS)** — vary internal resolution under load to hold a
  stable frame rate. ([12:04])

### Windowing / app lifecycle (NSNotification)

- `NSWindowDidChangeOcclusionStateNotification` + `NSWindow.occlusionState` — pause/resume
  rendering when the window is not visible. ([19:20])
- `NSApplicationDidChangeScreenParametersNotification` — display settings changed; refetch
  resolution and resize the game window. ([19:53])
- `NSWindowDidChangeScreenNotification` — window moved to a new display; read Display ID,
  resolution, mirror mode, screen name. ([20:12])
- `NSWindowDidResignKeyNotification` — window lost key focus → show system cursor, hide
  game cursor. ([21:03])
- `NSWindowDidBecomeKeyNotification` — window became key → hide system cursor, show game
  cursor. ([21:12])

### System features

- **Game Mode** — higher CPU/GPU priority for games, lowered background-task impact,
  doubled Bluetooth sampling rate (lower controller + AirPods latency); auto-enabled for
  apps categorized as games. ([21:21])
- **Game Controller framework** — native third-party controller support, adaptive
  triggers, touchpad. ([22:07])
- **Magic Mouse / trackpad** — adapt control scheme to current input (e.g. modifier-key +
  click as a middle-mouse alternative, toggle aiming). ([22:27])

### Display (Extended Dynamic Range)

- `maximumExtendedDynamicRangeColorComponentValue` — poll current max EDR value; feed the
  tone mapper for correct HDR output. ([23:36])
- `maximumPotentialExtendedDynamicRangeColorComponentValue` — auto-enable HDR by default
  when the display's potential EDR value is greater than 2.0. ([24:09])

### Audio (spatial)

- **AVAudioEngine** — audio middleware implements Apple spatial audio APIs through it.
  ([25:03])
- `AVAudioEnvironmentNode.listenerHeadTrackingEnabled = true` — head-tracked spatial audio
  for AirPods. ([25:15])

### Cloud saves

- **iCloud Drive** integration — transfer save files between Apple devices; paired with an
  in-house cross-progression solution. ([25:25])
