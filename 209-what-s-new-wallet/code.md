# Code samples — Session 209

Extracted from the Code tab. JSON snippets are `pass.json` fragments; Swift snippets
are Pass Builder (Swift on Server) usage. Timestamps map to the transcript / Summary tab.

> Note: the two barcode `pass.json` fragments below are reproduced as Apple's slide
> showed them — the object literals are missing the comma separators between members.
> Add commas when copying into a real `pass.json`.

## 0:40 — Adopting Poster Generic (`pass.json`)

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

## 0:40 — Poster Generic + Generic fallback for iOS 26 and earlier (`pass.json`)

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
},
"generic": {
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

## 2:36 — Adopting a new barcode type (`pass.json`)

```json
"barcodes": [
  {
    "format": "PKBarcodeFormatCodabar"
    "message": "…"
    "messageEncoding": "…"
  }
]
```

## 2:36 — New barcode type + QR fallback for iOS 26 and earlier (`pass.json`)

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

## 4:27 — Featured actions (`pass.json`)

```json
"featuredActions": [
  {
    "identifier": "my-offer-id",
    "type": "membershipBenefits",
    "url": "www.example.com/offers"
  }
]
```

## 10:40 — Adding Pass Builder as a dependency (`Package.swift`)

```swift
import PackageDescription

let package = Package(
    name: "MyServer",
    products: [
        .library(
            name: "MyServer",
            targets: ["MyServer"]
        ),
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

## 11:05 — Personalizing a template with Pass Builder (`CreatePass.swift`)

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

## 12:42 — Signing the pass for distribution (`CreatePass.swift`)

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

    let passCertificate = try PassCertificate(url: "pass.p12", password: "s3cr3t")
    let wwdrCertificate = try PassCertificate(url: "wwdr.cer")

    let signer = PassSigner(
        passCertificate: passCertificate,
        wwdrCertifiate: wwdrCertificate
    )

    let destinationURL = URL(string: "/www/passes/" + doggo.id)
    try signer.signPass(package, writingTo: destinationURL)

    return destinationURL
}
```

---

## Useful API facts surfaced by the code

- **Poster Generic** is adopted via the `posterGeneric` top-level style key in
  `pass.json`, using the standard `headerFields` / `primaryFields` / `footerFields`
  structure. Only the first footer field is rendered.
- **Backwards compat** is a side-by-side pattern: keep both `posterGeneric` and the
  legacy `generic` style keys with the same fields under each.
- **New barcode formats** reuse the existing `barcodes` array; `PKBarcodeFormatCodabar`
  is the example. Lead with the new format, then list a QR fallback for iOS 26.
- **Featured actions**: `featuredActions` is a top-level array; each `Action` has
  `identifier`, `type` (e.g. `membershipBenefits`), and a value such as `url`.
- **Pass Builder Swift API** types: `PassPackage` (loads a `.pkpasstemplate`),
  `package.pass.fields.setValue(_:forKey:)`, `PassImage`, `Pass.Barcode(message:format:)`
  (`.pdf417`), `Pass.Action(id:type:url:)`, `PassCertificate`, `PassSigner` with
  `signPass(_:writingTo:)`.
- Note the typos as shown on Apple's slides: `MemeberModel` and `wwdrCertifiate`.
