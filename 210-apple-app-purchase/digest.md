---
title: "What's new in Apple In-App Purchase — Full Digest"
session: WWDC26 · 210
url: https://developer.apple.com/videos/play/wwdc2026/210/
duration: 13m
speakers: Sam (StoreKit)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 210 — What's new in Apple In-App Purchase

## TL;DR

A pragmatic StoreKit / App Store Connect update with three headline areas:

1. **Monthly subscriptions with a 12-month commitment** (introduced **iOS 26.5**) — let customers pay monthly for an annual subscription. New `pricingTerms` / `billingPlanType` surface across StoreKit, StoreKit views, the Product API, Transaction/RenewalInfo, and the App Store Server APIs. The App Store auto-handles the disclosure sheet and the manage-subscription UI.
2. **Offer code redemption API, modernized** — now returns a `VerificationResult` (like a purchase) and accepts `RedeemOption` values, in both SwiftUI (`.offerCodeRedemption`) and UIKit (`presentOfferCodeRedeemSheet`).
3. **Enhanced submission experience** — group multiple IAP products as review items into a single App Review submission, combine them with in-app events / custom product pages / PPO, and drive it all through an expanding `reviewSubmissions` API (old per-resource submission endpoints are being deprecated).

Also teased: **Bundles and Suites** (cross-app subscription packaging, testable in Xcode 27, program details later in 2026). Running demo app throughout is **SKDemo** / SKDemo+.

---

## 1. Monthly subscriptions with a 12-month commitment

The headline pricing capability. Customers commit to a one-year subscription but pay in
twelve monthly installments instead of one upfront charge. Billing plans attach to **new
or existing** one-year auto-renewable subscriptions, configured in App Store Connect. The
pitch: offering both upfront and monthly reaches a larger base and lets the customer pick.

- Compile against the **26.5 SDK**; customers in available markets can then subscribe in-app on iOS / iPadOS / macOS / tvOS / visionOS **26.4**.
- Set up in App Store Connect: pick a one-year product → "monthly with a 12-month commitment availability" → Set Up Availability → configure the billing plan. Offers (e.g. a free trial) can be scoped **per billing plan type** — the demo adds a free trial only for 12-month-commitment subscribers.

### Merchandising in StoreKit

`pricingTerms` is the new property on `SubscriptionInfo`. It's an array of all available
billing plans for a product. Every auto-renewable subscription has at least one entry with
the default `billingPlanType` of `.upFront`; configuring a commitment plan adds a second
entry with `billingPlanType` of `.monthly` (which only applies to monthly-with-12-month-commitment).

With **StoreKit views**, add the new `.preferredSubscriptionPricingTerms` modifier to an
existing `SubscriptionStoreView` and filter for the `.monthly` plan:

```swift
import StoreKit
import SwiftUI

struct SubscriptionStore: View {
    var body: some View {
        SubscriptionStoreView(groupID: "3F19ED53") {
            // Custom marketing content
        }
        .preferredSubscriptionPricingTerms { _, subscriptionInfo in
            subscriptionInfo.pricingTerms.first {
                $0.billingPlanType == .monthly
            }
        }
    }
}
```

For **custom store UI**, fetch with the Product API, read both the monthly price and the
`totalCommitmentPrice`, and pass the new `.billingPlanType` purchase option:

```swift
let pricingTerms = product?.subscription?.pricingTerms
    .first(where: { $0.billingPlanType == .monthly })
if let pricingTerms {
    let monthlyPrice = pricingTerms.billingDisplayPrice
    let totalCommitmentPrice = pricingTerms.commitmentInfo.price
    // Display both monthly and total commitment price to the customer
}

let result = try? await product?.purchase(options: [.billingPlanType(.monthly)])
```

> Billing plan metadata is only returned when available in the customer's storefront.

### What the App Store handles for you

- **Disclosure sheet** before a customer's first commitment purchase — auto-presented, shown once per Apple Account, covering number of payments and cancellation guidance.
- **Management UI** for active commitment subs — available plans, remaining payments, and the commitment renewal date. Present it via `.manageSubscriptionsSheet` (SwiftUI) or `showManageSubscriptions` (UIKit).

### Reading commitment state on-device

New fields on `Transaction` and `RenewalInfo`, available starting **OS 26.4**:

