---
title: "What's new in Wallet — Full Digest"
session: WWDC26 · 209
url: https://developer.apple.com/videos/play/wwdc2026/209/
duration: 16m
speakers: Shaun
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 209 — What's new in Wallet

## TL;DR

iOS 27 is "a new chapter for building Wallet Passes." Four headline themes:

1. **Poster Generic** — a new pass style where a full-bleed background image takes
   center stage. Adopt it with the `posterGeneric` key in `pass.json`; keep the legacy
   `generic` style alongside it so iOS 26 users can still add the pass.
2. **Four new barcode types** — EAN-13, Code 39, Codabar, and ITF, via the existing
   `barcodes` array. Older iOS can't render them, so list fallback formats (e.g. QR) in
   priority order — and have a manual-entry plan when multi-format isn't possible.
3. **Featured actions** — a new top-level `featuredActions` API that surfaces up to two
   prioritized actions below the pass face for *every* pass style (generalizing the iOS
   18 second-gen event-ticket semantic URLs).
4. **A new developer toolchain** — **Pass Designer** (a WYSIWYG Mac app that renders
   true-to-iOS as you build) produces `.pkpasstemplate` files; **Pass Builder** (a Swift
   on Server package for Mac + Linux, plus a `buildpass` CLI) personalizes, signs, and
   distributes those templates at scale — and is reachable from Java and other languages.

The running demo is "Stacey's Doggy Day Care" — designing a Poster Generic membership
card template, then personalizing one pass per dog on a Swift server.

---

## 1. Poster Generic

A new pass style for **membership cards, loyalty programs, store cards** — anywhere you
want bold, colorful artwork to take center stage. The pass face is composed of:

- a background image
- a primary logo
- header fields
- primary fields
- a single footer field
- a barcode (if supplied)

Adopt it with the `posterGeneric` top-level style key, then fill in the usual pass
field structure; Wallet handles layout across the face.

```json
"posterGeneric": {
  "headerFields": [
    {
      "key": "memberID",
      "label": "Guest No.",
      "value": "102035"
    }
  ],
  "footerFields": [
    {
      "key": "membershipType",
      "value": "Family Pass"
    }
  ]
}
```

- **Only the first footer field renders** — extras are dropped.
- **Poster Generic requires iOS 27+.** For iOS 26 and earlier, include the existing
  `generic` style key *alongside* `posterGeneric`, with relevant fields under each, so
  older customers can still add the pass.

```json
"posterGeneric": { /* … */ },
"generic": { /* same fields … */ }
```

- Design tip from the demo: **omit the label on the first primary field** to get the
  bold, oversized title treatment for the value.

## 2. Four new barcode types

iOS 27 adds **EAN-13, Code 39, Codabar, and ITF**, specified through the existing
`Barcode` object and `barcodes` array. E.g. for Codabar set
`format` to `PKBarcodeFormatCodabar` (full list in the Wallet Passes docs).

```json
"barcodes": [
  {
    "format": "PKBarcodeFormatCodabar"
    "message": "…"
    "messageEncoding": "…"
  }
]
```

Because **older iOS can't render the new formats**, provide a priority-ordered array
with a widely-supported fallback (e.g. QR). If you ship only a new format and nothing
else, **iOS 26 and earlier renders no barcode at all.** Lead with your preferred format,
fall back gracefully:

```json
"barcodes": [
  {
    "format": "PKBarcodeFormatCodabar"
    "message": "123456789"
    "messageEncoding": "iso-8859-1"
  },
  {
    "format": "PKBarcodeFormatQR"
    "message": "123456789"
    "messageEncoding": "iso-8859-1"
  }
]
```

> The slide's object literals are missing the comma separators between members — add
> them in a real `pass.json`.

**When multiple formats aren't an option**, do two things:

1. Surface the credential ID in a prominent pass field (a `primaryField` or
   `headerField`) so it can be typed in manually.
2. Train front-line staff for manual-entry workflows. "A pass that can't be scanned
   shouldn't result in a blocked customer."

## 3. Featured actions

The iOS 18 second-gen event ticket let you attach semantic URLs that draw extra actions
below the pass (e.g. "view event schedule"). iOS 27 generalizes this into a **flexible
API for all pass styles**: a top-level `featuredActions` key holding an array of
`Action` objects. Each action has a **unique identifier**, an **action type**, and a
**value** such as a URL.

