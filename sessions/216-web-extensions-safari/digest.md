---
title: "Create web extensions for Safari — Full Digest"
session: WWDC26 · 216
url: https://developer.apple.com/videos/play/wwdc2026/216/
duration: 27m
speakers: Kiara
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 216 — Create web extensions for Safari

## TL;DR

A start-to-finish, build-it-live walkthrough of a Safari web extension. Kiara builds
**"Shiny OnTrack,"** a distraction blocker with two modes — a **Light mode** that allows
10 minutes of browsing with an on-page countdown timer, and a **Full mode** that
redirects you away the moment you navigate to a blocked site. Headline themes:

1. **It's just the web platform.** Extensions are HTML, CSS, and JavaScript in a
   Manifest v3 package. The *same project* runs unchanged on Safari across iOS,
   iPadOS, macOS, and visionOS.
2. **Cross-browser by design.** Apple builds to the W3C Web Extensions Working Group
   standard, so extensions written for other browsers port to Safari (and vice versa).
   The talk leans on the standard `browser.*` APIs throughout.
3. **A privacy-first permissions model.** Host access is something the *user* grants
   per site at runtime, not something the extension silently assumes. Optional host
   permissions plus `permissions.request` are the recommended pattern.
4. **You don't need a Mac to ship.** App Store Connect's Safari Web Extension Packager
   builds and packages from any browser; TestFlight handles betas.
5. **Native messaging is the escape hatch.** When the web platform can't do something
   (here, Face ID / Touch ID via Local Authentication), the JS extension messages its
   Swift containing app and gets a result back.

The build is deliberately incremental: each feature adds one manifest key and one small
JS (or Swift) file. The `code.md` companion has every snippet in order.

---

## 1. Introduction & what we're building (0:00)

Safari web extensions are packaged inside a containing app and distributed through the
App Store — ad blockers, custom new-tab pages, playback enhancers. Because they're
plain HTML/CSS/JS, anyone with web-dev experience already knows most of what's needed.
Apple participates in the **W3C Web Extensions Working Group** to standardize the APIs,
so an extension built for another browser can come to Safari.

The running example is a focus tool with **Light mode** (10-minute timer) and **Full
mode** (immediate redirect). The session promises the extension working across iOS,
iPadOS, macOS, and visionOS simultaneously by the end. A sample code project is
available to follow along.

## 2. Get started: manifest + UI (3:23)

