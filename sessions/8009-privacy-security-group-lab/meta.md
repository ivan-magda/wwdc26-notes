# Session 8009 — Privacy and Security Group Lab

- **URL:** https://developer.apple.com/videos/play/wwdc2026/8009/
- **Duration:** 01:01:40 (live developer Q&A, recorded and published on demand)
- **Format:** Group Lab — host + privacy/security engineering panel answering upvoted developer questions live.
- **Transcript:** machine-generated from the session SD video via whisper.cpp (`ggml-large-v3`). No Apple transcript, Summary, or Code tab exists for group labs. No speaker diarization, so attributions below are inferred from self-introductions and host routing.

## Panel

From the self-introductions (00:00–02:30):

- **David** — host; technology evangelist for privacy and security, Apple Worldwide Developer Relations. Favorite feature: passkeys.
- **Yash** — security engineer; platform security technologies, including code signing. Favorite feature: developer mode.
- **Katie** — leads the privacy engineering quality and tools team. Favorite feature: advanced data protection.
- **Dan** — engineering manager on the security engineering team, specifically the secure design team. Favorite feature: stolen device protection.
- **Rohith** — privacy engineering team; focus on hardware and sensor privacy (camera, microphone). Favorite feature: iCloud Private Relay. (Whisper also renders this name as "Rohit"; treated as the same person.)
- **Emily** — security engineer on the secure design team; primary focus Private Cloud Compute. Favorite feature: Private Cloud Compute.

## Description

A live, on-demand privacy and security group lab. After the panel shares "favorite privacy and security features," they answer upvoted developer questions spanning prompt injection and agentic risk, Private Cloud Compute, App Store nutrition labels and third-party data responsibility, on-device frameworks (Keychain, CryptoKit, CloudKit, data protection classes), passkeys, TCC, telemetry and data minimization, Siri AI privacy architecture, MDM controls, Safari ITP, conveying trust to users, careers in security, and protecting secrets in memory. The recurring throughline: privacy and security work hand in hand, lean on platform-native frameworks that are secure/private by default, and minimize the data you collect.

## Key topics

- **Prompt injection / agentic risk** — indirect prompt injection, the "lethal trifecta," deterministic vs. probabilistic mitigations (confirmation prompts, prompt spotlighting); securing agentic features in Xcode (MCP allow-listing).
- **Private Cloud Compute (PCC)** — stateless computation, non-targetability, cryptographic attestation, ephemeral wiped storage, the Virtual Research Environment (VRE), Apple Security Bounty eligibility, the new Google Cloud and NVIDIA hardware (two attestations from two separate vendors), isolated/sandboxed image parsing nodes.
- **Third-party AI APIs** — PCC guarantees do not extend to Anthropic/Google/other third-party models reached via the language model APIs; read their terms, disclose to users.
- **App privacy nutrition labels** — you must declare all data collected, including what third-party processors handle on your behalf.
- **On-device frameworks** — Keychain, CryptoKit, CloudKit, data protection classes and ACLs, out-of-process pickers, AccessorySetupKit; post-quantum TLS by default in the Network framework.
- **Passkeys** — phishing-resistant, syncable, shareable; the new Signal API for stale/revoked credential lifecycle; "zero to passkeys" advice.
- **TCC (Transparency, Consent, Control)** — permission prompts, purpose strings, sensor indicator lights, just-in-time access requests.
- **Siri AI privacy** — on-device-first, entitlements/sandboxing around the daemon that collects context, data minimization to third-party apps, multi-turn back-and-forth with PCC, TCC still applies.
- **Telemetry & data minimization** — privacy pillars (data minimization, on-device processing, transparency, control), aggregation, de-resolution, rotating/session identifiers, differential privacy, private federated learning.
- **Protecting secrets in memory (macOS)** — hardened runtime, token/memory destruction over encryption, CryptoKit zeroization, Secure Enclave-bound non-exportable keys with attestation.
- **Conveying trust** — nutrition labels, in-app transparency, "privacy assurances," avoiding "military/bank grade encryption" claims, PQ3, memory integrity enforcement.
- **MDM** — existing MDM controls for Siri still apply; file feedback for new agentic configuration needs.
- **Safari ITP** — continued investment, no new specifics this year; see WebKit.org.

## Related sessions (referenced on-air)

The panel referenced a WWDC session on securing agentic features but did not state a session number on air. No session numbers were spoken, so none are listed here.

- Securing agentic features in your app (mentioned by title only; session number not stated on air).

## Chapter summary

No published chapters (group lab). Rough arc:

- **00:00** — Intros and favorite privacy/security features (David, Yash, Katie, Dan, Rohith, Emily).
- **02:33** — Q&A begins: prompt injection and agentic model hijacking (Tanya).
- **03:04** — Third-party model data handling and PCC vs. Anthropic/Google.
- **08:15** — Nutrition labels: data you collect vs. third-party processors (Claire Casey).
- **09:28** — Agentic coding risks in Xcode (Scott G).
- **10:50** — PCC core architecture, attestation, VRE, security bounty (Yingxu).
- **17:15** — Auditing your own app; CloudKit + Swift Data, secure-by-default frameworks, post-quantum TLS (Michael Rowe).
- **22:24** — First frameworks for new developers; data minimization, Keychain, CryptoKit, passkeys (Patita).
- **23:00** — Telemetry strategy and identifiers; differential privacy, federated learning.
- **28:16** — Protecting journal-entry data while keeping search/sync; data protection classes, CK encrypted values.
- **31:21** — Xcode in an air-gapped VM; on-device code completion (evolving this design).
- **32:12** — Siri AI privacy architecture; PCC, sandboxing, TCC, multi-turn (CMDDev).
- **36:51** — What is TCC (Transparency, Consent, Control).
- **37:51** — Siri AI MDM policy (Brandon).
- **38:59** — Safari ITP / tracker prevention (User Contil).
- **39:53** — Conveying trust without security theater; nutrition labels, purpose strings, sensor lights (M3Lixer).
- **47:54** — Frameworks/concepts for a cybersecurity student; platform security guide, security.apple.com (ThumbDrive).
- **50:33** — Passkey lifecycle: stale/revoked credentials and the Signal API (Alexer).
- **51:51** — Protecting secrets in memory on macOS; hardened runtime, CryptoKit, Secure Enclave (D. Varevkin).
- **55:41** — Balancing privacy and useful telemetry; privacy pillars (KTH Chu).
- **01:00:09** — Wrap-up: forums, Feedback Assistant, survey.

## Code

See `code.md` — no code was shown on screen; this lab is verbal Q&A. API/feature names are captured in `digest.md`.
