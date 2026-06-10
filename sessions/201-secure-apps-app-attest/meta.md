# Session 201 — Secure your apps with App Attest

- **URL:** https://developer.apple.com/videos/play/wwdc2026/201/
- **Duration:** 20m
- **Speakers:** Manthan (engineer, Trust and Safety team)

## Description

How App Attest helps protect your apps and your users against fraud. App Attest
issues cryptographic attestations that prove your app is running on genuine Apple
hardware and surfaces information that reveals tampering (relying party identifier,
launch validation category, bundle version). It can also sign payloads with
assertions so your server can detect tampering in transit. The session walks
through the integration flow — key generation, attestation, assertion — plus
common pitfalls and the receipt-based fraud metric for spotting compromised
broker devices.

## Key topics

- Threat model: modified/re-signed app copies sending valid-looking requests (falsified quiz answers, injected game cheats, fraudulent leaderboard scores)
- Three protections: verify genuine Apple hardware, detect app modifications, secure payloads with assertions
- Availability: now on macOS 27 and all major platforms; not every app extension type (works on Action + SSO extensions); gate with `isSupported`, treat unexpected unsupported responses as a fraud signal
- Key generation: Secure Enclave–bound key pair, key ID hash stored in Keychain; 1 key per user (or per app); keys are per-device, invalidated on reinstall/restore/iCloud backup restore
- Attestation: server vends challenge → app calls attest API → Apple service validates device data → server validates cert chain + receipt + authenticator data
- macOS-specific key access control property (ACL Blob OID) in the leaf certificate — reflects full security mode + System Integrity Protection enforced by the Secure Enclave
- New iOS 27 authenticator-data extensions: launch validation category + bundle version (WebAuthn-formatted)
- Assertion: server-vended challenge → app signs payload → server validates signature + strictly-increasing counter (anti-replay)
- Pitfalls: handle key rotation for existing users, degrade gracefully on rejection, run a real risk assessment before blocking
- Fraud metric: receipt-based, approximate 30-day count of unique attested keys on a device — flags a compromised broker device; POST receipt to App Attest data server, refresh per not-before / expiration

## Related sessions to fetch (referenced in this talk)

- [ ] None named explicitly. The talk repeatedly points to the App Attest / DeviceCheck "Developer Documentation" for validating the certificate chain, parsing the receipt, unpacking authenticator data, and verifying the fraud-metric receipt payload.

## Chapter summary (Summary tab)

- **0:00 Introduction** — The threats App Attest addresses: modified copies of your app sending valid-looking requests (falsified quiz submissions, injected game cheats).
- **1:35 Protections** — Verify genuine Apple hardware, detect app modifications, and secure payloads with assertions.
- **4:04 Availability** — Where App Attest is available, now including macOS 27 and all major platforms (though not every app extension type); gate with the `isSupported` API and treat unexpected unsupported responses as a fraud signal.
- **5:02 Key generation** — Create a Secure Enclave–bound key ID and store it in the Keychain.
- **6:12 Attestation** — Request and validate attestations, including the macOS key access control property and new authenticator-data extensions.
- **12:10 Assertion** — Sign payloads with attested keys and validate the assertion counter on your server.
- **14:58 Common pitfalls** — Handle new keys for existing users, degrade gracefully on rejection, and assess risk before blocking.
- **16:27 Fraud metric** — The receipt-based fraud metric (approximate 30-day count of unique attested keys on a device) and how it fits a risk profile to spot a compromised device acting as a broker.
- **19:07 Next steps** — Adopt App Attest: rebuild against the latest SDKs, identify flows that benefit from attestations and assertions, set up your server to validate and track them, and fold the fraud metric into your risk pipeline.

## Code

See `code.md` — 3 snippets extracted from the Code tab (key generation, attestation, assertion).
