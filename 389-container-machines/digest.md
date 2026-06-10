---
title: "Discover container machines — Full Digest"
session: WWDC26 · 389
url: https://developer.apple.com/videos/play/wwdc2026/389/
duration: 11m
speakers: Michael
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 389 — Discover container machines

## TL;DR

**Container machine** is a new feature of Apple's open-source `container` tool (built on
the Containerization Swift framework) that gives you a **fast, lightweight, persistent
Linux environment that feels native to macOS**. Headline themes:

1. **Container speed + VM persistence.** Each machine runs in its own lightweight VM with
   sub-second-class start behavior, but unlike a throwaway container it is **stateful** —
   modifications persist, so you can stop and resume a project where you left off.
2. **Same images, familiar UX.** It's a first-class `container machine` subcommand and
   uses the **same OCI image format** as containers (e.g. start from `alpine`), so it
   slots into an existing `container` workflow with no new tooling to learn.
3. **Seamless macOS ↔ Linux.** **Automatic user mapping**, **shared filesystem**, and a
   **mirrored working directory** mean `whoami`/`pwd` match your Mac, and files edited on
   the Mac (Xcode, Icon Composer) are instantly visible inside the machine — no copying.
4. **Real cross-platform loop.** The demo edits a Vapor server in Xcode on macOS, builds
   and runs it in Linux, and views the live site in Safari over the machine's IP.

It builds directly on **Containerization** and the `container` CLI that Apple
open-sourced at WWDC25. Try it via the latest `container` 1.0 release on GitHub.

---

## 1. Recap: Containerization (1:19)

Containerization is the **Swift framework for running Linux containers on macOS** that
Apple open-sourced at WWDC25, focused on security, privacy, and performance. It provides:

- APIs for **storage, networking, execution, and a Linux init system**.
- **VM-based isolation** — each container gets its own lightweight virtual machine.
- **Sub-second start times** for those VMs.
- The companion **`container` CLI**, also open source, for image creation, distribution,
  and lifecycle management of Linux containers.

Background deep-dive: **"Meet Containerization"** (WWDC25).

## 2. Design principles (2:14)

Four principles shaped Container machine:

- **Fast and lightweight** — quick to create so each project can have its own dedicated
  environment without conflicting dependencies or toolchains.
- **Simple to create and operate** — switching between macOS and Linux should be easy;
  customize and create new environments quickly.
- **Persistent** — a long-lived environment where additional tools and dependencies can be
  added and reused over time across the development lifecycle.
- **A seamless extension of macOS** — developing for multiple platforms shouldn't require a
  large context switch or learning new tools for a different target.

## 3. What Container machine adds (3:36)

Building on Containerization:

- Each Container machine runs **inside its own lightweight VM** and uses the **same image
  format as containers** — images built with the `container` tool can be the starting
  point for a new machine.
- It's a **first-class feature of the `container` tool** with a familiar UX.
- It is **stateful** and **persists modifications** made while you work; start/stop as
  needed and continue where you left off.
- **Host integrations:** automatic user mapping, shared filesystem support, and the ability
  to enter the Linux environment from anywhere in a terminal — a smooth round trip from
  macOS into Linux and back.

## 4. Demo: the basics (4:36)

Discover the subcommand and create a default machine:

```console
container machine
container machine create --name demo --set-default alpine
```

- `container machine` lists the available actions (`create`, `run`, `stop`, …).
- `create --name demo --set-default alpine` makes a machine named `demo` from the `alpine`
  **OCI image** and marks it default, so later commands can omit the name.

Run commands inside the machine:

```console
container machine run echo hi
container machine run uname     # prints "Linux" (vs "Darwin" on macOS)
```

`uname` is the proof point: the same command prints `Darwin` on the Mac and `Linux` inside
the machine, confirming the Linux runtime.

User and directory mirroring:

```console
container machine run           # no args → interactive shell
```

- On the Mac, `whoami` → `Michael`, `pwd` → the user's home directory.
- Inside the interactive machine shell, `whoami` and `pwd` return the **same** username and
  path. Automatic user creation + filesystem sharing + a consistent working directory make
  it feel seamless.

## 5. Demo: a real Vapor cross-platform workflow (7:03)

The workflow: edit a **Vapor** web server in **Xcode on the Mac**, build and run it in
**Linux**, edit app images with **macOS tools**, and test from **Safari** on the Mac.

Project layout (from `ls`): `Package.swift`, source code, and a `Public/` assets
directory. The author already has a Container machine with the **Swift toolchain**
installed.

```console
container machine list   # name, IP address, resource info for all machines
```

The machine's **IP address** is copied for later. Then:

```console
container machine run    # interactive shell; project files already shared
swift run                # compile + run the Vapor server inside Linux
```

Networking detail worth noting: the machine has an **isolated network**, so for Safari on
the Mac to reach the server you must make **Vapor listen on the external interface** —
done by setting the server configuration's **hostname to the machine's IP** (edited in
Xcode on the Mac; the change is instantly visible inside the machine via the shared
filesystem). The site is then reached at:

```
http://<machine-ip>:8080
```

Live-edit loop with Icon Composer: the app's storage icon is opened in **Icon Composer**,
its background changed to a gradient, exported over the existing file in the project — and
**refreshing Safari shows the updated icon without copying anything into the machine**,
because the project directory is shared.

## 6. Next steps (10:33)

Container machine builds on the usability and speed of containers with the persistence of a
VM, and its host integrations make Linux feel like an extension of the Mac. Download the
latest release of the `container` tool on **GitHub** to try it. (The Summary tab refers to
this as the **`container` 1.0** release.)
