# Session 209 — What's new in Wallet

- **URL:** https://developer.apple.com/videos/play/wwdc2026/209/
- **Duration:** 16m
- **Speakers:** Shaun

## Description

What's new in Wallet for iOS 27. A new Poster Generic pass style puts bold,
full-bleed artwork front and center; four new barcode types (EAN-13, Code 39,
Codabar, ITF) add point-of-presentation flexibility; and featured actions surface
relevant actions below the pass face for *every* pass style. The session closes with
a brand-new developer toolchain for Mac and server: Pass Designer (a WYSIWYG template
editor) and Pass Builder (a Swift on Server package + `buildpass` CLI) for
personalizing, signing, and distributing passes at scale.

## Key topics

- **Poster Generic** — new `posterGeneric` pass style (iOS 27+): background image,
  primary logo, header fields, primary fields, single footer field, optional barcode.
  Include the legacy `generic` style alongside it for iOS 26 and earlier.
- **Four new barcode types** — EAN-13, Code 39, Codabar, ITF, via the existing
  `barcodes` array; provide fallback formats (e.g. QR) in priority order for older iOS.
- **Featured actions** — top-level `featuredActions` key, array of `Action` objects
  (`identifier`, `type`, value/URL); up to 2 per pass, in priority order; works for all
  pass styles. Generalizes the iOS 18 second-gen event ticket semantic-URL actions.
- **Pass Designer** — new Mac app; WYSIWYG, true-to-iOS live preview; saves
  `.pkpasstemplate` files.
- **Pass Builder** — new Swift on Server package (Mac + Linux); Swift API +
  `buildpass` CLI; loads templates, personalizes fields/images/barcodes/actions, and
  signs passes (handles manifest, detached signature, zip + `.pkpass`).
- **Cross-language** — swift-java generates Java bindings; protobuf definitions of the
  Pass Package format let any language build a customization message for `buildpass`.

## Related sessions to fetch (referenced in this talk)

- [ ] Explore Swift and Java interoperability

## Chapter summary (Summary tab)

- **0:01 Introduction** — Shaun; passes help people move faster, more privately,
  more securely. iOS 27 adds Poster Generic, four barcode types, featured actions, and
  a new Mac/server developer toolchain.
- **0:40 Poster Generic** — new `posterGeneric` pass style for membership/loyalty/store
  cards; background image + primary logo + header/primary fields + single footer field
  + optional barcode. Include `generic` alongside it for iOS 26 backwards compatibility.
- **2:36 Barcodes** — four new types (EAN-13, Code 39, Codabar, ITF) via the existing
  `Barcode` object; provide priority-ordered fallbacks (e.g. QR) so older iOS still
  renders something. If multi-format isn't possible: surface the credential ID in a
  prominent pass field and train staff for manual entry.
- **4:27 Featured actions** — top-level `featuredActions` array of `Action` objects
  (id, type, value); up to 2, priority order; all pass styles; Wallet draws a colorful
  icon + localized call-to-action below the pass.
- **5:46 Developer tools** — new suite for Mac and server: Pass Designer + Pass Builder.
- **5:47 Pass Designer** — WYSIWYG Mac editor with true-to-iOS rendering; produces
  `.pkpasstemplate` template files.
- **10:40 Pass Builder** — Swift on Server package (Mac + Linux) + `buildpass` CLI;
  personalizes templates from Pass Designer, then signs and validates passes.
- **13:50 Personalizing a pass template** — swift-java generates Java bindings;
  protobuf definitions of the Pass Package format let you generate type-safe models in
  any language, build a customization message, and invoke `buildpass`.
- **15:01 Next steps** — try Pass Designer with Poster Generic; plan graceful barcode
  fallbacks; pick the most meaningful featured actions.

## Code

See `code.md` — 8 snippets from the Code tab (pass.json fragments + Swift Pass Builder).
