# Code samples — Session 213

Extracted from the Code tab. The Code tab repeated the first SwiftUI snippet, so
it is shown once here. These illustrate making strings localizable (the
prerequisite for agent translation) and the XLIFF field that marks
machine-translated strings.

## Localizing strings in SwiftUI

```swift
Text("Hello, world!", comment: "A standard greeting")
```

## Localizing SwiftUI strings with a custom table name

```swift
Text("Hello, world!", tableName: "Greetings", comment: "A standard greeting")
```

Using the table name `Greetings` automatically puts these strings in a String
Catalog called `Greetings` (vs. the default `Localizable`).

## Localizing strings elsewhere (non-SwiftUI code)

```swift
String(localized: "Hello, world!", comment: "A standard greeting")

LocalizedStringResource("Hello World!", bundle: #bundle, comment: "A standard greeting")
```

## `leveraged-mt` field for machine-translated strings in the XLIFF

```xml
<trans-unit id="Grand Canyon" xml:space="preserve">
  <source>Grand Canyon</source>
  <target state="translated" state-qualifier="leveraged-mt">Grand Canyon</target>
  <note>Name of the 'Grand Canyon' landmark.</note>
</trans-unit>
```

When handling localizations exported from Xcode, check for the
`leveraged-mt` `state-qualifier` to identify which translations were provided by
an agent.

## Spoken APIs / artifacts (named in the talk, no Code tab entry)

- `Text`, `Button` — SwiftUI views that auto-expose their strings for localization
- `String(localized:)` and `LocalizedStringResource` — making non-SwiftUI strings localizable
- String Catalogs — default table `Localizable`; custom tables via `tableName:`
- `%lld` placeholder + plural variation handling (e.g. `un élément` / `deux éléments`)
- `TRANSLATION.md` / `AGENTS.md` — files where you place glossary, do-not-translate list, and tone guidance
- `leveraged-mt` state qualifier in exported XLIFF
