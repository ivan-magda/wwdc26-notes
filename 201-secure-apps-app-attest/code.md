# Code samples — Session 201

Extracted from the Code tab. Timestamps map to the App Attest workflow chapters.

## 5:02 — Generate a key ID

```swift
import DeviceCheck

let keyID = try await DCAppAttestService.shared.generateKey()
```

## 6:12 — Attest the key

```swift
import DeviceCheck

let keyId: String = ...
let clientDataHash: Data = ...
let attestation = try await DCAppAttestService.shared.attestKey(keyId: keyId, clientDataHash: clientDataHash)
```

## 12:10 — Generate an assertion

```swift
import DeviceCheck

let keyId: String = ...
let clientDataHash: Data = ...
let assertion = try await DCAppAttestService.shared.generateAssertion(keyId: String, clientDataHash: Data)
```

---

## Spoken APIs / concepts (not in the Code tab)

- `DCAppAttestService.shared.isSupported` — gate adoption; a spike of unexpected unsupported responses can itself be a fraud signal.
- **Relying party identifier** = `Team Identifier` (from the provisioning profile) + app's bundle identifier; embedded in the leaf certificate.
- **Attestation object** sections: `format` (fixed Apple anonymized-attestation string), `attestation statement` (certificate chain + receipt), `authenticator data`.
- **Key access control property (ACL Blob OID)** — in the leaf certificate; macOS 27+ keys require **full security mode** + **System Integrity Protection**.
- **Authenticator-data extensions** (iOS 27+, WebAuthn-formatted): `launch validation category`, `bundle version`.
- **Assertion object** sections: `signature`, `authenticator data`; validate the strictly-increasing **counter** for anti-replay.
- **Fraud-metric receipt** sections: `signature`, `certificate chain`, `receipt payload`; fields include `risk metric` (the fraud count), `not before`, `expiration time`. Fetched via POST to the App Attest data server.