- `Transaction.commitmentInfo` is **nil** for `.upFront`; for `.monthly` it returns commitment progress, price, and expiration. Always read from the **latest** transaction for an accurate `expirationDate`. Useful for entitlement checks or a custom commitment-progress indicator.
- `RenewalInfo` exposes `renewalBillingPlanType` and `commitmentInfo` describing the overall commitment renewal.

### Testing

Xcode **26.5** StoreKit Testing: open the StoreKit config, pick a one-year auto-renewable
subscription, use the new **Billing Plan picker** → Monthly with a 12-month commitment.
Offers can be created per billing plan type. Verify `commitmentInfo` in the Transaction
inspector.

## 2. Server-side: App Store Server APIs & Notifications V2

New fields in the signed transaction and renewal info objects describe the commitment plan.
Subscription notifications keep flowing across monthly renewals throughout the 12 months.
The **Retention Messaging API** supports the new payment option too.

Decoded `JWSTransaction` for one billing period of a commitment:

```json
{
    "expiresDate": 1783503660000,
    "price": 10990,
    "productId": "plus.pro.annual",
    "billingPlanType": "MONTHLY",
    "commitmentInfo": {
        "billingPeriodNumber": 1,
        "totalBillingPeriods": 12,
        "commitmentExpiresDate": 1812447660000,
        "commitmentPrice": 131880
    }
}
```

Decoded `JWSRenewalInfo` — present **only while the subscription is in a commitment**, and
reflecting the customer's preferences for after it ends (here they switched to a different
`BILLED_UPFRONT` plan):

```json
{
    "renewalBillingPlanType": "MONTHLY",
    "commitmentInfo": {
        "commitmentAutoRenewProductId": "plus.standard.annual",
        "commitmentAutoRenewStatus": 0,
        "commitmentRenewalDate": 1812447660000,
        "commitmentRenewalPrice": 10990,
        "commitmentRenewalBillingPlanType": "BILLED_UPFRONT"
    }
}
```

Deep dive on lifecycle handling: developer docs *"Managing the life cycle of monthly
subscriptions with a 12-month commitment."*

## 3. Bundles and Suites

Another way to package subscription value across apps:

- **Bundle** — a group of subscriptions that can each be bought individually, but are sold together in a single purchase at a better price than buying them separately.
- **Suite** — a group of subscriptions that **only exist in the context of the Suite** (not purchasable individually), typically serving a related set of apps.

Testable via the API in **Xcode 27**; more program details "coming later in 2026."

## 4. Offer code redemption API

Offer codes apply to consumable, non-consumable, auto-renewable, or non-renewing products
and grant free or discounted IAPs for a set duration. The redemption API is modernized to
mirror purchases:

- Now returns a **`VerificationResult`** on completion (transaction on success, descriptive error on failure).
- Accepts a set of **`RedeemOption`** values to configure redemption.
- SwiftUI: `.offerCodeRedemption(options:isPresented:) { result in … }`. UIKit: `presentOfferCodeRedeemSheet`.
- Testable in Xcode 27 across all applicable product types.

```swift
.offerCodeRedemption(options: [], isPresented: $presentingOfferCodeSheet) { result in
    switch result {
    case .success(let verificationResult):
        switch verificationResult {
            // Verify transaction, grant access, finish transaction
        }
    case .failure(let error):
        // Handle error
    }
}
```

> Action item from the talk: **update offer code redemption call sites** to the extended API.

## 5. Enhanced submission experience

When submitting to the App Store, IAP products can now be grouped as **review items** into a
single App Review submission and combined with other review-item types: in-app events,
custom product pages, and product page optimizations. After submission, a centralized view
shows App Review status for all items, unifying the workflow.

- App Store Connect website: use the **Add for Review** drop-down to add a product to an in-draft submission; multi-select a group of products and add them to an in-progress submission at once.
- **App Store Connect API:** `reviewSubmissions` is expanding to support In-App Purchase, subscription, and subscription group resources — automate all review items through one interface.
- The existing per-resource submission endpoints (In-App Purchase / subscription / subscription group) are being **deprecated** in favor of `reviewSubmission` + `reviewSubmissionItems`. Start migrating now.

## Next steps (from the talk)

- Add billing plans for monthly-with-12-month-commitment on annual products.
- Update offer code redemption call sites to the extended API.
- Test in **Xcode 27** and sandbox.
- Submit IAP products through the enhanced App Review experience.
