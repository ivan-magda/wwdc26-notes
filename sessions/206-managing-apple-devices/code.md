# Code samples — Session 206

_No Code tab for this session._

This is a management/admin overview talk; it ships no Swift snippets. The spoken APIs,
declarative configurations, commands, and frameworks named in the talk are listed below
for reference.

## Spoken APIs / configurations / commands

### Apple Business APIs (new)
- Create **Blueprints** and **Configurations**
- Modify **users and groups**
- **App license** information
- Get **audit events**
- (Existing, for context) list servers / devices / inventory; manage inventory→MDM-server assignment; AppleCare warranty details

### Declarative configurations
- Managed migration configuration (deployed right after enrollment)
- Apple Intelligence / Siri / keyboard settings configurations (granular per-feature controls; shipped in 26.4, refined since)
- Credential-bearing configurations transitioned to use **declarative assets** (certificate, identity, or password)
- Content Caching service configuration (macOS 27)
- `app.settings` — new declarative configuration carrying app privacy controls + binary blocking restrictions
- `safari.settings` — existing declarative configuration; now carries Safari website permissions

### Declarative status items (new / expanded)
- Enrollment type
- Awaiting device configuration
- Return-to-service state
- Shared iPad
- Current push token
- **Lockdown Mode** turned on by user
- **Device system health** (baseband, camera, Face ID, Touch ID, and more)
- Content Caching service state

### Commands
- `TriggerEnhancedLogCollection` — start enhanced AppleCare log collection on org-owned devices (iOS, iPadOS, tvOS, macOS 27)

### Frameworks
- **ManagedApp framework** — for declarative app configuration with credentials, hardware-bound keys, and Managed Device Attestation (now on macOS 27)
- **Endpoint Security framework** — backs binary execution allow/deny + process termination, with code-signing-property matching rules

### Identity / SSO
- Platform SSO web-based authentication (secure web view in login window / screen unlock / FileVault unlock; QR scanning in an isolated system process; offline authentication)
- Touch ID as a required built-in second factor
- Authenticated Guest Mode (now unlocks FileVault on macOS; coming to Shared iPad)
