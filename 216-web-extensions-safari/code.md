# Code samples — Session 216

Extracted from the Code tab of "Create web extensions for Safari." The extension is
"Shiny OnTrack." The manifest grows incrementally through the talk, so the snippets
below are grouped by the feature each one introduces; later manifests are the
cumulative state at that point.

## Minimal manifest

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0
}
```

## Manifest with icon (SVG, auto-scaled by Safari)

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    }
}
```

## Toolbar action popup vs. options page

```json
// Toolbar action button -> popup
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "action": {
        "default_popup": "popup.html"
    }
}
```

```json
// Full-page options UI (chosen for the blocklist so it isn't cramped)
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_ui": {
        "page": "options.html"
    }
}
```

## Hello World options page

```html
<!DOCTYPE html>
<html>
    <body>
    <p>Hello World</p>
    </body>
</html>
```

## Manifest: declarativeNetRequest permission

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_ui": {
        "page": "options.html"
    },

    "permissions": [ "declarativeNetRequest" ]
}
```

## Manifest: static rule resources

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_ui": {
        "page": "options.html"
    },

    "permissions": [ "declarativeNetRequest" ],

    "declarativeNetRequest": {
        "rule_resources": [
            {
                "id": "ruleset_id",
                "enabled": true,
                "path": "rules.json"
            }
        ]
    }
}
```

## A block rule

```javascript
// block rule
{
    id: 1,
    priority: 1,
    action: {
        type: "block"
    },
    condition: {
        urlFilter: "||webkit.org",
        resourceTypes: [ "main_frame" ]
    }
}
```

## Adding a dynamic rule at runtime

```javascript
await browser.declarativeNetRequest.updateDynamicRules({
    addRules: [ rule ]
})
```

## rules.js — host-to-ID hashing + dynamic block rules

```javascript
// A helper function to map the host to the declarative net request rule ID.
export function hostToRuleID(host) {
	let hash = 0;
	for (let i = 0; i < host.length; i++) {
		hash = ((hash << 5) + hash) + host.charCodeAt(i);
		hash |= 0;
	}
	return Math.abs(hash) || 1;
}

function createBlockRule(host) {
	return {
		id: hostToRuleID(host),
		priority: 1,
		action: {
			type: "block"
		},
		condition: {
			urlFilter: `||${host}`,
			resourceTypes: ["main_frame"]
		}
	}
}

export async function createRules(hosts) {
	try {
		await browser.declarativeNetRequest.updateDynamicRules({
			addRules: hosts.map(createBlockRule)
		})
	} catch {
		console.log("Failed to create declarative net request rules")
	}
}
```

## host.js — create rules only in Full mode (first version)

```javascript
import { createRules, removeAllRules, removeRule } from './rules.js'

export async function addHost(host, blockingMode) {
  if (!host)
    return

  if (blockingMode === "full")
    await createRules([host])
}
```

## A redirect rule

```javascript
{
    id: 1,
    priority: 1,
    action: {
        type: "redirect",
        redirect: {
            extensionPath: "/blocked.html"
        }
    },
    condition: {
        urlFilter: "||webkit.org",
        resourceTypes: [ "main_frame" ]
    }
}
```

## Manifest: host access for redirect + optional host permission for one site

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_ui": {
        "page": "options.html"
    },

    "permissions": [ "declarativeNetRequestWithHostAccess" ],
    "optional_host_permissions": [ "https://webkit.org/*" ]
}
```

## Manifest: optional host permission matching all URLs

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_ui": {
        "page": "options.html"
    },

    "permissions": [ "declarativeNetRequestWithHostAccess" ],
    "optional_host_permissions": [ "*://*/*" ]
}
```

## rules.js — block + redirect rule builders

```javascript
// A helper function to map the host to the declarative net request rule ID.
export function hostToRuleID(host) {
	let hash = 0;
	for (let i = 0; i < host.length; i++) {
		hash = ((hash << 5) + hash) + host.charCodeAt(i);
		hash |= 0;
	}
	return Math.abs(hash) || 1;
}

function createBlockRule(host) {
	return {
		id: hostToRuleID(host),
		priority: 1,
		action: {
			type: "block"
		},
		condition: {
			urlFilter: `||${host}`,
			resourceTypes: ["main_frame"]
		}
	}
}

function createRedirectRule(host) {
	return {
		id: hostToRuleID(host),
		priority: 1,
		action: {
			type: "redirect",
			redirect: { extensionPath: "/blocked.html" }
		},
		condition: {
			urlFilter: `||${host}`,
			resourceTypes: ["main_frame"]
		}
	}
}

export async function createRules(hosts) {
	try {
		await browser.declarativeNetRequest.updateDynamicRules({
			addRules: hosts.map(createRedirectRule)
		})
	} catch {
		console.log("Failed to create declarative net request rules")
	}
}
```