Every extension starts with a **`manifest.json`** — the "ID card" that tells the browser
the extension's name, description, version, and what it can do.

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0
}
```

**Icons** go in an `images/` folder and can be supplied as a single **SVG** — Safari
scales it for the toolbar, Extensions Settings, etc.

```json
"icons": { "512": "images/icon.svg" }
```

### Loading an unsigned extension for local development

Safari → Settings (Cmd+Comma) → **Advanced** → check **"Show features for web
developers."** That reveals the Develop menu, where you **Add Temporary Extension**.
Because the code isn't signature-verified, you also **Allow Unsigned Extensions**, then
point Safari at the extension's resource folder.

### UI surfaces: popup vs. options page

Two ways to give the extension UI:

- **Toolbar action button → popup.** Define `action.default_popup`. Clicking the
  toolbar button shows that HTML. Resource filenames are arbitrary as long as they're
  wired to the right manifest key.

  ```json
  "action": { "default_popup": "popup.html" }
  ```

- **Options page.** A full page for settings. Kiara picks this so the blocklist isn't
  cramped, using `options_ui.page` (later snippets switch to the `options_page`
  shorthand).

  ```json
  "options_ui": { "page": "options.html" }
  ```

The page starts as a literal "Hello World" to confirm wiring, then is replaced with the
pre-built Light/Full mode interface.

## 3. Block content with declarativeNetRequest (7:23)

The **`declarativeNetRequest`** API lets an extension **block, modify, or redirect**
network requests — the engine behind ad/tracker blockers. It needs a **permission**
declared in the manifest:

```json
"permissions": [ "declarativeNetRequest" ]
```

A **rule** has an `id`, a `priority`, an `action` type, and a `condition`:

```javascript
{
    id: 1,
    priority: 1,
    action: { type: "block" },
    condition: { urlFilter: "||webkit.org", resourceTypes: [ "main_frame" ] }
}
```

### Static vs. dynamic rules

- **Static rules** live in the manifest (`declarativeNetRequest.rule_resources` →
  `rules.json`). Best when you know the rules ahead of time.
- **Dynamic rules** are added at runtime in JS via
  `browser.declarativeNetRequest.updateDynamicRules({ addRules: [...] })`. Required here
  because the blocked sites aren't known until the user adds them.

The `rules.js` helper hashes a host string to a stable integer `id` and builds a block
rule per host; `host.js`'s `addHost` creates the rule only when the extension is in Full
mode (see `code.md`).

### Redirect rules + a custom page

The default block produces an unpolished error page. A **redirect rule** sends the user
to a designed page instead — same shape, but `action.type: "redirect"` with an
`extensionPath`:

```javascript
action: {
    type: "redirect",
    redirect: { extensionPath: "/blocked.html" }
}
```

### Host permissions and the privacy model

Crucially: **blocking** a request needs no page access, but **redirecting** does. So the
manifest switches from `declarativeNetRequest` to
**`declarativeNetRequestWithHostAccess`**.

Host permissions are an array of **match patterns** (scheme + host + path). Two flavors:

- **`host_permissions`** — declared up front, but **still not granted automatically**;
  the user must approve. Safari badges the action button; clicking it shows an alert to
  grant access; once granted the icon tints to signal the extension is active.
- **`optional_host_permissions`** — request access **at runtime** only when needed, via
  `browser.permissions.request({ origins: [...] })`.

```json
"permissions": [ "declarativeNetRequestWithHostAccess" ],
"optional_host_permissions": [ "*://*/*" ]
```

Because any site can be blocked, the extension uses the all-URLs pattern but only
*requests* a given host when the user adds it. Apple frames this explicitly as a
**privacy decision**: browsing exposes personal data, so the user — not the extension —
decides which sites are accessible.

## 4. Modify webpages: content scripts, storage, background (14:40)

### Content scripts (the on-page countdown timer)

**Content scripts** read and modify page contents. Like rules, they can be **static**
(declared in `content_scripts` with `js`/`css`/`matches`) or **dynamic**.

Dynamic registration uses the **`scripting`** permission and
`browser.scripting.registerContentScripts([...])`. Dynamic scripts add two fields over
static ones: an **`id`** and **`persistAcrossSessions: true`** (survive a Safari
relaunch).

```javascript
let script = {
    id: "id",
    js: [ "content.js" ],
    css: [ "content.css" ],
    matches: [ "*://*.webkit.org/*" ],
    persistAcrossSessions: true
}
await browser.scripting.registerContentScripts([ script ])
```

`addHost` now also registers a per-host content script that renders the 10-minute timer.
In Full mode the redirect fires before the page loads, so it's safe to always register
the script.

### Storage: keeping the blocklist around

Everything so far lived in memory, so reloading the extension wiped the list. The
**`storage`** API fixes this. Two areas:

- **`storage.session`** — in-memory, fine for transient state, gone on restart.
- **`storage.local`** — written to disk; the right choice for the persistent blocklist.

`storage.js` wraps `browser.storage.local` with `updateHosts/getHosts` and
`saveBlockMode/getBlockMode`. Now `addHost` appends to the stored host list, the options
page renders from storage, and switching modes persists the choice (and re-creates
redirect rules for all hosts when entering Full mode).

### Background page / service worker: surviving updates

Registered content scripts persist across **Safari restarts but not across extension
updates** — an update would drop them. The fix is a **background page or service
worker** (Safari supports both; they manage lifecycle, listen for browser events, and
pass messages). Kiara prefers a **background page** because it has DOM access.

```javascript
browser.runtime.onInstalled.addListener(async (details) => {
    if (details.reason !== "update") return
    const hosts = await getHosts()
    await registerScripts(hosts)
})
```

On the `onInstalled` "update" event it re-reads hosts from storage and re-registers the
content scripts.

## 5. Package and distribute (19:53)

**App Store Connect** uploads, submits, and manages extensions — and you **don't need a
Mac**; it works from any browser.

1. Enroll in the **Apple Developer Program** at developer.apple.com.
2. At appstoreconnect.apple.com, create the **containing app** (extensions ship inside
   an app). Choose **platforms** — picking **iOS and macOS** makes it available on
   iPhone, iPad, Mac, and **as a compatible app on Apple Vision Pro**. Set a **bundle
   identifier**.
3. Under the **Xcode Cloud** tab, use the **Safari Web Extension Packager** to upload
   the extension resources; it packages in minutes and surfaces any issues.
4. **TestFlight** distributes beta builds for feedback before submission.
5. The **Distribution** tab takes a screenshot, a description, and a build, then submits
   for review.

## 6. Communicate with your app: native messaging (22:33)

To reach platform features the web can't, the extension talks to its **containing app**.
From here you need **Xcode**. The **Safari Web Extension Packager tool** generates and
launches an Xcode project containing both the app and the extension:

```bash
xcrun safari-web-extension-packager --copy-resources /path/to/ShinyOnTrack
```

**Native messaging** is "three people passing notes": the extension's **JavaScript**
starts it, the **app extension** in the middle relays it, and the **native app** acts
and replies back along the same path.

The goal here: require **biometric authentication** before the blocklist can change.

1. Add the **`nativeMessaging`** permission to the manifest.
2. In the background page, send a message:
   ```javascript
   const response = await browser.runtime.sendNativeMessage({ message: "requestBioAuth" })
   return response?.success
   ```
3. On the native side, edit the generated **`SafariWebExtensionHandler`** (an
   `NSExtensionRequestHandling` class). The template already receives messages; you
   parse for the `requestBioAuth` key, run **Local Authentication**, and reply:
   ```swift
   let success = try await LAContext().evaluatePolicy(
       .deviceOwnerAuthenticationWithBiometrics,
       localizedReason: "Authenticate to change blocked sites"
   )
   ```
   The reply puts `["success": success]` back under `SFExtensionMessageKey` and calls
   `context.completeRequest(...)`.

In the demo, adding webkit.org now prompts for **Touch ID** first; after authenticating,
the site is added. Final distribution is done from Xcode via an archive (bumping the
build number above the App Store Connect build) and the Organizer's distribute flow.

## 7. Next steps (26:04)

Download the sample project, read the **cross-browser WebExtensions docs on MDN**, and
file feedback via **Feedback Assistant** or **bugs.webkit.org** while testing on
**Safari 27**.
