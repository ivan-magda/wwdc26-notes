---
title: "What's new in managing Apple devices — Full Digest"
session: WWDC26 · 206
url: https://developer.apple.com/videos/play/wwdc2026/206/
duration: 23m
speakers: Cyrus Daboo
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 206 — What's new in managing Apple devices

## TL;DR

The annual MDM "what's new" rundown, delivered by Cyrus Daboo. Five through-lines:

1. **Declarative management is now "the standard," not "the future."** Apple retires its old tagline. Credentials, app config, and status all move into the declarative model, and the new-feature list is declarative-first.
2. **Apple Business arrives.** A new all-in-one platform live in 200+ countries with built-in device management, plus new automation APIs (Blueprints, Configurations, users/groups, app licenses, audit events) and volume licensing for App Store *subscriptions*.
3. **Deeper fleet visibility.** A big expansion of the status channel: device system health (hardware component diagnostics), Lockdown Mode state, content caching health, enrollment/return-to-service state, and a `TriggerEnhancedLogCollection` command for AppleCare logs.
4. **macOS gets enterprise parity + new controls.** Declarative app configuration (hardware-bound keys, Managed Device Attestation) comes to macOS 27; a consolidated privacy consent prompt replaces prompt fatigue; binary execution control (Endpoint Security) blocks non-compliant apps.
5. **Identity + education.** Platform SSO gains required Touch ID as a second factor, a web-based auth option (QR sign-in, offline auth, FileVault unlock), and Authenticated Guest Mode that now unlocks FileVault. Education gets Authenticated Guest Mode on Shared iPad and guided browsing in the Classroom app.

---

## 1. Apple services — Apple Business and licensing

- **Apple Business** is a new all-in-one platform combining tools for organizations of all sizes, now available in **over 200 countries and regions** — a big geographic expansion. It provides zero-touch deployment, Managed Apple Accounts, and built-in device management, lowering the bar for businesses to start managing Apple devices.
- **New automation APIs for Apple Business:** create **Blueprints** and **Configurations**, modify **users and groups**, read **app license** info, and pull **audit events**. These join existing APIs (list servers/devices/inventory, assign inventory to MDM servers, AppleCare warranty lookups).
- **Volume licensing for subscriptions:** a new mechanism lets IT admins purchase and manage **App Store app subscriptions** using the same at-scale distribution workflows already used for apps. Coming **later** to Apple Business and Apple School Manager. Deep-dive: **"Offer subscriptions to groups and organizations."**

## 2. Device management — declarative is the standard

The framing shift: Apple's old line "the future of device management is declarative management" becomes **"the standard for device management is declarative management."** It's shipping in production across fleets; not using it means "working harder than you need to."

### Managed migration

- A new **managed migration** feature moves data to a new Mac while **preserving device management enrollment and settings**.
- Activated by a **declarative configuration deployed right after enrollment**, giving IT control over which **accounts, files, and security/privacy settings** are migrated.
- **Migration Assistant reports declarative management status** so admins can monitor progress. The settings are **shown to the user but locked** — the user just clicks **Continue**.

### Apple Intelligence / Siri / keyboard configs

- 26.4 added declarative configurations for **Apple Intelligence, Siri, and keyboard** settings; the latest releases refine them with **granular per-feature controls**.

### Credential management

- Configuration profiles limit how they reference credentials, forcing large, inefficient profiles.
- The declarative model's **many-to-many** relationship lets **multiple configurations reference a single credential**. Credential-using profiles are being transitioned to declarative configurations backed by **declarative assets** (certificate, identity, or password).
- To **refresh a credential**, the server changes only the asset; the device updates every configuration that uses it.

### Status channel expansion

The status channel removes the need to poll devices. New status items include:

- Enrollment type, awaiting device configuration, return-to-service state, **Shared iPad**, current **push token**, and more.
- **Lockdown Mode** turned on by the user.
- **Device system health** — surfaces hardware component health (**baseband, camera, Face ID, Touch ID, and more**) that iOS/iPadOS already show users in Settings, now as a status item on iOS/iPadOS **27**. Gives admins a fleet-wide health view for proactive action.
- **Content Caching** state (see below).

Adopting a status item is "a simple matter of subscribing" — the device then pushes changes as they occur.

### Enhanced log collection