## host.js — request site access at runtime before creating rules

```javascript
import { createRules, removeAllRules, removeRule } from './rules.js'

export async function addHost(host, blockingMode) {
  if (!host)
    return

  const granted = await browser.permissions.request({
    origins: [`*://${host}/*`, `*://*.${host}/*`]
  })
  if (!granted)
    return

  if (blockingMode === "full")
    await createRules([host])
}
```

## Manifest: static content_scripts (the "known sites" approach)

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_ui": {
        "page": "options.html"
    },

    "permissions": [ "declarativeNetRequestWithHostAccess" ],
    "optional_host_permissions": [ "*://*/*" ],

    "content_scripts": [
        {
            "js": [ "content.js" ],
            "css": [ "content.css" ],
            "matches": [ "*://*.webkit.org/*" ]
        }
    ]
}
```

## Registering a content script dynamically

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

## Manifest: scripting permission added

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_page": "options.html",

    "permissions": [
        "declarativeNetRequestWithHostAccess",
        "scripting"
    ],

    "optional_host_permissions": [ "*://*/*" ]
}
```

## scripting.js — register per-host content scripts

```javascript
// scripting.js

function contentScript(host) {
    return {
        id: `cs-${host}`,
        js: [ "content.js" ],
        css: [ "content.css" ],
        matches: [ `*://${host}/*`, `*://*.${host}/*` ],
        persistAcrossSessions: true
    }
}

export function registerScripts(hosts) {
    const scripts = hosts.map(contentScript)
    try {
        await browser.scripting.registerContentScripts(scripts)
    } catch {
        console.log("Failed to register content scripts")
    }
}
```

## host.js — request access, create rules, register scripts

```javascript
// host.js

export async function addHost(host, blockMode) {
    if (!host)
        return

    const granted = await browser.permissions.request({
        origins: [`*://${host}/*`, `*://*.${host}/*`]
    })

    if (!granted)
        return

    if (blockingMode === "full")
        await createRules([ host ])

    await registerScripts([ host ])
}
```

## Storage API — session vs. local

```javascript
await browser.session.storage.set({
  key: value
})

await browser.local.storage.set({
  key: value
})
```

## Manifest: storage permission added

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_page": "options.html",

    "permissions": [
        "declarativeNetRequestWithHostAccess",
        "scripting",
        "storage"
    ],

    "optional_host_permissions": [ "*://*/*" ]
}
```

## storage.js — persist hosts and block mode

```javascript
// storage.js

export async function updateHosts(hosts) {
    await browser.storage.local.set({ hosts: hosts })
}

export async function getHosts() {
    const { hosts = [] } = await browser.storage.local.get("hosts")
    return hosts
}

export async function saveBlockMode(mode) {
    await browser.storage.local.set({ blockMode: mode })
}

export async function getBlockMode() {
    const { blockMode = "full" } = await browser.storage.local.get("blockMode")
    return blockMode
}
```

## host.js — persist the host list after adding

```javascript
// host.js

export async function addHost(host, blockMode) {
    if (!host)
        return

    const granted = await browser.permissions.request({
        origins: [`*://${host}/*`, `*://*.${host}/*`]
    })

    if (!granted)
        return

    if (blockingMode === "full")
        await createRules([ host ])

    await registerScripts([ host ])

    let existingHosts = await getHosts()
    let updatedHosts = [ ...existingHosts, host ]
    await updateHosts(updatedHosts)
}
```

## options.js — render the blocklist from storage

```javascript
// options.js

let existingHosts = await getHosts()
let blockMode = await getBlockMode()

displayBlocklist(existingHosts)
```

## host.js — switching block mode persists and re-applies rules

```javascript
// host.js

export async function userDidSwitchMode(blockMode) {
    await saveBlockMode(blockMode)

    if (blockMode === "full") {
        let hosts = await getHosts()
        await createRules(hosts)
    } else
        await removeAllRules()
}
```

## Manifest: background page (module)

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_page": "options.html",

    "permissions": [
        "declarativeNetRequestWithHostAccess",
        "scripting",
        "storage"
    ],

    "optional_host_permissions": [ "*://*/*" ],

    "background": {
        "scripts": [ "background.js" ],
        "type": "module"
    }
}
```

