# Session 206 — What's new in managing Apple devices

- **URL:** https://developer.apple.com/videos/play/wwdc2026/206/
- **Duration:** 23m
- **Speaker:** Cyrus Daboo (Device Management team)

## Description

An overview of what's new across Apple services, declarative device management, app
management, identity integrations, and education technologies. Covers the new Apple
Business platform and its APIs, declarative management as the new standard (managed
migration, new status items, system health monitoring), declarative app configuration
on macOS, consolidated privacy consent prompts, binary execution control, Platform SSO
enhancements (Touch ID as a second factor, web-based authentication, QR sign-in), and
education updates (Authenticated Guest Mode on Shared iPad, guided browsing in the
Classroom app).

## Key topics

- **Apple Business** — new all-in-one platform in 200+ countries; new APIs (Blueprints, Configurations, users/groups, app licenses, audit events); volume licensing for App Store subscriptions
- **Declarative management is "the standard"** — framing shift from "the future" to "the standard"; in production across fleets
- **Managed migration** — declarative configuration deployed post-enrollment to migrate data to a new Mac while preserving enrollment/settings; locked settings shown to the user
- **Credential management** — many-to-many declarative assets; multiple configurations can reference a single credential; refresh by changing the asset
- **Status channel expansion** — enrollment type, awaiting device configuration, return-to-service, Shared iPad, push token, Lockdown Mode, device system health, content caching state
- **Device system health monitoring** — hardware component health (baseband, camera, Face ID, Touch ID) surfaced as a status item on iOS/iPadOS 27
- **Enhanced log collection** — new `TriggerEnhancedLogCollection` command for AppleCare logs on org-owned devices
- **Content Caching** — declarative config + status on macOS 27; servers can POST reports to an arbitrary HTTPS endpoint
- **Declarative app configuration on macOS 27** — ManagedApp framework, hardware-bound keys, Managed Device Attestation; package file cleanup on removal
- **Privacy** — consolidated privacy consent prompt (apps + Safari websites); `app.settings` / `safari.settings` configurations
- **Binary execution control** — Endpoint Security-backed allow/deny rules using code signing properties; auto-allow managed apps
- **Platform SSO on macOS 27** — new login/unlock UI; Touch ID as required built-in second factor; web-based authentication (secure web view, QR scanning, offline auth); Authenticated Guest Mode unlocks FileVault
- **Education** — Authenticated Guest Mode on Shared iPad; guided browsing in the Classroom app

## Related sessions to fetch (referenced in this talk)

- [ ] Offer subscriptions to groups and organizations
- [ ] App Attest
- [ ] Assessment mode

## Chapter summary (Summary tab)

- **0:00 Introduction** — Cyrus Daboo; agenda: Apple services, declarative management, app management, identity, education.
- **0:41 Apple services** — Apple Business all-in-one platform in 200+ countries; new APIs (Blueprints, configurations, users, audit events); volume licensing for App Store subscriptions.
- **2:34 Device management** — declarative management is now "the standard"; managed migration moves data to a new Mac while preserving enrollment/settings; new declarative configs for Apple Intelligence/Siri/keyboard; credential assets; expanded status channel (system health, log collection, content caching).
- **9:33 App management** — declarative app configuration comes to macOS 27 (hardware-bound keys, Managed Device Attestation); package file cleanup on removal; new consolidated privacy consent prompt; binary execution control via Endpoint Security.
- **14:56 Identity Integrations** — Platform SSO on macOS 27: new login/unlock UI, required Touch ID second factor, web-based authentication, QR sign-in, Authenticated Guest Mode + FileVault.
- **19:46 Education** — Authenticated Guest Mode on Shared iPad; guided browsing in the Classroom app.

## Code

See `code.md` — no Code tab for this session (no snippets). The talk names concrete
configurations/commands/frameworks rather than Swift code.