- New **`TriggerEnhancedLogCollection`** command lets IT admins start enhanced log collection on **org-owned devices** (iOS, iPadOS, tvOS, macOS **27**) — the same enhanced collection AppleCare normally triggers via a customer link. Declarative status monitors the process.

### Content Caching

- New **declarative configuration** to control the Content Caching service on a Mac (macOS 27) plus **status items** reporting service state — a direct way to monitor content-caching server health.
- Content cache servers can now **POST their own reports to an arbitrary HTTPS endpoint**, enabling more sophisticated monitoring consoles.

## 3. App management

### Declarative app configuration on macOS

- The declarative app configuration feature (already on iOS/iPadOS/visionOS) **comes to macOS 27**: secure provisioning of managed apps with credentials and configuration, including **hardware-bound keys** and **Managed Device Attestation** to authenticate apps/extensions with enterprise services.
- Apple encourages enterprise app developers to adopt the **ManagedApp framework**.

### Package cleanup

- macOS 27 can now **remove all files and directories** installed by a declarative management package when the **package configuration is removed** — no leftover data.

### Privacy — consolidated consent prompt

- A new **consolidated privacy consent prompt** shown on first app launch (or when a website first appears in Safari) replaces repeated camera/mic/location prompts.
- The prompt shows the **organization + app name**, an **IT-provided justification string**, and **per-component** privacy defaults each with the app's own justification.
- Two buttons: **Allow** (defaults applied, no further prompts) and **Not Now** (behaves like the unmanaged state, prompting on access). **Allow is the highlighted default button** to steer users toward the right choice.
- The same prompt covers **Safari website** permissions.
- Configurations: app privacy controls live in a new **`app.settings`** declarative configuration; Safari website permissions live in the existing **`safari.settings`** configuration.

### Binary execution control

- macOS 27 adds declarative settings to **control binary execution**, backed by the **Endpoint Security framework** — allow or deny binaries and **shut down processes** of denied ones.
- Flexible matching rules use **code-signing properties** to ensure the right binaries are matched. An option **auto-allows any managed app** without per-app rules. Aimed at compliance-driven organizations.

## 4. Identity integrations — Platform SSO

### New login/unlock experience + Touch ID second factor

- macOS 27 brings a new Platform SSO **login and unlock experience** that makes it clear the user is using organization credentials; password or Touch ID as today.
- **New:** IT admins can **require Touch ID as a built-in second factor** in addition to the password, enforced at **login, screen unlock, and FileVault unlock**.

### Web-based authentication

- A new **web-based authentication option** renders a **secure web view** in the login window and screen unlock, able to run any modern auth flow (one-time codes, push notifications for conditional access, QR password-free sign-in, custom challenge-response).
- The web view runs in a **tightly controlled OS-managed execution context**.
- **QR scanning** runs the camera in an **isolated secure system process** — the web page only receives the **decoded data**, never the raw camera feed or image data.
- Works across **login window, screen unlock, and FileVault unlock**; **offline authentication** is supported.
- Enables localized sign-in pages, accessibility-optimized flows, conditional prompting on device state, and integration with existing identity infrastructure.
- Partners named: **Authentik, ClassLink, and Identity Automation** are enabling web-login + QR support.

### Authenticated Guest Mode + FileVault

- Authenticated Guest Mode (e.g., a nurse/doctor moving room to room) now lets an authenticated guest user **sign in on FileVault-protected Macs and unlock FileVault itself**, so **full disk encryption** protects data during the guest session — for data-protection compliance. **Automatic** on devices configured for Authenticated Guest Mode; no extra configuration.

## 5. Education

### Authenticated Guest Mode on Shared iPad

- Coming to **Shared iPad later in this release**. iPad boots into a **temporary session** with a login screen; users sign in with a **Managed Apple Account** via native or **federated** authentication (full SSO support).
- Username shown top-left. The temporary session **shares device capacity** with the system (**no hard quotas**) for flexible storage. On sign-out from the lock screen, **all local data and the Managed Apple Account are removed**.

### Guided browsing in the Classroom app

- A new **guided browsing** feature lets teachers **lock students to one or more tabs**, or a **single tab** for focus.
- Teachers configure allowed websites by entering them directly or from **prepared bookmarks**, can **limit navigation** inside/outside sites, and can **grant camera/mic access** (students retain agency over keeping them enabled).
- Teachers can navigate **one or many** students to the chosen sites; student devices open the guided browser.
