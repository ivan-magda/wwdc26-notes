# Code samples — Session 391

_No Code tab for this session._

This is a conceptual/configuration talk. The walkthroughs are in App Store
Connect (availability toggles, volume pricing bands), Apple Business / School
Manager, and StoreKit 2 purchase flows — no source snippets were shown.

## Spoken APIs / surfaces named in the talk

- **StoreKit 2** purchase request — pass the **requested seat count** for in-app group purchases (required; StoreKit 2 is a prerequisite for the whole feature).
- **App Store Server API — custom invitation flow endpoints** — for apps that want to integrate their own invitation/member management instead of the included system.
- **App Store Server API — Group management endpoints** — query all groups a single customer is in, and all members in a group; supported for volume purchasing and group purchases that use the included seat management flows.
- **App Store Connect** — per-subscription availability config (on / School-Manager-only / off) and **volume pricing** (up to 5 price bands).
