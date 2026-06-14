---
title: "Unlock in-game content with StoreKit and Background Assets — Full Digest"
session: WWDC26 · 378
url: https://developer.apple.com/videos/play/wwdc2026/378/
duration: 10m
speakers: Sam (StoreKit team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 378 — Unlock in-game content with StoreKit and Background Assets

## TL;DR

A 10-minute games-focused session from the StoreKit team, structured in three acts:

1. **Background Assets gets bigger and smarter.** Managed Background Assets download
   asset packs only when needed; Apple now hosts up to **200 GB per app**, included in
   the Developer Program. In **iOS 27**, **localized asset packs** ship only the
   player's preferred-language assets, with a sensible fallback chain. For
   multilingual games, that cuts download and storage size. And `xcrun ba-package` can now
   **convert Steam depots** into asset pack manifests/archives.
2. **Two new Apple Unity plug-ins: Background Assets and StoreKit.** They expose C# APIs
   that bridge to the native frameworks, so Unity games can do In-App Purchase and
   on-demand asset delivery with the platform-native flows. Build with **Xcode 27,
   Python 3, Unity 2022 LTS+**.
3. **Ship and stand out.** Local testing via **StoreKit Testing in Xcode** + a new
   **Background Assets mock server**, then new **product page / search visuals** that
   also surface in the **Apple Games app**, plus a **redesigned landscape-friendly
   payment sheet** in iOS 27.

The running example is a game called "The Coast," packed with audio, video, textures,
and ML models needed only at specific moments.

---

## 1. Managed Background Assets

The pitch: a content-heavy game (audio, video, textures, ML models) shouldn't force
players to download everything upfront. **Managed Background Assets** lets the system
download asset packs automatically *when they're needed*, saving time and storage.

- For App Store apps, **Apple can host up to 200 GB of assets per app**, included in the
  Developer Program membership.
- **Apple-Hosted Background Assets** is available starting with **iOS, iPadOS, macOS,
  tvOS, and visionOS 26**.
- Setup/API deep dive: **"Discover Apple-Hosted Background Assets" (WWDC25)**.

## 2. Localized asset packs (iOS 27)

New in **iOS 27**: the system reads the player's preferred language (set in Settings)
and delivers only the asset packs for that language.

Fallback chain demonstrated with "The Coast" (primary English; French + German also
provided):

- Player sets **German** → only German asset packs install.
- Player sets **English-UK** (not provided) → falls back to the **base language of the
  preferred language**, i.e. **English-US**.
- Player sets **Spanish** (not provided, no regional variant) → falls back to the
  **primary app language** (English).

To adopt, **add a `language` tag to your asset pack manifest JSON**:

```json
{
   "assetPackID": "voice-english",
   "downloadPolicy": { /* … */ },
   "language": "en-US",
   "sourceRoot": ".",
   "fileSelectors": [ /* … */ ],
   "platforms": [ /* … */ ]
   //…
}
```

## 3. Convert Steam depots to asset packs

If your game already manages assets as **Steam depots**, you can convert them to asset
packs for Apple platforms with the `ba-package` tool (ships in **Xcode 27**, on macOS;
coming to **Linux and Windows**).

Step 1 — depot → manifest. Three arguments: asset-pack ID, language (if applicable),
and download policy (here `--on-demand`):

```bash
xcrun ba-package convert --asset-pack-id voice-english -l en-US --on-demand voice-english.vdf -o voice-english.json
```

Step 2 — manifest → archive (`.aar`):

```bash
xcrun ba-package voice-english.json -o voice-english.aar
```

The resulting archive is ready to ship with the game.

## 4. New Apple Unity plug-ins

Two plug-ins join the Apple Unity plug-in portfolio: **Background Assets** and
**StoreKit**. Both expose a **C# Unity API that bridges to the underlying native
framework**.

- Available now on GitHub (link in the session resources), alongside Apple's existing
  Unity plug-ins; the repo has build/install instructions.
- Build them with the **same Python script** used for the other Apple Unity plug-ins.
- Toolchain: **Xcode 27, Python 3, Unity 2022 LTS or later**.
- Setup/config context: **"Chart your game's course to Apple platforms"** (Meet with
  Apple) and **"Plug-in and play: Add Apple frameworks to your Unity game projects"**
  (WWDC22).

## 5. StoreKit in C# — fetch, purchase, listen

**Fetch / merchandise** products with the Product API:

```csharp
using UnityEngine;
using Apple.StoreKit;

async void Start() {
    var products = await Product.FetchProducts(new[] {
            "com.thecoast.capecod"
    });
}
```

**Purchase** to display the system payment sheet, verify, deliver content, then
`Finish()`:

```csharp
async void Purchase(Product product) {
    var result = await product.Purchase();
    if (result.Result == PurchaseResult.ResultEnum.Success
        && result.TransactionVerification.IsVerified)
    {
        // Unlock access to purchased content
        result.TransactionVerification.SafePayload.Finish();
    }
}
```

**Listen** for transactions created/updated outside the app or on other devices with
`Transaction.Updates`. Key nuance: **consumables are not in `CurrentEntitlements`**, so
handle them inline (checking `RevocationDate`); for non-consumables and subscriptions,
re-read `CurrentEntitlements` as the source of truth (it already filters refunded /
revoked / expired):

```csharp
public static class TransactionListener {
    public static void Initialize() => Transaction.Updates += OnUpdate;

    async void OnUpdate(VerificationResult<Transaction> result) {
        if (!result.IsVerified) return;
        var verifiedTransaction = result.SafePayload;

        if (verifiedTransaction.ProductType == ProductType.ProductTypeEnum.Consumable) {
            if (verifiedTransaction.RevocationDate != null) {
                // Revoke the consumable identified by verifiedTransaction.ProductId
            } else {
                // Grant access to the consumable
            }
        } else {
            await foreach (var verificationResult in Transaction.GetCurrentEntitlements()) {
                if (!verificationResult.IsVerified) continue;
                // Grant access to the product
            }
        }
        verifiedTransaction.Finish();
    }
}
```

## 6. Downloading content on purchase (Background Assets in C#)

When a player buys content, use the Background Assets plug-in to **ensure the asset pack
is locally available** before serving it, and surface download progress via
`DownloadStatusUpdatesAsync`:

```csharp
using Apple.BackgroundAssets;
using UnityEngine;

async void LoadTutorial(string language) {
    try {
        string assetPackId = $"tutorial-{language}";
        AssetPackManifest manifest = await AssetPackManager.GetManifestAsync();
        AssetPack assetPack = manifest.GetAssetPack(assetPackId);
        CancellationTokenSource tokenSource = new CancellationTokenSource();
        _ = Task.Run(async () => {
            await foreach (AssetPackManager.DownloadStatusUpdate statusUpdate in AssetPackManager.DownloadStatusUpdatesAsync(assetPackId)) {
                // Update download progress in UI
            }
        }, tokenSource.Token);
        await AssetPackManager.EnsureLocalAvailabilityOfAssetPackAsync(assetPack);
        tokenSource.Cancel();
        // Start tutorial with the locally available assets
    } catch (Exception exception) {
        // Handle the exception
    }
}
```

## 7. Testing

Export the Unity project to Xcode and test before shipping:

- **StoreKit Testing in Xcode** — create a **StoreKit configuration file**, add test
  products, then edit the target's scheme → **Run** → pick the config file in the
  drop-down. The same scheme screen lets you select the **folder of packaged asset
  packs** for the mock server to serve.
- **Background Assets mock server** — when you run in **Xcode 27**, it **automatically
  starts and attaches to the debug session** to serve assets locally.
- **Sandbox testing** — also available, exercising the real user experience against
  products set up in **App Store Connect**.

## 8. Game presence & the payment sheet

- Add new visuals to the **product page header** and **search results** to stand out;
  those images/videos also appear in the **Apple Games app**.
- Configuration deep dive: **"Enhance your presence on the App Store" (WWDC26)**.
- **iOS 27 redesigned system payment sheet** works in **landscape**, so players can
  unlock content without breaking out of the game.

## 9. Next steps

- Upload **localized asset packs** in App Store Connect to further reduce app size.
- Adopt the new **Background Assets** and **StoreKit** Unity plug-ins.
- Plan **new image/video assets** for the App Store and Apple Games app.
