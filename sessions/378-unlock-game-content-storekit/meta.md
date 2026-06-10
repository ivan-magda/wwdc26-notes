# Session 378 — Unlock in-game content with StoreKit and Background Assets

- **URL:** https://developer.apple.com/videos/play/wwdc2026/378/
- **Duration:** 10m
- **Speakers:** Sam (StoreKit team engineer)

## Description

New tools for building games on Apple platforms: updates to Managed Background
Assets (Apple-hosting up to 200 GB per app, localized asset packs, Steam depot
conversion), two new Apple Unity plug-ins (Background Assets and StoreKit) that
bridge C# to the native frameworks, and ways to enhance a game's presence on the
App Store and the Apple Games app.

## Key topics

- **Apple-Hosted Managed Background Assets** — download asset packs only when needed; Apple hosts up to **200 GB per app**, included in the Developer Program membership; available since iOS/iPadOS/macOS/tvOS/visionOS 26
- **Localized asset packs (iOS 27)** — system delivers only the player's preferred-language assets (set in Settings); fallback chain: exact regional variant → base language of preferred → primary app language
- **Steam depot conversion** — `xcrun ba-package convert` turns Steam depots into asset pack manifests, then `ba-package` turns a manifest into an `.aar` archive; tool coming to Linux/Windows
- **Apple Unity plug-ins** — new Background Assets + StoreKit plug-ins; C# API bridging to native frameworks; build with Xcode 27, Python 3, Unity 2022 LTS or later; on GitHub
- **StoreKit C# flows** — `Product.FetchProducts`, `Product.Purchase()`, `PurchaseResult` + `IsVerified`, `Transaction.Updates` listener, `CurrentEntitlements` as source of truth; consumables handled inline
- **Background Assets C# flow** — `AssetPackManager.GetManifestAsync`, `EnsureLocalAvailabilityOfAssetPackAsync`, `DownloadStatusUpdatesAsync` for progress UI
- **Testing** — StoreKit Testing in Xcode (config file in scheme) + Background Assets mock server (auto-attaches to debug session in Xcode 27); Sandbox testing via App Store Connect
- **Game presence** — new product page header + search result visuals that also surface in the Apple Games app
- **Redesigned system payment sheet (iOS 27)** — works in landscape so players unlock content without leaving the game

## Related sessions to fetch (referenced in this talk)

- [ ] Discover Apple-Hosted Background Assets (WWDC25)
- [ ] Chart your game's course to Apple platforms (Meet with Apple)
- [ ] Plug-in and play: Add Apple frameworks to your Unity game projects (WWDC22)
- [ ] Enhance your presence on the App Store (WWDC26)

## Chapter summary (Summary tab)

- **0:01 Introduction** — Sam (StoreKit team); agenda: Background Assets updates, new Unity plug-ins for In-App Purchase, enhancing App Store / Apple Games presence.
- **0:33 Background Assets** — Managed Background Assets download asset packs only when needed; Apple-hosting up to **200 GB per app** included in the Developer Program; available since the 26 OS releases.
- **1:35 Localized asset packs** — iOS 27; system identifies the player's preferred Settings language and delivers only those assets; fallback to closest match (regional → base → primary app language).
- **3:14 Convert Steam depots to asset packs** — `xcrun ba-package convert` (asset-pack ID, language, download policy) produces a manifest; `ba-package` produces an `.aar` archive; coming to Linux/Windows.
- **4:15 Unity plug-ins** — two new Apple Unity plug-ins (Background Assets, StoreKit) expose C# APIs bridging native frameworks; build/test with Xcode 27, Python 3, Unity 2022 LTS+.
- **5:52 StoreKit and Background Assets sample code** — C# examples: fetch/purchase products, listen for transaction updates, download asset packs on purchase.
- **8:25 Game presence** — new product page header + search result visuals; also appear in the Apple Games app; redesigned landscape-friendly payment sheet in iOS 27.
- **9:10 Next steps** — upload localized asset packs in App Store Connect, adopt the new Unity plug-ins, highlight features with new image/video assets.

## Code

See `code.md` — 7 snippets from the Code tab (JSON manifest, two `ba-package` shell invocations, four C# Unity snippets).