## background.js — re-register scripts on update (onInstalled)

```javascript
// background.js

import { registerScripts } from "./utilities/scripting.js"
import { getHosts } from "./utilities/storage.js"

browser.runtime.onInstalled.addListener(async (details) => {
    if (details.reason !== "update")
        return

    const hosts = await getHosts()
    await registerScripts(hosts)
})
```

## Package the extension into an Xcode project

```bash
xcrun safari-web-extension-packager --copy-resources /path/to/ShinyOnTrack
```

## Manifest: nativeMessaging permission added

```json
{
    "manifest_version": 3,
    "name": "Shiny OnTrack",
    "description": "Stay on track while you browse the web",
    "version": 1.0,

    "icons": {
        "512": "images/icon.svg"
    },

    "options_page": "options.html",

    "permissions": [
        "declarativeNetRequestWithHostAccess",
        "scripting",
        "storage",
        "nativeMessaging"
    ],

    "optional_host_permissions": [ "*://*/*" ],

    "background": {
        "scripts": [ "background.js" ],
        "type": "module"
    }
}
```

## background.js — send a native message for biometric auth

```javascript
// background.js

import { registerScripts } from "./utilities/scripting.js"
import { getHosts } from "./utilities/storage.js"

browser.runtime.onInstalled.addListener(async (details) => {
    if (details.reason !== "update")
        return

    const hosts = await getHosts()
    await registerScripts(hosts)
})

export async function requestBioAuth() {
    const message = { message: "requestBioAuth" }
    const response = await browser.runtime.sendNativeMessage(message)
    return response?.success
}
```

## SafariWebExtensionHandler.swift — receive the message, run Local Authentication

```swift
// SafariWebExtensionHandler.swift

import LocalAuthentication

class SafariWebExtensionHandler: NSObject, NSExtensionRequestHandling {
    func beginRequest(with context: NSExtensionContext) {
        let request = context.inputItems.first as? NSExtensionItem
        let message = request?.userInfo?[SFExtensionMessageKey] as? [String: Any]

        if message?["message"] as? String == "requestBioAuth" {
            let lAContext = LAContext()
            Task {
                do {
                    let success = try await lAContext.evaluatePolicy(
                        .deviceOwnerAuthenticationWithBiometrics,
                        localizedReason: "Authenticate to change blocked sites"
                    )
                    self.reply(context: context, success: success)
                } catch {
                    self.reply(context: context, success: false)
                }
            }
        }
    }
}
```

## SafariWebExtensionHandler.swift — full handler with reply helper

```swift
// SafariWebExtensionHandler.swift

import LocalAuthentication

class SafariWebExtensionHandler: NSObject, NSExtensionRequestHandling {
    func beginRequest(with context: NSExtensionContext) {
        let request = context.inputItems.first as? NSExtensionItem
        let message = request?.userInfo?[SFExtensionMessageKey] as? [String: Any]

        if message?["message"] as? String == "requestBioAuth" {
            let lAContext = LAContext()
            Task {
                do {
                    let success = try await lAContext.evaluatePolicy(
                        .deviceOwnerAuthenticationWithBiometrics,
                        localizedReason: "Authenticate to change blocked sites"
                    )
                    self.reply(context: context, success: success)
                } catch {
                    self.reply(context: context, success: false)
                }
            }
        }
    }

    private func reply(context: NSExtensionContext, success: Bool) {
        let response = NSExtensionItem()
        response.userInfo = [SFExtensionMessageKey: ["success": success]]
        context.completeRequest(returningItems: [response], completionHandler: nil)
    }
}
```

---

## Notes on the extracted code (verify before reproducing)

- The `storage` snippet shows `browser.session.storage.set` / `browser.local.storage.set`. The standard WebExtensions shape is `browser.storage.session.set` / `browser.storage.local.set` — and `storage.js` later uses the standard `browser.storage.local`. Treat the first form as a slide simplification.
- Several JS helpers are `export function` but use `await` inside without being `async` (`registerScripts` in `scripting.js`) — illustrative, not necessarily compile-clean.
- `addHost` parameters drift between `blockingMode` and `blockMode` across versions; the body sometimes references `blockingMode` while the signature says `blockMode`. Pick one when reproducing.
