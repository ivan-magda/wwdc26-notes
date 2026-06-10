---
title: "Get the most out of Device Hub — Full Digest"
session: WWDC26 · 260
url: https://developer.apple.com/videos/play/wwdc2026/260/
duration: 17m
speakers: Matt, Hassan
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 260 — Get the most out of Device Hub

## TL;DR

**Device Hub** is a new app that ships alongside **Xcode 27** and becomes your home for
working with **both physical devices and simulators** — with one consistent set of tools
across both. Three headline ideas:

1. **One app, two modes.** A *compact* window (live screen + a few contextual hardware
   controls) auto-launches when you build-and-run to a simulator; expand it into a
   *full* window for the deeper toolset. Device Hub runs standalone — you don't need
   Xcode open to use it.
2. **Control / Organize / Configure.** The full window is organized around a central
   **canvas** (interact with the live screen directly), a **sidebar** (your whole
   device + simulator inventory, with filter/sort/group and multi-window compare), and
   a five-panel **inspector** (appearance, conditions, diagnostics, info, apps,
   profiles).
3. **A real debugging workflow.** The demo shows finding a layout bug on a physical
   iPhone and reproducing it on a simulator by mirroring app data + configuration
   (orientation, location, text size). For scripting and CI, the same capabilities are
   exposed via the **`devicectl`** command-line tool.

---

## 1. What Device Hub is

- Ships **alongside Xcode 27**, but built for "anyone who works with devices" —
  developing an app, testing across configurations, or managing an inventory. You
  **don't need to launch Xcode** to use it.
- Offers the **same set of tools** whether you're pointed at a real device or a
  simulator; you can configure either however you need.
- If you're developing, you'll meet it immediately: **build-and-run to a simulator in
  Xcode automatically launches Device Hub**, presenting a live, interactive view of the
  screen.

## 2. Two modes

**Compact mode** — a focused, lightweight window: just the screen plus a few essentials.
At the bottom are **contextual device controls** that change per device:

- iPhone/iPad: home button, screenshots, rotation
- **Apple TV:** play/pause and navigation
- **Apple Vision Pro:** environment and camera movement
- **Apple Watch:** side button and Digital Crown

**Full window** — click the **expand** button to transition. This unlocks a much broader
toolset, organized into three areas: **control, organize, configure**.

## 3. Control — the canvas

The canvas sits at the center of the full window (presented by Hassan).

- **Live display** of the screen you can interact with directly: click, drag, scroll, or
  use **natural trackpad gestures**. Works identically for a device or a simulator
  (demoed driving a connected Apple Watch).
- The **same contextual controls** as compact mode sit at the bottom.
- **Above** the canvas (not present in compact mode):
  - **Zoom** in and out.
  - **Snap to 1:1 physical size** — see the app at real-world dimensions.
  - **Resize mode** — transform the app's dimensions freely. Covered in the
    **"Modernize your UIKit app"** session.
  - **Capture keyboard** — routes the Mac's keystrokes straight to the device, for
    testing key commands and hardware support.
  - A button to switch **back to compact mode**.

## 4. Organize — the sidebar

For people juggling "dozens of devices and simulators" (back to Matt).

- The sidebar shows your **full inventory** — every device and simulator in one place.
- **Filter** menu at the top controls visibility; also **sort** and **group** by several
  options.
- **Context-click** any device for quick actions — e.g. restarting, or **pairing an
  iPhone and Apple Watch simulator** together.
- **Quick access / compare:** view any number of devices at once via **tabs** or
  **standalone compact windows**. Select several phone sizes, double-click them in the
  sidebar, and you get a compact window for each — easy side-by-side layout comparison.

## 5. Configure — the inspector (five panels)

Deep configuration lives in the inspector on the right, across **five panels**:

1. **Device settings** — change how the device looks and behaves:
   - *Appearance:* dark mode, text size, and more — **changes take effect instantly**,
     no digging through device Settings.
   - *Conditions:* test how the app responds to changes like **location**.
   - *Audio:* sound levels and I/O.
2. **Diagnostic reports** — where you start when the app **hangs or crashes**; contains
   everything the device has logged (crashes, spins, other diagnostics).
3. **Info** — storage, model, serial number — at-a-glance device facts.
4. **Apps** — install, uninstall, and manage apps, including **downloading and replacing
   their data containers**.
5. **Profiles** — manage both **configuration** and **provisioning** profiles.

> Note: the talk frames these as "five panels" reached through inspector tabs, where one
> tab groups Info / Apps / Profiles. The takeaway is the five configuration surfaces, not
> the exact tab arithmetic — see Open Questions.

## 6. End-to-end workflow — find on device, reproduce on simulator

A workout app shows altitude-based recovery advice; in **landscape**, some recovery text
gets **cut off**. The demo splits the work between two engineers.

**Hassan (on the physical device)** focuses on three things — **pairing, logging,
diagnostics:**

- **Pair the Apple Watch wirelessly:** Add button → *Pair Nearby Device* → select the Mac
  on the Watch → tap pair → enter the PIN shown in Device Hub. The Watch then appears in
  the sidebar and is available whenever nearby.
- **Install a logging profile:** Profiles panel → drag-and-drop the **CoreLocation logging
  configuration profile** → confirm on the iPhone → **reboot the iPhone for privacy
  reasons**.
- **Capture diagnostics:** build-and-run from Xcode, interact on the Mac, **rotate** to
  reproduce the cutoff, **take a screenshot**, kick off a **sysdiagnose** for
  system-level capture, and copy the clipped text off the device into a file.
- **Share app data:** Device Hub can **inspect the saved state in Finder, restore to a
  baseline, snapshot for later**, and **download** the app's data container to hand off.

**Matt (on a simulator)** reproduces UI-only bugs without a device. Three steps:

1. **Choose the right simulator** — no physical iPhone 17e, but the corresponding
   simulator is in the sidebar; verify "iPhone 17e" in the Info panel.
2. **Match the app data** — his app is empty, so he opens the **Apps inspector** and
   **replaces his data container with Hassan's**; relaunching shows all of Hassan's
   workouts.
3. **Mirror the configuration** — using the screenshot as ground truth, match the settings
   that affect UI: **landscape orientation** (rotate via device controls), **location =
   Johannesburg** (Settings inspector → high elevation → long recovery string), and —
   the missing piece — **text size all the way up**.

The bug was a **confluence**: landscape **and** that location **and** maximum text size,
all together, were needed to truncate. Device Hub let one engineer reproduce all of it on
a simulator, making it easy to verify a fix.

## 7. `devicectl` — scripting and CI

For automation, use **`devicectl`**, a command-line tool on the **same underlying
technology** as Device Hub. It's aimed at managing devices in a **test environment**:

- List devices
- Install apps
- Capture diagnostics
- Change settings (e.g. switch dark/light mode)
- Get device information
- **`--json-output`** for structured output that drops into **scripts or CI workflows**

(See `code.md` for the named commands. Exact flag spellings should be confirmed against
the docs — the transcript describes capabilities, not literal syntax.)

## 8. Next steps

- Download **Xcode 27** and try Device Hub.
- Use **`devicectl`** for scripts and CI.
- **"Modernize your UIKit app"** for the Resize-mode / resizability angle.
- **"Getting the Most Out of Simulator"** (WWDC 2019) for simulator depth.
- Device Hub **documentation** linked in the video description.
