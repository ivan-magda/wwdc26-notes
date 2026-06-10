# Session 224 — Expand the capabilities of your Virtualization app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/224/
- **Duration:** 21m
- **Speakers:** Ronnie Misra (Virtualization team)

## Description

A tour of new and existing Virtualization framework APIs for building more powerful
virtualization apps: automating macOS guest setup with provisioning options, attaching
host USB accessories into VMs with the new Accessory Access framework, configuring
advanced network topologies via the vmnet framework, creating high-performance,
space-efficient layered disk images with the new DiskImageKit framework, and
implementing custom paravirtualized devices with the custom Virtio device API.

## Key topics

- **macOS guest provisioning** — `VZMacGuestProvisioningOptions` sets full name / username / password and optionally auto-login + remote login (SSH); passed to Setup Assistant on first boot only.
- **Accessory Access** — new framework to pass host USB devices into macOS and Linux VMs with explicit user control + hot plugging; `AAUSBAccessoryManager`, matching criteria, `VZUSBPassthroughDevice`; needs the "Claim USB Accessory" capability.
- **Advanced networking** — `vmnet` framework (macOS 26+) for custom topologies, DHCP config, TCP/UDP port forwarding; share one network across VMs/processes via serialization over XPC.
- **DiskImageKit** (macOS 27) — layered, sparse disk images using ASIF; base / cache / overlay layers; copy-on-write snapshots; shared read-only base layers across VMs.
- **Custom Virtio** (macOS 27) — `VZCustomVirtioDevice` to implement custom paravirtualized devices (crypto, ML accelerators, custom protocols) for Linux guests; requires a custom guest driver.
- **Other advancements (briefly)** — iCloud support in VMs, EFI Secure Boot for Linux, Metal argument buffers + indirect command buffers for macOS guests.

## Related sessions to fetch (referenced in this talk)

- [ ] No specific companion sessions are named in this talk. References point to documentation: "Accessory Access documentation" (supported device types) and the macOS virtual machine sample app.

## Chapter summary (Summary tab)

- **0:01 Introduction** — Advanced capabilities ahead: automating Virtual Mac setup, USB pass-through with Accessory Access, advanced networking, DiskImageKit disk images, custom Virtio devices.
- **1:04 macOS guest provisioning** — `VZMacGuestProvisioningOptions` to set credentials and enable auto-login / SSH on first boot, automating Setup Assistant.
- **4:34 Accessory Access** — pass host USB accessories into VMs with explicit, per-app user control; hot plugging supported.
- **8:26 Advanced network topologies** — integrate the `vmnet` framework with Virtualization to define exactly how VMs talk to each other and the host.
- **11:35 DiskImageKit** — new macOS 27 framework for high-performance, space-efficient disk images; layered base / cache / overlay images for efficient sharing.
- **15:57 Custom Virtio** — define custom paravirtualized devices via `VZCustomVirtioDevice` for specialized, high-performance host↔guest communication.

## Code

See `code.md` — 8 snippets extracted from the Code tab.
