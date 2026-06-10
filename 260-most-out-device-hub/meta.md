# Session 260 — Get the most out of Device Hub

- **URL:** https://developer.apple.com/videos/play/wwdc2026/260/
- **Duration:** 17m
- **Speakers:** Matt (Devices team engineer), Hassan (Devices team)

## Description

Meet Device Hub, a new app shipping alongside Xcode 27 that is your home for working
with both physical devices and simulators. It offers one consistent set of tools across
devices and simulators: a live interactive canvas to control them, a sidebar to organize
a large inventory, and a five-panel inspector to configure appearance, conditions,
diagnostics, app data, and profiles. The session closes with an end-to-end debugging
workflow (find a bug on a device, reproduce it on a simulator) and a pointer to
`devicectl` for scripting and CI.

## Key topics

- **Device Hub** ships with Xcode 27 but runs standalone — no need to launch Xcode; auto-launches on build-and-run to a simulator
- **Two modes** — compact (focused live screen + essential controls) and full window (control, organize, configure)
- **Consistent experience** across real devices and simulators
- **Control / canvas** — live interactive display, click/drag/scroll/trackpad gestures, contextual hardware controls, zoom, 1:1 physical size, Resize mode, Capture keyboard
- **Organize / sidebar** — full inventory of devices + simulators; filter, sort, group; context-click quick actions (restart, pair iPhone+Watch); multiple compact windows or tabs to compare side by side
- **Configure / inspector** — five panels: device settings (appearance, conditions like location, audio), diagnostic reports (crashes, spins), Info, Apps (install/uninstall + data containers), Profiles (configuration + provisioning)
- **App data containers** — inspect in Finder, restore to baseline, snapshot, download, replace
- **Diagnostics** — screenshots, sysdiagnose for system-level capture
- **`devicectl`** — CLI on the same underlying technology; list/install/configure devices, capture diagnostics, `--json-output` for scripts and CI

## Related sessions to fetch (referenced in this talk)

- [ ] Modernize your UIKit app (Resize mode / resizability) — WWDC26
- [ ] Getting the Most Out of Simulator — WWDC 2019

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of Device Hub, a new app shipping with Xcode 27 for working with devices and simulators.
- **1:04 Device Hub overview** — A tour of the two modes (compact and full window) and the consistent experience across real devices and simulators.
- **3:00 Control** — Use the canvas to interact with devices directly: live display, touch input, hardware controls, zoom, resize mode, keyboard capture.
- **4:39 Organize** — Manage the full device and simulator inventory from the sidebar with filters, sorting, grouping, and compact windows.
- **6:04 Configure** — Use the five inspector panels to configure appearance, simulate conditions like location, view diagnostics, manage device info, install/manage apps, and handle configuration and provisioning profiles.
- **8:08 Reproducing a bug** — Device Hub in action: one developer pairs devices, installs a logging profile, captures diagnostics, and shares app data — then another reproduces the bug on a simulator by mirroring the device's configuration.
- **15:52 devicectl** — The command-line tool for managing devices, installing apps, capturing diagnostics, and integrating into scripts and CI.
- **16:30 Next steps** — Resources: devicectl docs, related sessions on simulator and UIKit resizability, and Device Hub documentation.

## Code

See `code.md` — no Code tab for this session; only the `devicectl` commands named in the transcript.
