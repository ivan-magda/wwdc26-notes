---
title: "Secure your apps with App Attest — Full Digest"
session: WWDC26 · 201
url: https://developer.apple.com/videos/play/wwdc2026/201/
duration: 20m
speakers: Manthan (Trust and Safety team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 201 — Secure your apps with App Attest

## TL;DR

App Attest gives your server cryptographic proof that requests come from a genuine,
unmodified copy of your app running on real Apple hardware — the defense against
reverse-engineered, re-signed app clones that send valid-looking-but-fraudulent
requests (falsified quiz answers, injected game cheats, faked leaderboard scores).

Three protections, one workflow, two new-this-year additions:

1. **Verify genuine Apple hardware** — an *attestation* proves the app's key was
   generated in the device's Secure Enclave.
2. **Detect modifications** — App Attest surfaces the relying-party identifier,
   **launch validation category** (new), and **bundle version** (new) so a re-signed
   or wrong-environment build is visible to your server.
3. **Secure payloads** — *assertions* sign each request; a strictly-increasing
   counter blocks replays.

New this year: **App Attest now runs on macOS 27** (with a macOS key access control
property that asserts full security mode + System Integrity Protection), and **iOS 27
adds WebAuthn-formatted authenticator-data extensions** carrying the launch
validation category and bundle version. The closing **fraud metric** is a
receipt-based, ~30-day count of unique attested keys on a device — built to catch a
compromised "broker" device minting valid attestations for clones on other devices.

The whole client surface is three `DCAppAttestService` calls; everything load-bearing
happens on your server.

---

## 1. The threat model (0:00)

You shipped your app into a secure environment, but fraudsters target it beyond its
intended functionality. Two canonical attacks:

- **Falsified requests from a modified client.** A quiz-proctoring app gets reverse
  engineered; a modified copy submits fake quiz responses to your server. App Attest
  lets the server reject requests from modified clients.
- **Injected content you didn't ship.** A "dragon slayer" game gets a cheat menu
  injected by modifying source/resource bundles and re-signing. The clone submits
  fraudulent scores and climbs the leaderboard.

## 2. What App Attest protects (1:35)

**Genuine Apple hardware.** The attestation is cryptographic proof, verified by your
server, that the app runs on a secure Apple device.

**Awareness of modifications.** App Attest surfaces three identity signals:

- **Relying party identifier** = `Team Identifier` (from your provisioning profile)
  + your app's **bundle identifier**. A fraudster re-signing with a non-matching team
  ID becomes visible.
- **Launch validation category** (new in iOS 27). Distributed via the App Store but
  seeing a *TestFlight* launch category? That's a red flag.
- **Bundle version.** Confirms the shipped version is what's running; a fraudster
  re-signing with an unexpected bundle version is now transparent.

**Secured payloads.** Assertions are generated from a previously issued attestation's
cryptographic properties; your server verifies them to confirm the payload wasn't
tampered in transit.

## 3. Availability (4:04)

- Supported on **all Apple platforms**, now including **macOS 27+** (previously
  unsupported).
- Not available through **every app type** — e.g. it works on **Action** and **SSO**
  app extensions but not other extension types.
- Gate adoption with the **`isSupported`** API:

```swift
// DCAppAttestService.shared.isSupported
```

- Use the `isSupported` response as a **fraud signal**: a spike of *unexpected*
  unsupported responses from a user on a platform you do support may indicate
  tampering. Decide whether such a user may proceed.

## 4. Key generation (5:02)

The app asks App Attest to generate a key ID. App Attest creates a
**Secure Enclave–bound key pair** (private key stays in the Secure Enclave) and
returns a **hash of the public key** — the key ID — which you store in the **Keychain**.

```swift
import DeviceCheck

let keyID = try await DCAppAttestService.shared.generateKey()
```

Best practices:

- **One key per user** for account-based apps, or **one key per app** on the device.
  Never share keys across your user population.
- Store key IDs in the **Keychain**.
- Key IDs **survive app updates** but are **invalidated** on app reinstall, device
  restore, or restore from **iCloud backup**.
- Keys are **per-device** — they do **not** sync across a user's devices.

## 5. Attestation (6:12)

The flow:

1. App fetches the key ID from Keychain.
2. App asks **your server** to perform an attestation for that key ID.
3. **Server vends a challenge** to include in the attestation.
4. App calls the attest API with key ID + challenge.

```swift
import DeviceCheck

let keyId: String = ...
let clientDataHash: Data = ...  // hash of (challenge + client data)
let attestation = try await DCAppAttestService.shared.attestKey(keyId: keyId, clientDataHash: clientDataHash)
```

5. App Attest fetches the key pair plus **attestation data derived from the Secure
   Enclave** — a snapshot of the device's hardware properties from boot, which
   **cannot be modified** — and calls an Apple service that validates the device data
   and returns the attestation.
6. App sends the attestation to your server, which **validates**, **saves**, and
   **associates it with the user**.

Best practices:

- **Server controls initiation** — keeps you within a safe requests-per-second bound.
- On failure, **retry later with exponential back-off**; do **not** hard-code retry
  logic in the app (avoids global rate-limit spikes against Apple's attestation
  server).
- Collect attestations **outside user flows**, ideally on a **background task**.
- **Always validate on the server, never in the app** — a compromised app can't be
  trusted to validate its own attestation.

### Inside the attestation object (8:12)

Three sections:

1. **Format** — fixed string identifying the Apple anonymized attestation.
2. **Attestation statement** — a **certificate chain** + a **receipt**.
   - Certificate chain proves the key was generated on genuine Apple hardware. The
     **leaf certificate** carries the nonce, key ID, and relying-party identifier
     (validate per the Developer Documentation).
   - **macOS 27+:** the leaf also carries a **key access control property (ACL Blob
     OID)** — the security conditions the Secure Enclave enforced when the attestation
     was collected. On macOS, each key requires **full security mode** (verifies OS
     integrity) and **System Integrity Protection** (blocks unauthorized code, protects
     system paths) — both on by default on Mac. Available on all platforms but
     especially important on macOS.
   - **Receipt** — App Store–receipt-like. Validate relying-party ID, attested key,
     and your server challenge. **Store the receipt** — it's the handle for the fraud
     metric.
3. **Authenticator data** — info about your app and the attestation. **iOS 27+**
   appends an **extensions** structure (WebAuthn authenticator-model format) with two
   new identifiers: **launch validation category** and **bundle version**. Monitor for
   unexpected values and fold them into per-user risk.

**Tampering example (macOS):** a fraudster disables SIP, modifies and re-signs your
app with a different provisioning profile, and patches the App Attest framework on the
system path. The attestation reaching your server exposes the disabled-SIP state via
the key access control property, and may also show a modified Team Identifier, launch
validation category, or bundle version — so the server can reject the modified copy.

## 6. Assertion (12:10)

Once the server has validated the attestation and stored the public key, the app
signs ongoing requests:

1. App prepares data to send.
2. **Server vends a challenge** for the payload.
3. App fetches the key ID and calls the assertion API.

```swift
import DeviceCheck

let keyId: String = ...
let clientDataHash: Data = ...
let assertion = try await DCAppAttestService.shared.generateAssertion(keyId: String, clientDataHash: Data)
```

4. App embeds the assertion in the payload and transmits it.
5. Server validates the assertion and accepts/rejects the payload.

Considerations:

- **Generate on demand**, at the lifecycle point where you need them.
- Assertions are **local** — they do **not** round-trip Apple servers.
- They have **CPU cost** (crypto ops) — don't generate too many or too rapidly.
- **Validate the counter**: the server tracks a **strictly-increasing counter** per
  user for **anti-replay** protection. A **steady or decreasing** counter suggests a
  compromised clone unaware of the server's recorded value.

### Inside the assertion object (14:04)

Two sections:

1. **Signature** — validate using the authenticator data, server challenge, and the
   **public key from the attestation object**.
2. **Authenticator data** — like the attestation's, identifies app info at assertion
   time. **iOS 27+** appends the same **extensions** structure; handle it the same way.

## 7. Common pitfalls (14:58)

- **New keys for existing users are normal.** App reinstall or device restore causes
  key rotation — don't reject new keys outright, and don't immediately invalidate a
  user's previous-attestation keys. Your server's map of a user's attestations (with
  the fraud metric) is itself a fraud/abuse signal.
