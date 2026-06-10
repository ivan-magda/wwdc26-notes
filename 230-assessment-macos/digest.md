---
title: "What's new in assessment on macOS — Full Digest"
session: WWDC26 · 230
url: https://developer.apple.com/videos/play/wwdc2026/230/
duration: 14m
speakers: Chris (Education Technologies team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 230 — What's new in assessment on macOS

## TL;DR

The **Automatic Assessment Configuration (AAC)** framework on **macOS 27** gains a
broad set of new controls for building secure, locked-down exam environments. Everything
flows through one object — **`AEAssessmentConfiguration`** — whose properties you set
before starting a session. The talk groups the new surface into four control planes plus
adoption guidance:

1. **Precondition checks** — refuse to start unless the Mac is in a hardened state (SIP on, MDM enrolled, single standard account, Lockdown Mode and Private Relay off).
2. **Accessibility restrictions** — per-feature allow toggles so approved accommodations (e.g. VoiceOver, Switch Control, Zoom) stay available while content-bearing features can be locked out.
3. **System experience customization** — selectively re-introduce the Menu Bar, Menu Bar items, the Apple menu, the Dock, input methods, and Finder/file access (all hidden or restricted by default).
4. **Application launch restrictions** — shut down everything except the assessment app and allowlisted participants, and block Shortcuts/Automator script execution.

Plus **best practices**: lean on the framework instead of rolling your own, restrict the
minimum, treat accessibility as a requirement, register for session transition callbacks,
and re-test on every macOS beta. The framework needs the **restricted AAC entitlement**
(requested through the Apple Developer portal). The whole talk is demoed with a single
sample assessment app.

---

## 1. Precondition checks

Before letting a student begin, the app can require the Mac to be in a hardened,
tamper-resistant state. Four integrity checks plus two privacy-feature checks:

- `requiresSIP` — System Integrity Protection enabled.
- `requiresManagedDevice` — the Mac is MDM enrolled.
- `requiresSingleUser` — only a single account is signed in.
- `requiresUserAccountType` — the signed-in account is a specific type (e.g. `.standard`).
- `allowLockdownMode = false` — ensure Lockdown Mode is off so Apple's hardening doesn't interfere with assessment infrastructure.
- `allowPrivateRelay = false` — ensure iCloud Private Relay is off.

If one or more requirements aren't met, the framework shows the student an alert listing
what must be addressed before they may continue.

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowLockdownMode = false
    configuration.allowPrivateRelay = false
    configuration.requiresSIP = true
    configuration.requiresManagedDevice = true
    configuration.requiresSingleUser = true
    configuration.requiresUserAccountType = .standard

    return configuration
}
```

## 2. Accessibility restrictions

macOS ships a full suite of built-in accessibility features, and AAC treats them as
essential for equitable exam access — visual, auditory, motor, or cognitive needs covered
without third-party assistive software.

- **Default behavior:** the Menu Bar and Dock are hidden, but any *currently enabled*
  accessibility feature keeps running into the session (the demo shows Switch Control
  continuing after the session begins).
- **The security angle:** some accessibility features accept **user-generated content**,
  so they're a potential leak vector. You gate those for students who don't need them as
  an approved accommodation.
- **Semantics to remember:** setting a property to `true` *permits* the feature during an
  assessment when the user has it enabled — it does **not** turn the feature on. Setting it
  to `false` means that if the feature is running it's automatically **quit at session
  start and cannot be relaunched** for the duration.

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowsAccessibilityVoiceOver = true
    configuration.allowsAccessibilitySwitchControl = false   // restricted in this example
    configuration.allowsAccessibilityAlternativeInputMethods = true
    configuration.allowsAccessibilityBackgroundSounds = true
    configuration.allowsAccessibilityHoverText = true
    configuration.allowsAccessibilityLiveSpeech = true
    configuration.allowsAccessibilitySpokenContent = true
    configuration.allowsAccessibilityVoiceControl = true
    configuration.allowsAccessibilityZoom = true

    return configuration
}
```

## 3. System experience customization

Described as one of the most powerful areas of the framework — tailoring how students
interact with macOS during the exam. By default the Menu Bar and Dock are hidden; you opt
pieces back in.

### Menu Bar and Apple menu

