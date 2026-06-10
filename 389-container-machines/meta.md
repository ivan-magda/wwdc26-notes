# Session 389 — Discover container machines

- **URL:** https://developer.apple.com/videos/play/wwdc2026/389/
- **Duration:** 11m
- **Speakers:** Michael
- **Audio:** English.

## Description

Introduces Container machine, a new feature built on the open-source Containerization
Swift framework that provides a fast, lightweight, and persistent Linux environment
seamlessly integrated with macOS. Container machine combines the usability and speed of
a container with the persistence of a virtual machine, adds OCI image support and
first-class integration into the `container` CLI, and mirrors your macOS user and
filesystem so you can move between macOS and Linux without a context switch.

## Key topics

- **Containerization** — the WWDC25 open-source Swift framework for running Linux containers on macOS (storage, networking, execution, Linux init), VM-based isolation, sub-second start times, and the companion `container` CLI.
- **Design principles** — fast & lightweight; simple to create and operate; persistent across sessions; a seamless extension of macOS.
- **Container machine** — builds on Containerization: each machine runs in its own lightweight VM, uses the same OCI/container image format, is a first-class `container` subcommand, is stateful/persistent, and auto-mirrors your username, working directory, and filesystem.
- **Cross-platform workflow** — automatic user mapping, shared filesystem, and "enter the Linux environment from anywhere in a terminal."
- **Demo** — create a default `alpine` machine; run `echo`/`uname` (Darwin vs Linux); interactive shell with mirrored `whoami`/`pwd`; build & run a Vapor web server in Linux, edit it in Xcode on the Mac, change an app icon with Icon Composer, and view the live site in Safari — all without copying files into the machine.

## Related sessions to fetch (referenced in this talk)

- [ ] Meet Containerization (WWDC25)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Michael introduces Container machine: a fast, lightweight, persistent Linux environment built on Containerization and integrated with macOS.
- **1:19 Containerization** — review of the WWDC25 open-source Swift framework: VM-based isolation, sub-second start times, and the companion `container` CLI tool.
- **2:14 Design principles** — the four principles shaping Container machine: fast & lightweight, simple to create/operate, persistent across sessions, and a seamless extension of macOS.
- **3:36 Container machine** — how it builds on Containerization: OCI image support, first-class integration into the `container` tool, stateful persistence, and automatic user + filesystem mirroring.
- **4:36 Demo** — creating and running a Container machine, executing commands with automatic user/directory mirroring, and building/testing a Vapor web server from macOS using Xcode, Icon Composer, and Safari.
- **10:33 Next steps** — recap of strengths; download the latest `container` 1.0 release from GitHub to try it.

## Code

See `code.md` — the Code tab is a sequence of `container machine ...` CLI invocations from the demo (plus `swift run`).
