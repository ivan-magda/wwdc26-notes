# Session 210 — What's new in Apple In-App Purchase

- **URL:** https://developer.apple.com/videos/play/wwdc2026/210/
- **Duration:** 13m
- **Speakers:** Sam (StoreKit engineer)

## Description

Learn how to merchandise products and grow your business with expanded subscription
pricing options, updates to the offer code redemption API, and an enhanced App Store
Connect submission experience. The session walks through monthly subscriptions with a
12-month commitment, the new `pricingTerms`/`billingPlanType` StoreKit surface,
server-side commitment fields, Bundles and Suites, the verification-result-based offer
code redemption API, and unified review-item submissions.

## Key topics

- **Monthly subscriptions with a 12-month commitment** (introduced iOS 26.5) — pay monthly for an annual subscription; added to new or existing one-year auto-renewable subs in App Store Connect.
- **StoreKit merchandising** — new `pricingTerms` on `SubscriptionInfo`/`Product.subscription`; `billingPlanType` of `.upFront` (default) vs `.monthly`; `.preferredSubscriptionPricingTerms` view modifier; `.billingPlanType` purchase option; `commitmentInfo` `totalCommitmentPrice`.
- **Disclosure + management UI** — App Store auto-presents a commitment disclosure sheet once per Apple Account; `.manageSubscriptionsSheet` (SwiftUI) / `showManageSubscriptions` (UIKit).
- **Transaction / RenewalInfo fields** — `commitmentInfo` (nil for `.upFront`), `renewalBillingPlanType`; new fields available starting OS 26.4.
- **App Store Server APIs / Notifications V2** — new `commitmentInfo` fields in JWSTransaction and JWSRenewalInfo; Retention Messaging API supports the new plan.
- **Bundles and Suites** — Bundle = subs sold together at a better price; Suite = subs that only exist inside the suite; testable in Xcode 27, program details later in 2026.
- **Offer code redemption API** — now returns a `VerificationResult`, accepts `RedeemOption` values; `.offerCodeRedemption(options:isPresented:)` (SwiftUI) / `presentOfferCodeRedeemSheet` (UIKit).
- **Enhanced submission experience** — group IAP products as review items into a single App Review submission alongside in-app events / custom product pages / PPO; `reviewSubmissions` API expanding; old IAP/subscription/subscription-group resources deprecated.

## Related sessions to fetch

- [ ] Explore Retention Messaging in App Store Connect (WWDC26)
- [ ] Meet StoreKit for SwiftUI (WWDC23)
- [ ] What's new in StoreKit 2 and StoreKit Testing in Xcode (WWDC23)
- [ ] Implement App Store Offers (WWDC24)
- [ ] What's New in App Store Connect (WWDC22)

## Chapter summary (Summary tab)

- **0:01 Introduction** — Sam (StoreKit). Agenda: expanded subscription pricing, offer code redemption updates, enhanced App Store Connect submission.
- **0:51 Monthly subscriptions with a 12-month commitment** — new pricing option to pay monthly for an annual subscription; added to new or existing one-year subs to reach a wider base (introduced iOS 26.5).
- **1:42 Set up in App Store Connect** — configure the billing plan, pricing, offers, and availability on a one-year subscription; offers (e.g. free trial) can be scoped per billing plan type.
- **2:28 Merchandise with StoreKit** — `pricingTerms`/`billingPlanType`, `.preferredSubscriptionPricingTerms`, Product API + `totalCommitmentPrice`, `.billingPlanType` purchase option, disclosure + manage sheets; test with StoreKit Testing in Xcode 26.5.
- **6:55 Monitor subscriptions with App Store Server APIs** — new `commitmentInfo` fields in JWSTransaction/JWSRenewalInfo; notifications continue through the commitment; Retention Messaging support.
- **8:50 Bundles and Suites** — another way to provide more value across apps; testable in Xcode 27, details later in 2026.
- **9:26 Offer code redemption** — extended API takes `RedeemOption` values and returns a `VerificationResult`.
- **10:35 Enhanced submission experience** — group products as review items into one submission, combine with other review-item types, centralized status; `reviewSubmissions` API expanding, old resources deprecated.
- **12:38 Next steps** — adopt expanded pricing, update offer code call sites, test in Xcode 27 + sandbox, submit through the enhanced App Review experience.

## Code

See `code.md` — 6 snippets extracted from the Code tab.
