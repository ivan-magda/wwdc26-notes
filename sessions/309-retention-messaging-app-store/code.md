# Code samples — Session 309

Extracted from the Code tab of the session page. These are JSON payloads and an API
endpoint map (this session has no Swift); timestamps are approximate against the
transcript.

## Signed transaction / renewal info — retention offer redeemed (`offerType: 5`)

```json
{
    "bundleId": "com.example.app",
    "productId": "Yoga_summer_2026",
    "type": "Auto-Renewable Subscription",
    "transactionReason": "RENEWAL",
    "inAppOwnershipType": "PURCHASED",
    "quantity": 1,
    "price": 0,
    "currency": "USD",
    "offerType": 5,
    "offerIdentifier": "Yoga_2026_cancel_free_3m",
    "offerDiscountType": "FREE_TRIAL",
    "offerPeriod": "P3M",
    "transactionId": "1000098916194",
    "originalTransactionId": "1000011859217",
    "appAccountToken": "23a91ca7-06f3-425f-bff6-820904b510a9"
}
```

`offerType: 5` marks a **retention offer** redemption. The usual offer fields
(`offerIdentifier`, `offerDiscountType`, `offerPeriod`) appear as expected — here a
3-month free trial (`FREE_TRIAL`, `P3M`).

## Retention Messaging API — endpoint map

Base: `https://api.storekit.apple.com/inApps/v1/messaging`

```http
# URL configuration (your real-time endpoint)
PUT    /realtime/url
GET    /realtime/url
DELETE /realtime/url

# Message configuration
PUT    /message/{messageIdentifier}
DELETE /message/{messageIdentifier}
GET    /message/list
PUT    /default/{productId}/{locale}
DELETE /default/{productId}/{locale}
GET    /default/{productId}/{locale}

# Image configuration
PUT    /image/{imageIdentifier}
DELETE /image/{imageIdentifier}
GET    /image/list

# Performance testing — Sandbox only
POST   /performanceTest                     # initiate test
GET    /performanceTest/result/{requestId}  # get results
```

A passing **performance test** (sandbox only) is required before going live with
real-time Retention Messaging in production.

## Real-time request — what the App Store sends you

```json
{
    "originalTransactionId": "123456789",
    "appAppleId": 6745974591,
    "productId": "Yoga_summer_2026",
    "userLocale": "en-US",
    "requestIdentifier": "c03248af-dd76-4e9b-9c1e-4489cd19a768",
    "environment": "Production",
    "signedDate": 1780920000000
}
```

`environment` is `Production` or `Sandbox`. `originalTransactionId` identifies the
subscription; `userLocale` selects the localization; `requestIdentifier` is for tracking.

## Real-time response — format 1: message (or message + image)

```json
{
    "message": {
        "messageIdentifier": "551ee7c0-c097-418e-9dd5-2a98533a7390"
    }
}
```

Point at a `messageIdentifier` you configured via the API; if that message was paired
with an image, the image shows too.

## Real-time response — format 2: alternateProduct (switch plan)

```json
{
    "alternateProduct": {
        "messageIdentifier": "ed7f25fc-5741-46a3-8502-062e0fb8afd0",
        "productId": "Yoga_summer_2026_annual"
    }
}
```

Offers a different plan in the **same subscription group** (here the annual tier) as
an alternative to canceling. For the monthly-with-12-month-commitment plan type, add a
`billingPlanType` field alongside the `alternateProduct` selection.

## Real-time response — format 3: promotionalOffer

```json
{
    "promotionalOffer": {
        "messageIdentifier": "80135e2b-ae15-4ec4-8c5c-9ecc8045c0dc",
        "promotionalOfferSignatureV2": "eyJhbGciOiJFUzI…"
    }
}
```

Gives the customer an offer. A **signature** (`promotionalOfferSignatureV2`) is still
required for promotional offers used in retention messages.
