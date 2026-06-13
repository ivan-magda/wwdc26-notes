---
title: "Privacy and Security Group Lab — Full Digest"
session: WWDC26 · 8009
url: https://developer.apple.com/videos/play/wwdc2026/8009/
duration: 01:01:40
sources: transcript.md (whisper.cpp ggml-large-v3, session SD video audio), meta.md
compiled: 2026-06-12
note: Group lab — no Apple transcript/Summary/Code tab. Transcript machine-generated from SD video audio; no speaker diarization, so attributions below are inferred from self-introductions and host routing. Names spoken mid-sentence may be whisper artifacts.
---

# WWDC26 · 8009 — Privacy and Security Group Lab

> A 62-minute live developer Q&A with a six-person privacy and security panel from
> Apple. Not a feature talk: it's the unscripted layer under the WWDC26 privacy and
> security sessions. The throughline is consistent and repeated: privacy and security
> work hand in hand, lean on platform-native frameworks that are secure and private by
> default, collect as little data as you can, and be transparent with users about what
> you do collect.

## TL;DR

- **PCC guarantees stop at the third party.** Private Cloud Compute's stateless and
  non-targetability guarantees apply only to Apple's PCC interfaces (Foundation Models
  and other PCC-backed APIs). If your app sends data to Anthropic, Google, or any other
  third-party model, those guarantees do not follow it. Read their terms and disclose
  to your users.
- **You declare third-party data collection too.** For App Store nutrition labels, you
  are responsible for declaring all data collected from your app, whether it goes to
  your servers or a third party's. Understand what those processors do with it.
- **PCC's new cloud hardware demands two attestations from two separate vendors.** For
  the newly announced Google Cloud (and NVIDIA) PCC hardware, every piece of hardware
  must produce two verifiable attestations from two separate vendors, described as a
  step above what most cloud platforms enforce. Image-parsing for diffusion features
  (spatial reframing) runs in extra-sandboxed, isolated nodes, adding a little latency.
- **The Virtual Research Environment is for everyone, not just researchers.** The VRE
  ships with the PCC security guide. Anyone can download it, run Apple's exact server
  software virtualized on their own Mac, and verify Apple's claims. Findings are
  eligible for Apple Security Bounty. Transparency is continuous: if the back end
  changes, the attestation changes and devices reject rogue nodes automatically.
- **"Zero to passkeys."** The panel's strongest single piece of advice: if you have user
  accounts, do not bother letting users set a password. Adopt passkeys from the start.
  Stale/revoked passkey lifecycle is handled by a new **Signal API**.
- **Hardened runtime first for in-memory secrets.** On macOS, enable hardened runtime so
  your process memory can't be read and unsigned code can't be mapped in. Prefer
  destroying tokens after use over encrypting them in RAM. CryptoKit zeroizes key
  backing memory; the Swift runtime won't guarantee that if you roll your own.
- **Secure Enclave-bound keys.** You can bind a generated key to the device's Secure
  Enclave so it is non-exportable even if the app, process, or device is compromised,
  and attest that the key is held by the Secure Enclave.

## Panel

Host **David** (technology evangelist for privacy and security, Worldwide Developer
Relations) with **Yash** (security engineer; platform security, code signing),
**Katie** (leads privacy engineering quality and tools), **Dan** (engineering manager,
secure design team), **Rohith** (privacy engineering; hardware and sensor privacy:
camera, microphone), and **Emily** (security engineer, secure design team; Private
Cloud Compute).

## Favorite privacy and security features (warm-up, 00:00–02:32)

- **David** — passkeys: simple login, protects everyone from phishing.
- **Yash** — developer mode.
- **Katie** — advanced data protection.
- **Dan** — stolen device protection.
- **Rohith** — iCloud Private Relay ("an incredible technology… read up on it").
- **Emily** — Private Cloud Compute (also her primary focus).

David flips his answer late in the hour, declaring **Apple Silicon** his new favorite
after the Siri AI discussion, because so much processing and so many security
boundaries can live on device.

## Developer Q&A

### Prompt injection and agentic risk

- **Hijacking models via prompt injection (Tanya).** Agentic technology brings a new
  category of risk. **Indirect prompt injection** is the headline threat: an attacker
  plants instructions in, say, a malicious document, and when a user points an agentic
  tool at it, the model reads it into context and may act against the user's interest.
  This ties to the externally-discussed **"lethal trifecta"**: models are most at risk
  when they have access to private data, can perform actions, and ingest untrusted
  context. For features like Siri AI, Apple combines **deterministic mitigations**
  (confirmation prompts the user taps) with **probabilistic mitigations** such as
  **prompt spotlighting** (marking where a prompt contains untrusted content). Pointer:
  a WWDC session on securing agentic features in your app (named by title, no number
  spoken).
