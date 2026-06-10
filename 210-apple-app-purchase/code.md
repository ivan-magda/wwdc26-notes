# Code samples — Session 210

Six snippets from the Code tab. Timestamps map to the transcript chapters.

## Merchandise pricing terms with StoreKit views

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

## Get pricing terms and make a billing-plan purchase (custom UI)

```swift
import StoreKit

var product: Product?
// Fetch and assign product

// Get the monthly billing plan's pricing terms for merchandising
let pricingTerms = product?.subscription?.pricingTerms
    .first(where: { $0.billingPlanType == .monthly })
if let pricingTerms {
    let monthlyPrice = pricingTerms.billingDisplayPrice
    let totalCommitmentPrice = pricingTerms.commitmentInfo.price
    // Display both monthly and total commitment price to the customer
}

let result = try? await product?.purchase(options: [.billingPlanType(.monthly)])
switch result {
    // Verify the transaction, give the customer access to
    // the purchased content, and then finish the transaction
}
```

## Present the manage-subscriptions sheet by group ID

```swift
import SwiftUI
import StoreKit

struct ManageSubscriptionsButton: View {
    let subscriptionGroupID: String
    @State var presentingManageSubscriptionsSheet: Bool = false

    var body: some View {
        Button("Manage Subscriptions") {
            presentingManageSubscriptionsSheet = true
        }
        .manageSubscriptionsSheet(
            isPresented: $presentingManageSubscriptionsSheet,
            subscriptionGroupID: subscriptionGroupID
        )
    }
}
```

## Decoded JWSTransaction — one billing period of a 12-month commitment

```json
{
    // …
    "expiresDate": 1783503660000, // for this billing period
    "price": 10990,               // for this billing period
    "productId": "plus.pro.annual",
    "purchaseDate": 1780911660000,
    "type": "Auto-Renewable Subscription",
    "billingPlanType": "MONTHLY",
    "commitmentInfo": {
        "billingPeriodNumber": 1,
        "totalBillingPeriods": 12,
        "commitmentExpiresDate": 1812447660000,
        "commitmentPrice": 131880
    }
}
```

## Decoded JWSRenewalInfo — renewal preferences after the commitment

```json
{
    // …
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

> Note: the extracted snippet used curly/smart quotes (`“plus.standard.annual”`) around
> `commitmentAutoRenewProductId` — straightened to plain `"` here. Verify against the
> real payload schema before quoting.

## Redeem an offer code (returns a VerificationResult)

```swift
struct OfferCodeRedemption: View {
    @State var presentingOfferCodeSheet: Bool = false

    var body: some View {
        Button("Redeem Offer Code") {
            presentingOfferCodeSheet = true
        }
        .offerCodeRedemption(options: [], isPresented: $presentingOfferCodeSheet) { result in
            switch result {
            case .success(let verificationResult):
                switch verificationResult {
                    // Verify the transaction, give the customer access to
                    // the purchased content, and then finish the transaction
                }
            case .failure(let error):
                // Handle error
            }
        }
    }
}
```

---

## API facts surfaced by the code + transcript

- `SubscriptionInfo.pricingTerms` / `Product.subscription?.pricingTerms` — array of billing plans; at least one with default `billingPlanType == .upFront`.
- `billingPlanType`: `.upFront` (default) vs `.monthly` (only for monthly-with-12-month-commitment).
- View modifier `.preferredSubscriptionPricingTerms { _, subscriptionInfo in … }` on `SubscriptionStoreView`.
- Purchase option `.billingPlanType(.monthly)`.
- Custom-UI pricing fields: `pricingTerms.billingDisplayPrice` (monthly) and `pricingTerms.commitmentInfo.price` (= `totalCommitmentPrice`).
- Manage UI: `.manageSubscriptionsSheet(isPresented:subscriptionGroupID:)` (SwiftUI) / `showManageSubscriptions` (UIKit).
- Server `commitmentInfo` (transaction): `billingPeriodNumber`, `totalBillingPeriods`, `commitmentExpiresDate`, `commitmentPrice`.
- Server `commitmentInfo` (renewal): `commitmentAutoRenewProductId`, `commitmentAutoRenewStatus`, `commitmentRenewalDate`, `commitmentRenewalPrice`, `commitmentRenewalBillingPlanType`; plus `renewalBillingPlanType`.
- Offer code: `.offerCodeRedemption(options:isPresented:)` (SwiftUI) / `presentOfferCodeRedeemSheet` (UIKit); takes `[RedeemOption]`, returns `Result<VerificationResult, Error>`.
