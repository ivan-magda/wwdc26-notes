# Code samples — Session 230

Extracted from the Code tab. Every snippet builds an `AEAssessmentConfiguration`,
the object that defines an assessment session's parameters. Timestamps map to the
transcript chapters.

## ~2:42 — Precondition checks (hardened device)

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

## ~4:01 — Accessibility restrictions (allow all except Switch Control)

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowsAccessibilityVoiceOver = true
    configuration.allowsAccessibilitySwitchControl = false
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

## ~5:32 — Menu Bar customization (allowlisted items + Apple menu)

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

## ~7:01 — Input method restrictions

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

## ~7:38 — Enable the Dock

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowsDock = true

    return configuration
}
```

## ~8:35 — Allowlist directories and files (Finder + Open/Save panels)

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

## ~9:58 — Application launch restrictions

```swift
import AutomaticAssessmentConfiguration

func makeAssessmentConfiguration() -> AEAssessmentConfiguration {
    let configuration = AEAssessmentConfiguration()

    configuration.allowOnlyParticipantsToRun = true
    configuration.allowsUserScriptExecution = false

    return configuration
}
```

---

## Useful API facts surfaced by the code

- Entry point is **`AEAssessmentConfiguration`** from the **`AutomaticAssessmentConfiguration`** framework; all controls are properties set before starting a session.
- **Precondition flags:** `requiresSIP`, `requiresManagedDevice`, `requiresSingleUser`, `requiresUserAccountType` (`.standard`), `allowLockdownMode`, `allowPrivateRelay`.
- **Accessibility allow-toggles** (a `Bool` per feature): VoiceOver, SwitchControl, AlternativeInputMethods, BackgroundSounds, HoverText, LiveSpeech, SpokenContent, VoiceControl, Zoom. `true` *permits* but does not enable; restricting one quits it at session start.
- **Menu Bar:** `allowsMenuBar`, `allowedMenuBarItems` (e.g. `.battery`, `.clock`, `.volume`), `allowedAppleMenuItems` (e.g. `.sleep`; empty array leaves only "About This Mac").
- **Input methods:** `allowsDictation`, `allowsAutoFill`, `allowsStructuralInput`, `allowsEmojiKeyboard`.
- **Dock & files:** `allowsDock`; `allowedDirectoriesAndFiles` (also filters standard Open/Save panels). Finder must be added as a participant to be accessible even when shown in the Dock.
- **Process lockdown:** `allowOnlyParticipantsToRun`, `allowsUserScriptExecution` (blocks Shortcuts/Automator).