- **Agentic coding risks in Xcode (Scott G).** Same secure-by-design approach applied to
  Siri AI, Safari features, and Xcode. Xcode adds extra mitigations, e.g. you can
  **allow-list the tools** Xcode may call when used as an MCP server. Each use case gets
  the mitigation set that fits its user experience.

### Private Cloud Compute and third-party models

- **What happens to data sent to PCC vs. a third-party model like Anthropic or Google
  (Tanya, follow-up).** PCC's guarantees apply only to Apple's PCC interfaces (Foundation
  Models and other PCC-backed APIs): **stateless computation** and **non-targetability**
  (a user's request cannot be targeted), all **cryptographically provable** via published
  documentation and tooling. Apple does not collect your data with PCC: it lives on the
  node only to fulfill the request, is removed when done, and an **ephemeral mode** wipes
  the persistent-data volume on every reboot, all provably attestable. For **third-party
  AI APIs reached via the language model interfaces**, none of that applies: read the
  provider's terms and documentation and disclose to your users what you learn.
- **PCC core architecture, "mathematical" assurance, hidden flaws (Yingxu).** Start with
  the in-depth **PCC security guide on Apple.com** (documentation exists at multiple
  depth levels). Beyond stateless compute and verifiable transparency: for the new
  **Google Cloud** PCC hardware, every piece of hardware must produce **two verifiable
  attestations from two separate vendors** (Yash), above what most cloud platforms
  enforce. New **diffusion capabilities for spatial reframing** (shipped that week) parse
  images in the cloud; that complex parsing is **isolated into separate, extra-sandboxed
  nodes**, trading a little latency for protection. The systems are hardened at their
  core, not just wrapped in attestation: purpose-built, hardened OSes with functionality
  deliberately removed to shrink attack surface.
- **The Virtual Research Environment.** Shipped with the security guide, the **VRE** is a
  specially-tooled VM that lets anyone run Apple's exact PCC application software,
  virtualized, on their own Mac, and verify each application's claims. Eligible for
  **Apple Security Bounty**. Explicitly **not limited to security researchers**: fully
  public, no gating, so an enterprise can verify claims before enabling capabilities for
  its users. Transparency is **continuous**: if a back end changes you can re-verify the
  newest deployment in real time, and if anyone deploys rogue software to a node the
  **attestation changes and devices automatically reject it**. "Don't just trust us, you
  can prove it yourself."

### Nutrition labels and third-party processors

- **Data I collect vs. data a third-party processor handles (Claire Casey).** Framed
  around **App Store privacy nutrition labels** (a glanceable, comparable summary of an
  app's data practices: what's collected, whether it's linked to identity, and for what
  purposes). The rule: **you must declare all data collected from your app, whether it
  goes to your servers or another company's.** It's your responsibility to understand
  what third parties do with it so you can represent the full picture to users.

### Auditing your own app, and secure-by-default frameworks

- **New tools to audit my app's privacy/security; Swift Data + CloudKit (Michael Rowe).**
  Generative AI is now a practical audit tool: ask your coding agent what your app's
  privacy/security guarantees are and have it check against what you expect. Apple's
  frameworks aim to be **secure by default and, where possible, private by default**
  (e.g. out-of-process pickers). Concrete example: the **Network framework** using **TLS**
  now defaults to the latest **post-quantum cipher suites**; when iOS 26 shipped support,
  sites saw a large uptick in post-quantum TLS traffic with no developer code changes for
  apps using the default configuration. **CloudKit** lets Apple sync user data securely
  across the user's devices without you running a server (which would be a lot of risk to
  take on). Two questions to anchor security and privacy: what are the **inputs** to your
  app and how much do you trust them, and where do you **vend** (hand out) the data you
  collect. And remember you are also a user of others' apps: build what you'd want used on
  your own data.
- **First frameworks for new developers (Patita).** Privacy: **CloudKit** for sync,
  **Keychain** for secure on-device storage, **CryptoKit**, encourage **passkeys**. But
  first ask whether you need the data at all: **data minimization** removes future risk.
  Use **out-of-process pickers** for protected categories (location, photos, contacts) so
  the user selects exactly what to share, and ask whether you need location once vs.
  continuously. Security: **Keychain's data protection classes** let you apply the right
  ACL/level (e.g. "available after first unlock" vs. "always available," the latter being
  not great), for free.

### Telemetry and data minimization

- **Telemetry strategy.** Start with data minimization: what question are you actually
  answering, and what will you do with the answer (code change, business decision)? Keep
  it actionable. Tell users what you collect and why; it's their data, donated to you.
  Choose the right **identifier**: real identity vs. an anonymous user, and prefer a
  **rotating / session-based identifier** over a device-based one. Then **bucket** and
  **de-res** the data. For more sensitive data, **private federated learning** and
  **differential privacy** give a mathematical guarantee that the data can't be connected
  back to the original person.