```json
"featuredActions": [
  {
    "identifier": "my-offer-id",
    "type": "membershipBenefits",
    "url": "www.example.com/offers"
  }
]
```

Wallet draws each one below the pass with an appropriate colorful icon and a localized
call-to-action. **Up to two featured actions per pass, in priority order** — keep them
to the most meaningful actions for your customers. The supported action types and their
expected values are in the Wallet Passes documentation.

## 4. Pass Designer (new Mac app)

Passes have accumulated a decade of features, and it's hard to connect what's in the
pass bundle to what shows up on device. **Pass Designer** is a WYSIWYG Mac editor with a
**true-to-iOS live preview**: a rendered pass on the right, an editing sidebar on the
left. The sidebar configures Identity & Signing, pass style, images, barcodes, fields,
and (where supported) semantics.

Demo flow (the doggy day-care membership card):

- Switch **Style → Poster Generic**; drag a photo into **Images** as the background.
- Add a **header field** (`DOG_ID` / label "Member ID").
- Add **primary fields**: `DOG_NAME` (label omitted → bold title) and `LOVES`
  (label "Loves").
- Under **Barcode & NFC**, swap a wide QR for the narrower **PDF417** via the Format
  picker.
- Add a **primary logo**, set the **Label Color** under Style, add a **footer field**
  ("Stacey's Doggy Day Care").
- **File → Save** writes a **`.pkpasstemplate`** template file.

## 5. Pass Builder (Swift on Server)

Pass Designer makes *templates*; **Pass Builder** turns a template into a personalized,
signed, distributable pass. It's a **Swift on Server package that runs on Mac and
Linux**, exposing a Swift API and a **`buildpass` command-line executable**.

Add it as a dependency:

```swift
import PackageDescription

let package = Package(
    name: "MyServer",
    products: [
        .library(name: "MyServer", targets: ["MyServer"]),
    ],
    dependencies: [
        .package(path: "./path/to/PassBuilder")
    ],
    targets: [
        .target(
            name: "MyServer",
            dependencies: [
                .product(name: "PassBuilder", package: "PassBuilder")
            ]
        ),
        …
    ]
)
```

Personalize a template at scale. `PassPackage` loads the `.pkpasstemplate` and gives
type-safe access to the pass bundle; `package.pass` reaches into `pass.json`:

```swift
import PassBuilder

func createPass(for doggo: MemeberModel) async throws -> URL {
    var package = PassPackage(url: "template.pkpasstemplate")

    package.pass.fields.setValue(doggo.name, forKey: "DOG_NAME")
    package.pass.fields.setValue(doggo.favoriteToy, forKey: "LOVES")
    package.pass.fields.setValue(doggo.id, forKey: "MEMBER_ID")

    package.background = PassImage(url: doggo.photoURL)

    package.pass.barcodes = [
        Pass.Barcode(message: doggo.id, format: .pdf417)
    ]

    package.featuredActions = [
        Pass.Action(id: "action-1", type: "viewMembership", url: doggo.membershipURL)
    ]
    …
}
```

**Signing.** To ship a pass you normally have to: write a manifest of the bundle
contents, create a detached signature of that manifest, write it back into the bundle,
then zip the directory and rename it `.pkpass`. **Pass Builder does all of that** — you
just supply the certificates (your pass-signing cert and the WWDR intermediate),
construct a `PassSigner`, and call `signPass(_:writingTo:)`:

```swift
let passCertificate = try PassCertificate(url: "pass.p12", password: "s3cr3t")
let wwdrCertificate = try PassCertificate(url: "wwdr.cer")

let signer = PassSigner(
    passCertificate: passCertificate,
    wwdrCertifiate: wwdrCertificate
)

let destinationURL = URL(string: "/www/passes/" + doggo.id)
try signer.signPass(package, writingTo: destinationURL)
return destinationURL
```

## 6. Using Pass Builder from other languages

Pass Builder isn't Swift-only:

- **swift-java** generates native **Java bindings** for the Swift API, so you can invoke
  Pass Builder from the Java runtime. (See the session "Explore Swift and Java
  interoperability.")
- Apple is publishing **protobuf definitions of the Pass Package format**, so any
  language can generate type-safe models, build a **customization message**, and invoke
  the **`buildpass`** CLI to personalize and sign — without writing Swift.

## Next steps (Apple's)

- Try Pass Designer; experiment with Poster Generic and decide if it fits your pass.
- If adopting any new barcode type, plan graceful fallbacks.
- Identify your most meaningful actions and wire them up as featured actions.
