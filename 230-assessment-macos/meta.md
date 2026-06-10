# Session 230 — What's new in assessment on macOS

- **URL:** https://developer.apple.com/videos/play/wwdc2026/230/
- **Duration:** 14m
- **Speakers:** Chris (Education Technologies team)

## Description

An overview of the enhancements to the Automatic Assessment Configuration (AAC)
framework in macOS 27. The framework creates a secure, locked-down testing
environment for organizations that deliver education assessments or certifications
on Apple devices. Using a sample assessment app, the session walks through five
areas: system precondition checks, accessibility restrictions, system experience
customization (Menu Bar, Dock, input methods, Finder/file access), application
launch restrictions, and adoption best practices. All capabilities are configured
through properties on a single `AEAssessmentConfiguration` object. The framework
requires the restricted Automatic Assessment Configuration entitlement, requestable
through the Apple Developer portal.

## Key topics

- **Precondition checks** — require a hardened device before an exam starts: `requiresSIP`, `requiresManagedDevice` (MDM enrolled), `requiresSingleUser`, `requiresUserAccountType` (e.g. `.standard`), plus `allowLockdownMode = false` and `allowPrivateRelay = false`. Unmet requirements surface an alert to the student.
- **Accessibility restrictions** — per-feature allow toggles (`allowsAccessibilityVoiceOver`, `allowsAccessibilitySwitchControl`, `allowsAccessibilityZoom`, etc.). Setting `true` *permits* a feature during the session but does not enable it; restricting one (e.g. Switch Control) quits it at session start and blocks relaunch. Features that accept user-generated content are the security-sensitive ones to gate.
- **System experience customization** — Menu Bar (`allowsMenuBar`, `allowedMenuBarItems`, `allowedAppleMenuItems`); input methods (`allowsDictation`, `allowsAutoFill`, `allowsStructuralInput`, `allowsEmojiKeyboard`); Dock (`allowsDock`); Finder + file access (`allowedDirectoriesAndFiles`, which also filters Open/Save panels). Menu Bar and Dock are hidden by default.
- **Application launch restrictions** — `allowOnlyParticipantsToRun = true` shuts down non-essential processes at session start, leaving only the assessment app, allowlisted participants, and essential system processes; `allowsUserScriptExecution = false` blocks Shortcuts and Automator scripts.
- **Best practices** — adopt the framework's APIs instead of rolling your own; restrict the minimum required (start permissive, tighten deliberately); treat accessibility as a requirement; register for session transition callbacks (begin/end is not instantaneous); re-validate on every macOS beta the day it drops.

## Related sessions to fetch

- [ ] _None explicitly named in this talk._

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of the AAC framework in macOS 27 (locked-down testing environment) and a preview of the five areas covered. Requires the restricted AAC entitlement.
- **1:34 Precondition checks** — Require a hardened device before an exam starts: System Integrity Protection, MDM enrollment, a single signed-in standard account, and disabled Lockdown Mode and iCloud Private Relay.
- **3:00 Accessibility restrictions** — Control which built-in accessibility features stay available during a session, allowing approved accommodations like Switch Control while restricting features that accept user-generated content.
- **4:33 System experience customization** — Tailor how students interact with macOS during an assessment by customizing the Menu Bar and its items, the Dock, input technologies like Dictation and AutoFill, and Finder access.
- **9:16 Application launch restrictions** — Restrict which processes run during an assessment to your app and allowlisted participants, and block Shortcuts and Automator script execution to protect the testing environment.
- **10:51 Best practices** — Rely on the framework's APIs rather than building equivalents, restrict only the minimum required, treat accessibility as a requirement, and register for session transition callbacks.
- **12:35 Next steps** — Validate device integrity with pre-checks, enable accessibility for an equitable experience, customize the system experience, block non-essential processes, and test with real exam workflows.

## Code

See `code.md` — 7 snippets from the Code tab, each building an `AEAssessmentConfiguration`.