- **Passkeys plug.** "Zero to passkeys": don't let users set passwords, adopt passkeys
  from default. They're a complete password replacement, **not phishable**, **syncable**,
  shareable if you want. They also reduce login friction and the cost of password resets,
  getting users to your app's value faster.
- **Balancing privacy with useful telemetry (KTH Chu).** Apple's framing (from Craig, on
  stage years ago) is **"great features and great privacy,"** both, not one, which
  sometimes takes extra creativity. Worked through the **privacy pillars**: (1) **data
  minimization**: do you need full-grain timestamps, or is "morning" / "that day" / a
  weekly aggregate enough; (2) **on-device processing**: do the aggregation/minimization
  on device before sending; (3) **transparency and control**: let users see and control
  what's collected, and **technically enforce** a "no" so you actually stop collecting;
  (4) **security**: protect collected data from attackers, and think about **when you
  delete** it. Caution: an un-aggregated time series can carry behavioral patterns you
  didn't intend to collect (the alarm-telemetry example: fine-grained timestamps reveal
  when people wake up). Aggregation also saves bandwidth.

### Protecting sensitive data (storage and memory)

- **Journal entries: protect while keeping search and sync (upvoted).** Layer the data
  protection classes. In **Keychain**, create a key to encrypt the data and set a data
  protection class such as **only when unlocked**, optionally with an **ACL requiring
  biometric unlock** (Face/Touch). For files on disk, set a data protection class
  (**only when device is unlocked** for sensitive data). To sync via **CloudKit**, use
  **CKRecord encrypted values** so that users with **advanced data protection** get
  end-to-end encryption across devices. For background indexing/search: there's a **class
  of key that keeps data available for a period of hours after the device locks** (Dan
  couldn't recall the exact API name, "transcript unclear", refer to developer docs), so
  an app can index in the background and then the key locks and the data goes dark. Color:
  the reason a freshly-rebooted phone shows only phone numbers (not contact names) on the
  lock screen is that contacts are a data class unavailable until full passcode unlock.
- **Protecting secrets in memory on macOS: passwords, tokens, keys (D. Varevkin).** First
  step is **hardened runtime**: your application memory can't be read by others and
  malicious/unsigned code can't be mapped into your process, blocking the common attacks
  (spinning a thread to read memory, or attaching a debugger). On encryption vs.
  destruction: for short-lived secrets, **destroy the token after use** rather than
  encrypt it; long-lived in-memory encryption is really DRM-level territory, not pure
  security. Use **CryptoKit** for keys: it **zeroizes** the backing memory; the Swift
  runtime won't guarantee zeroization if you roll your own. And bind keys to the **Secure
  Enclave** so they're **non-exportable** even if the app/process/device is compromised,
  with an **attestation interface** to prove the key is Secure-Enclave-held.

### Siri AI privacy architecture

- **How Siri AI ensures privacy/security (CMDDev).** Siri does a lot on device but calls
  larger models hosted in **PCC**; that combination (PCC's power plus on-device personal
  information and the **semantic index**) is what makes Siri AI work while keeping data
  protected. On device, **entitlements and sandboxing** restrict which process can access
  user data: prompts are processed in a very secure container, handed to a secure daemon
  that collects context into a format, then vended to PCC. Not every process can do this.
  Even with third-party apps, data is **minimized**: a Siri directive sends only what the
  query needs, nothing extra. **TCC** still applies (e.g. user choice over location with
  Siri). Interaction is **multi-turn**: PCC may come back to the device for more context,
  and TCC may prompt for access not granted before. Apple Silicon enables keeping so much
  on device, and **Core AI framework** (transcript unclear on exact name) lets developers
  run their own models on device.
- **What is TCC (David prompts the panel to spell it out).** **Transparency, Consent, and
  Control**: the on-device frameworks/protections that let apps ask users for access to
  additional data (photos, microphone, etc.). The benefit is a high-trust guarantee: an
  app does **not** have your microphone data unless it prompted you and you said yes.

### Enterprise, web, and dev environment

- **Siri AI MDM policy (Brandon).** Yes. Existing **MDM controls for Siri** still apply.
  File feedback with proposals for how you'd want new agentic features configured in an
  enterprise environment. Example called out: the new **Safari automatic password
  change** feature is a good security uplift for small businesses without single sign-on.
- **Safari ITP / tracker prevention this year (User Contil).** ITP is a long-running,
  continuously-invested-in technology, but **no specific new items to reference this
  year**. Learn more on **WebKit.org**.
- **Xcode AI coding in an air-gapped VM (evolving this design).** Existing **code
  completion is entirely on device and works offline**. Other features, particularly ones
  integrating with **Claude Code** (whisper: "Clawed Code") or **OpenAI's Codex**, will
  require internet. So it varies, but code completion itself works offline.

### Conveying trust to users

- **How to make trust observable without security theater (M3Lixer).** Lead with
  **nutrition labels**: standardized, glanceable, carry more weight than a self-made "we
  don't collect identifiable data" claim. Add **in-app transparency**: show users the data
  you hold so there's no surprise. Internally Apple uses **"privacy assurances"**: simple,
  understandable privacy promises per feature that any architecture can be held up
  against, a north-star for the whole team. **Consistency** builds trust over time, it's
  organic. On security, decide **what story/guarantee you want to make** (examples:
  **memory integrity enforcement**, **iMessage post-quantum encryption / PQ3**) and make
  it feel effortless to the user. Demonstrate care by having expected security features
  **present when users expect them** (passkeys or Sign in with Apple at account creation),
  and request only data you need (example: **AccessorySetupKit** for a Bluetooth accessory
  instead of full Bluetooth API access). **Never** claim "**bank-grade**" or "**military-
  grade**" encryption: users don't know what it means, and doing things by default you'll
  generally do the same or better. Apple open-sources part of PCC as proof it walks the
  walk. **Timing matters**: ask for access at the moment it's needed (camera access when
  the user wants a picture), with a **good purpose string** explaining why, so users say
  yes and don't have to dig through Settings (or give up). And honor sensor expectations:
  the **sensor indicator lights** turning on unexpectedly is an instant "no" (Rohith);
  only use granted sensors when the user expects it.