Re-enable the Menu Bar so students reach essential app functions, then allowlist which
menu extras stay visible (volume, Wi-Fi, etc.) and strip ones that could leak information
or content. The Apple menu can be filtered down too.

- `allowsMenuBar = true` shows the bar.
- `allowedMenuBarItems` is an allowlist (e.g. `.battery`, `.clock`, `.volume`). These extras
  are **not forced on** — they remain available only if they were already present in the bar.
- `allowedAppleMenuItems` filters the Apple menu (e.g. show only `.sleep`). An **empty array**
  hides everything except "About This Mac".

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowsMenuBar = true
    configuration.allowedMenuBarItems = [
        .battery,
        .clock,
        .volume
    ]
    configuration.allowedAppleMenuItems = [
        .sleep
    ]

    return configuration
}
```

### Input methods

Several input technologies can inadvertently hand students answers or hints, so you can
disable them. Each disabled method is hidden from menus and blocked in UI controls that
normally support it (e.g. AutoFill, Dictation, and Emoji & Symbols disappear from the Edit
menu).

- `allowsDictation = false` — Dictation can produce correct spelling automatically.
- `allowsAutoFill = false` — AutoFill can inject pre-loaded answers/notes/reference material from sources like Contacts.
- `allowsStructuralInput = false` — structural input reveals character composition clues.
- `allowsEmojiKeyboard = false` — the emoji picker exposes a searchable symbol library.

Any of these could bypass an assessment meant to test unaided recall.

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowsDictation = false
    configuration.allowsAutoFill = false
    configuration.allowsStructuralInput = false
    configuration.allowsEmojiKeyboard = false

    return configuration
}
```

### Dock

Enabling the Dock gives students a clear, focused workspace for finding and switching
between apps. During the session the Dock shows **only the allowed apps** plus the
always-present anchor elements (Finder and Trash).

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowsDock = true

    return configuration
}
```

> Note: even though Finder appears in the Dock, it isn't accessible unless it's explicitly
> added as a **participant** in the session.

### Finder and file access

If the assessment needs file interaction, allow Finder as a participant and allowlist the
directories students can reach. The same allowlist also filters the standard **Open and
Save** panels, so a student saving scratch-paper work sees only the permitted directory.

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowedDirectoriesAndFiles = [
        URL(fileURLWithPath: "~/Documents/")
    ]

    return configuration
}
```

## 4. Application launch restrictions

The last control plane: restrict which processes are allowed to run. Non-essential
processes can capture screen content, log keystrokes, exfiltrate data, or otherwise
undermine the testing environment.

- `allowOnlyParticipantsToRun = true` — at session start the system shuts down
  non-essential processes, leaving only the assessment app, its explicitly allowlisted
  participants, and essential system processes.
- `allowsUserScriptExecution = false` — blocks Shortcuts and Automator script execution.

In the demo, Safari, Notes, and a long-running Shortcut are all running before the
assessment; after it starts only the sample app and its allowlisted Finder participant
remain, and the background Shortcut has been stopped.

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowOnlyParticipantsToRun = true
    configuration.allowsUserScriptExecution = false

    return configuration
}
```

## 5. Best practices

Whether adopting Assessment Mode for the first time or hardening an existing integration:

- **Let the framework do the work.** Adopt its APIs directly and delete redundant
  home-grown equivalents you've been maintaining — the framework's deep system integration
  secures the environment for you.
- **Restrict the minimum.** Every restriction can degrade the test-taker's experience.
  Start permissive and tighten deliberately.
- **Treat accessibility as a requirement, not an exception.** Design for assistive
  technologies from day one rather than carving out accommodations later.
- **Register for session transition callbacks.** Session transitions don't happen the
  instant you call begin/end. Drive app state off the framework's transition events so you
  always know when a session truly started, ended, or terminated unexpectedly.
- **Re-validate on every macOS beta the day it drops.** Run your full assessment test
  matrix and file Feedback reports immediately — don't let customers discover regressions.

## Next steps (from the talk)

- Use system pre-checks to validate device integrity before starting.
- Enable accessibility features for an equitable experience.
- Customize Menu Bar items, the Dock, and the file system for a familiar experience.
- Block non-essential processes to secure the runtime.
- Test with real exam workflows on macOS — from a classroom quiz app to a nationwide
  standardized testing platform.
