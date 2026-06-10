---
title: "Explore Retention Messaging in App Store Connect — Full Digest"
session: WWDC26 · 309
url: https://developer.apple.com/videos/play/wwdc2026/309/
duration: 15m
speakers: Tori (App Store server team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 309 — Explore Retention Messaging in App Store Connect

## TL;DR

Retention Messaging is a new App Store feature that surfaces a developer-controlled
message — and optionally an image or offer — in the **subscription cancellation flow**,
right when a customer is about to cancel. The goal: remind them of the value or sweeten
the deal so they stay subscribed.

Three headline points:

1. **Two ways to do it.** **Retention Messaging in App Store Connect** is the
   no-server option: configure messages (and Asset Library images / retention offers),
   map them to subscriptions, and the App Store decides what to show. **Real-time
   Retention Messaging** adds a server-to-server API so your endpoint chooses what to
   show *per customer, in real time*.
2. **Four view types.** Message, message + image, message + offer — plus a **switch
   plan** view (real-time only) that proposes a different plan in the same subscription
   group instead of canceling.
3. **It moves the needle.** Apple reports an average save-rate lift of **+1.4 points
   (~82%)**, and **+5.5 points (~223%)** for promotional-offer messages — with the
   caveat of "varying results across developers."

Real-time builds *on top of* the App Store Connect version: you should always
configure App Store Connect messaging as the fallback even if you run a real-time
endpoint. Real-time access requires submitting an interest form.

---

## 1. What Retention Messaging is

When a customer reaches the cancel-confirmation page in Manage Subscriptions, the App
Store already shows the impact of canceling (e.g. "your family will no longer have
access"). Retention Messaging lets you add your own **value-proposition message** at
that exact moment — optionally with an image or an incentive offer (the demo offers
three months free of "Yoga+").

**Save rate** = the percentage of subscribers who keep their subscription after
reaching the cancel-confirmation page. Reported results:

- Adopting Retention Messaging: avg **+1.4 points** save-rate increase (~82% relative).
- Promotional-offer messages: highest observed, **+5.5 points** (~223% relative).
- Apple explicitly notes "varying results across developers" — treat these as
  directional, not guaranteed.

Three views: **message only**, **message + image**, **message + offer**.

## 2. Retention Messaging in App Store Connect (no server required)

Configure retention messages for your app and map them to any of your subscriptions.

- Pick an accompanying image from **Asset Library** (see "Enhance your presence on the
  App Store").
- Attach **retention offers** to incentivize staying.
- Everything is also doable through the **App Store Connect API**.

### The setup flow (demo)

1. On the **Subscriptions** page, a new area prompts you to get started → **Get
   Started** opens a modal to name the message ("Yoga Message").
2. The editor has controls on the left (message text, image, applicable subscriptions,
   offers) and a **live preview** on the right that updates as you type.
3. **Message text is required**; image and offer are **optional**. Text is localizable
   (demo uses English; other localizations selectable).
4. Choose the image from Asset Library.
5. Select which subscription(s) get this message — **one message can map to many
   subscriptions**.
6. Add **offer options**. You can select **multiple offers per subscription**; the App
   Store **automatically picks the best offer** for each eligible customer. When a
   customer is eligible for an offer, **the offer replaces the image** in the view. The
   preview's offer dropdown lets you preview each offer (or "no offer").

### Testing in sandbox + `offerType: 5`

Retention messages are **fully testable in sandbox** by canceling a subscription
there. If you attached a retention offer, you can verify the offer fields land in the
signed transaction / renewal info.

**Retention offers** are a new offer type. The signed transaction and renewal info
carry **`offerType: 5`** to indicate a retention offer was redeemed, alongside the
usual `offerIdentifier`, `offerDiscountType`, and `offerPeriod`.

```json
{
    "bundleId": "com.example.app",
    "productId": "Yoga_summer_2026",
    "transactionReason": "RENEWAL",
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

## 3. Real-time Retention Messaging (your server decides)

If you want to choose what each customer sees in the moment, stand up an **endpoint**
that responds to a **server-to-server HTTP request** from the App Store with your
real-time message preference.

It supports the same three views as App Store Connect, **plus a switch-plan view** —
offer a different plan within the same subscription group (the demo offers an annual
tier of Yoga+) as an alternative to canceling.

### Retention Messaging API

Server-to-server endpoints for managing messages and images, in both sandbox and
production. Base: `https://api.storekit.apple.com/inApps/v1/messaging`.

```http
PUT /realtime/url            GET /realtime/url            DELETE /realtime/url
PUT /message/{id}            DELETE /message/{id}         GET /message/list
PUT /default/{productId}/{locale}   GET /default/...      DELETE /default/...
PUT /image/{id}              DELETE /image/{id}           GET /image/list
POST /performanceTest        GET /performanceTest/result/{requestId}   # sandbox only
```

With it you configure your endpoint URL, set up messages, choose **default messages**
per subscription, and upload/manage images. **Performance testing is sandbox-only** and
**passing a performance test is required before going live in production**.

### The real-time request

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

`originalTransactionId` identifies the subscription, `userLocale` selects the
localization, `requestIdentifier` is for tracking, `environment` is Production or Sandbox.

### Three response formats

**Message** (or message + image) — point at a configured `messageIdentifier`:

```json
{ "message": { "messageIdentifier": "551ee7c0-c097-418e-9dd5-2a98533a7390" } }
```

**alternateProduct** (switch plan) — same subscription group, different `productId`:

```json
{
    "alternateProduct": {
        "messageIdentifier": "ed7f25fc-5741-46a3-8502-062e0fb8afd0",
        "productId": "Yoga_summer_2026_annual"
    }
}
```

**promotionalOffer** — give the customer an offer; a signature is still required:

```json
{
    "promotionalOffer": {
        "messageIdentifier": "80135e2b-ae15-4ec4-8c5c-9ecc8045c0dc",
        "promotionalOfferSignatureV2": "eyJhbGciOiJFUzI…"
    }
}
```

### Fallback chain

Real-time always **prioritizes your real-time response**. If it's unavailable or
malformed, the App Store falls back, in order:

1. Your **App Store Connect** Retention Messaging preference (including eligible offers).
2. If App Store Connect messaging isn't configured for that subscription, the **default
   message** configured via the Retention Messaging API.

Your server may not always respond in time — that's exactly why the fallback exists,
and why the performance test gates production.

### Sandbox → production rollout

1. Set up test messages and images **in sandbox**.
2. Configure your **endpoint** to start receiving requests when a tester cancels.
3. When satisfied, **start a performance test**.
4. On pass, set up messages and images **in production**, then your **production
   endpoint** — now you're live. Keep production messages/images up to date.

### 12-month-commitment switch plan

iOS 26.5 introduced **monthly subscriptions with a 12-month commitment**. The real-time
API supports offering this as a switch plan: include the **`billingPlanType`** field
alongside your `alternateProduct` selection. (See "What's new in Apple In-App
Purchase.")

## 4. Which one to use?

The key difference is **decisioning**:

| | App Store Connect Retention Messaging | Real-time Retention Messaging |
|---|---|---|
| **Who decides** | App Store shows it automatically once configured | You choose per customer, in real time |
| **Configuration** | App Store Connect UI or App Store Connect API | Server-to-server Retention Messaging API only |
| **Offers** | Specific **retention offers** linked to subscriptions | **Promotional offers** chosen in real time (signed) |
| **Views** | message / image / offer (3) | those 3 **+ switch plan** (4) |
| **Server needed** | No | Yes (fast, responsive; must pass perf test) |
| **Access** | Open to all | Interest form required |

Real-time **builds on top of** App Store Connect — you should set up App Store Connect
messaging regardless, so there's a fallback. Pick App Store Connect if you have no
server or want the App Store to decide; pick real-time if you have a server and want
per-customer control and finer offer/eligibility control via promotional offers.