### Careers in security

- **What to learn first for app + platform security (ThumbDrive, a cybersecurity
  student).** Apple is often hiring for security and privacy roles. Start with the
  **platform security guide** (deeper than standard docs: data protection, the **Secure
  Enclave**, how it ties together). Follow the **security.apple.com** blog (also home to
  the **bug bounty** program and vulnerability reporting); recent posts include
  **extending PCC onto Google and NVIDIA hardware**, plus past posts on memory integrity
  enforcement, iMessage, and kernel memory allocation changes. Generic advice: low-level
  security means learning **binary exploitation** and **reverse engineering**; networking
  means learning **protocols, how TLS works, how the internet works**. It comes down to
  understanding how something works well enough to either attack or defend it.

### Passkey lifecycle

- **Stale, expired, revoked passkeys in the Passwords app (Alexer).** "Solved": a new set
  of APIs called the **Signal API** notifies your app or website that things have changed
  and need updating. Passkeys are part of a wide cross-company standard whose governing
  body includes Apple; they only work because they sync everywhere, so companies
  collaborate on it.

## Unconventional facts and takeaways (the live-Q&A gold)

- **PCC guarantees are bounded.** A clear, candid limit: the moment your data leaves for
  Anthropic/Google/any third-party model, PCC's stateless and non-targetability
  guarantees no longer apply. This was stated plainly, twice.
- **Two attestations, two vendors.** For the new Google Cloud / NVIDIA PCC hardware,
  Apple requires two verifiable attestations from two separate vendors per piece of
  hardware, called out as above industry norm.
- **Image parsing is quarantined.** New cloud diffusion/spatial-reframing parsing runs in
  separate, extra-sandboxed nodes, deliberately accepting added latency for isolation.
- **The VRE is public, not gated to researchers.** Anyone (including enterprises) can run
  Apple's exact PCC software virtualized and verify claims; findings earn bounty.
- **Continuous transparency with auto-reject.** A changed back end changes the
  attestation; rogue node software causes devices to reject it automatically.
- **Post-quantum TLS for free.** Apps on the Network framework's default config got
  post-quantum cipher suites with no code change in iOS 26, visibly moving internet-wide
  PQ TLS traffic.
- **A "few hours" data-availability key class** exists for background indexing after lock
  (exact API name not recalled on air, "transcript unclear").
- **Hardened runtime is the first answer** for in-memory secrets on macOS, and the panel
  prefers destroying tokens over encrypting them in RAM.
- **CryptoKit zeroizes; DIY does not.** The Swift runtime won't guarantee key-memory
  zeroization outside CryptoKit.
- **Secure Enclave-bound keys are non-exportable** even on full device compromise, with
  attestation.
- **Don't say "bank-grade" or "military-grade" encryption.** Marketing claims most users
  can't parse; defaults usually do the same or better.
- **Why the lock screen shows only phone numbers** after a reboot: contacts are a data
  class gated behind full passcode unlock.
- **Cultural color:** the running "favorite feature" gag (David switches to Apple Silicon
  mid-lab); repeated "six-hour lecture by Emily" jokes about PCC depth; "imagining someone
  in a bunker with a MacBook" for the air-gapped question; "I hope that's not your pin"
  about a username; the host failing to get the lab extended to six hours.