- **Degrade gracefully on rejection.** If the server rejects an attestation/assertion,
  degrade App-Attest-tied functionality, allow limited access with heightened
  monitoring, and avoid blocking the user without a full risk assessment.
- **Risk assessment is a business decision.** It varies by business, app type, and
  fraud implications. Follow your own deactivation/suspension guidelines. Blocking
  users without proper evaluation erodes trust and can hit legitimate users.

## 8. Fraud metric (16:27)

A compromised device can still pass attestations and act as a **broker** — minting
valid attestations on behalf of modified app instances on *other* devices, which then
send compromised requests to your server.

The **fraud metric** is an **approximate count of unique attested keys** associated
with your app on a particular device over the **past 30 days** — a way to tell if a
user is tied to attestations from a likely-compromised device.

Access flow (server ↔ App Attest data server):

1. Server retrieves the **receipt** from an attestation associated with the user.
2. Server **POSTs** the receipt to the App Attest data server.
3. The data server returns a **new receipt** carrying the fraud metric, which you use
   for subsequent fetches.

Receipt structure (App Store–receipt-like): **signature** (signs the payload),
**certificate chain** (roots to the Apple certifying authority), **receipt payload**
(attested-key info + the metric). Key fields:

- **risk metric** — the fraud-metric count.
- **not before** — earliest time you can refresh the receipt.
- **expiration time** — when the receipt expires and can no longer be refreshed.

Considerations:

- **Legitimate key rotation contributes** to the metric (reinstall, device restore →
  re-key + re-attest). So a nonzero value isn't proof of fraud.
- **Don't block users outright** on the metric. Treat it as an **investigation
  signal**: establish a baseline and watch for **spikes** as indicators of suspicious
  activity.

## 9. Next steps (19:07)

- **Rebuild against the latest SDKs** to get the newest App Attest API features.
- Identify flows that benefit from attestation/assertion (auth flows, premium-content
  payloads).
- Set up the server to **validate attestations, store receipts, track assertion
  counters**.
- Fold the **fraud metric** into your risk-assessment pipeline.
