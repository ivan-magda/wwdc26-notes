# Code samples — Session 378

Extracted from the session's Code tab. Three platform/shell snippets
and four C# Unity snippets.

## Localized asset pack manifest (JSON)

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

## Convert a Steam depot to an asset pack manifest

```bash
# Convert a Steam depot to an asset pack manifest
xcrun ba-package convert --asset-pack-id voice-english -l en-US --on-demand voice-english.vdf -o voice-english.json
```

## Convert a manifest to an asset pack archive

```bash
# Convert an asset pack manifest to an asset pack archive
xcrun ba-package voice-english.json -o voice-english.aar
```

## Fetch products with the StoreKit Unity plug-in (C#)

```csharp
// Fetch and purchase products with the StoreKit plug-in

using UnityEngine;
using Apple.StoreKit;

async void Start() {
    var products = await Product.FetchProducts(new[] {
            "com.thecoast.capecod"
    });
}
```

## Purchase a product and grant access (C#)

```csharp
// Fetch and purchase products with the StoreKit plug-in

using UnityEngine;
using Apple.StoreKit;

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

## Listen for transaction updates (C#)

```csharp
// Listen for Transaction updates with the StoreKit plug-in

using UnityEngine;
using Apple.StoreKit;

public static class TransactionListener {
    public static void Initialize() => Transaction.Updates += OnUpdate;

    async void OnUpdate(VerificationResult<Transaction> result) {
        if (!result.IsVerified) return;
        var verifiedTransaction = result.SafePayload;

        // Consumables are not in CurrentEntitlements, so handle them inline
        if (verifiedTransaction.ProductType == ProductType.ProductTypeEnum.Consumable) {
            if (verifiedTransaction.RevocationDate != null) {
                // Revoke the consumable identified by verifiedTransaction.ProductId
            } else {
                // Grant access to the consumable
            }
        } else {
            // Non-consumables and subscriptions: re-read CurrentEntitlements as source of truth
            await foreach (var verificationResult in Transaction.GetCurrentEntitlements()) {
                if (!verificationResult.IsVerified) continue;
                // Grant access to the product
            }
        }
        verifiedTransaction.Finish();
    }
}
```

## Download asset packs with the Background Assets Unity plug-in (C#)

```csharp
// Download asset packs with the Background Assets plug-in

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

---

## Useful API facts surfaced by the code

- StoreKit Unity namespace: `Apple.StoreKit`; Background Assets: `Apple.BackgroundAssets`.
- Purchase path: `product.Purchase()` → `PurchaseResult` with `.Result == PurchaseResult.ResultEnum.Success`; verify via `.TransactionVerification.IsVerified`, unwrap with `.SafePayload`, then `.Finish()`.
- `Transaction.Updates += OnUpdate` delivers `VerificationResult<Transaction>`; consumables (`ProductType.ProductTypeEnum.Consumable`) are **not** in `CurrentEntitlements` and must be handled inline (check `RevocationDate`).
- `Transaction.GetCurrentEntitlements()` is the source of truth for non-consumables/subscriptions (already filters refunded/revoked/expired).
- Background Assets flow: `AssetPackManager.GetManifestAsync()` → `manifest.GetAssetPack(id)` → `EnsureLocalAvailabilityOfAssetPackAsync(assetPack)`; progress via `DownloadStatusUpdatesAsync(id)`.
- `ba-package convert` flags seen: `--asset-pack-id`, `-l <lang>`, `--on-demand` (the download policy); input `.vdf`, output `.json`; then `ba-package <manifest>.json -o <name>.aar`.
- Manifest keys: `assetPackID`, `downloadPolicy`, `language`, `sourceRoot`, `fileSelectors`, `platforms`.
