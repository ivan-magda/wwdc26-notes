# Session 216 — Create web extensions for Safari

- **URL:** https://developer.apple.com/videos/play/wwdc2026/216/
- **Duration:** 27m
- **Speakers:** Kiara (engineer, Safari team)

## Description

Learn how Safari web extensions — built with HTML, CSS, and JavaScript and packaged
inside an app — can run across iOS, iPadOS, macOS, and visionOS from a single
unchanged project. The session builds a "Shiny OnTrack" distraction-blocker extension
from scratch: manifest, popup/options UI, content blocking with declarativeNetRequest,
on-page content scripts, persistent storage, App Store Connect distribution, and
native messaging to reach platform features like Local Authentication.

## Key topics

- Manifest v3 basics — `manifest.json` as the extension's "ID card" (name, description, version, icons)
- UI surfaces — toolbar action `default_popup` vs. a full `options_ui` / `options_page`
- Loading an unsigned extension in Safari via the web-developer Advanced settings pane
- Content blocking with `declarativeNetRequest` — static (manifest) vs. dynamic (`updateDynamicRules`) rules; block vs. redirect actions; `extensionPath`
- Permissions & privacy model — `declarativeNetRequest` vs. `declarativeNetRequestWithHostAccess`; host permissions vs. `optional_host_permissions`; runtime `permissions.request`; action-button badge + tint
- Content scripts — static in manifest vs. dynamic `scripting.registerContentScripts` (with `id` + `persistAcrossSessions`)
- Storage API — `session` (in-memory) vs. `local` (on-disk) storage areas
- Background page / service worker — lifecycle, `runtime.onInstalled` to re-register scripts after an update
- Packaging & distribution — App Store Connect Safari Web Extension Packager (no Mac needed), TestFlight betas, App Store submission
- Native messaging — `xcrun safari-web-extension-packager`, `runtime.sendNativeMessage`, `SafariWebExtensionHandler` Swift class, Local Authentication for biometric gating
- Cross-browser standardization via the W3C Web Extensions Working Group

## Related sessions to fetch (referenced in this talk)

- [ ] None named directly. Pointers go to external docs: cross-browser WebExtensions documentation on MDN, Feedback Assistant, bugs.webkit.org.

## Chapter summary (Summary tab)

- **0:00 Introduction** — Safari web extensions are HTML/CSS/JS packaged in an app; preview the distraction-blocker with a 10-minute Light mode and a redirect Full mode; runs across iOS, iPadOS, macOS, visionOS.
- **3:23 Get started** — write a `manifest.json` from scratch, add a popup UI so the extension is reachable from Safari's toolbar; same project runs unchanged on every Apple platform.
- **7:23 Block content** — use `declarativeNetRequest` to block, modify, and redirect requests; declare host permissions, including optional host permissions, so users grant per-site access.
- **14:40 Modify webpages** — inject a countdown timer with content scripts; register scripts dynamically via the `scripting` API; persist preferences and per-host state with the `storage` API and a background service worker.
- **19:53 Package and distribute** — submit via App Store Connect; share beta builds through TestFlight.
- **22:33 Communicate with your app** — generate an Xcode project with the Safari Web Extension Packager, then use native messaging to pass requests between the JS extension and its containing app, unlocking platform features (Local Authentication) unavailable to web APIs.
- **26:04 Next steps** — download the sample project; explore cross-browser WebExtensions docs on MDN; file feedback via Feedback Assistant or bugs.webkit.org.

## Code

See `code.md` — all manifest, JavaScript, shell, and Swift snippets extracted from the Code tab.
