# Session 278 — Modernize your UIKit app

- **URL:** https://developer.apple.com/videos/play/wwdc2026/278/
- **Duration:** 16m
- **Speakers:** Michael Ochs (engineering manager, UI Frameworks team)

## Description

A modern UIKit app dynamically adjusts to the environment it runs in. iOS 27 makes
iPhone apps fully resizable — in iPhone Mirroring on the Mac and when running on iPad —
and introduces new requirements and APIs to get there. The session walks through the
adaptivity audit (scene lifecycle, main screen references, idiom checks, orientation
checks), new APIs for tab bars / navigation bars / menus, Apple Intelligence support,
and a new Xcode 27 app-modernization skill that lets an agent handle much of the work
automatically.

## Key topics

- **Full resizability is now table stakes** — iPhone apps resize in iPhone Mirroring on Mac and when run on iPad; apps must adapt to any scene size at runtime
- **Scene lifecycle is now mandatory** — apps without a `UISceneDelegate` will no longer launch when built with the latest SDKs
- **Drop `UIScreen.main`** — read the screen from `window.windowScene.screen`; better still, replace screen usage with `traitCollection.displayScale` and the window scene's `effectiveGeometry`
- **Automatic trait tracking** — tracked traits used inside `layoutSubviews`/`updateProperties`/`drawRect` trigger re-invocation automatically; `registerForTraitChanges` for the rest
- **User interface idiom and interface orientation are no longer layout signals** — use size classes (and surrounding view size) instead; an iPhone app on iPad stays in the phone idiom but is fully resizable
- **`UIRequiresFullscreen` repurposed for games** — honored on iPhone in resizable environments, now enabling discrete resizing that respects supported orientations rather than fully opting out
- **CoreMotion / CoreLocation Body protocols** — `UIView` conforms, keeping motion/heading data in the right coordinate space regardless of orientation
- **Tab bars** — iPhone sidebar opt-in via `sidebar.preferredPlacement`; `sidebar.isAvailable`; `prominentTabIdentifier` to pin a tab during scroll collapse
- **Navigation bars** — interactive minimization via `barMinimizationBehavior` / `barMinimizationSafeAreaAdjustment`; new `.automatic` scroll edge effect visuals (re-evaluate `.soft` overrides)
- **Menus** — element images may be hidden by default under Liquid Glass; `preferredImageVisibility` to override
- **Apple Intelligence** — automatic Ask Siri button in menus; View Annotations API to attach `AppEntity` to views; Siri loads resources via drag handlers (move stateful UI out of `sessionWillBegin` into `sessionDidMove`)
- **Agentic modernization skill** — Xcode 27 skill converts `UIScreen.main`, orientation→size-class checks, and migrates to scene lifecycle; export with `xcrun agent skills export`
- **Testing** — Device Hub app and Xcode Previews resize mode; verify on real devices for iPhone Mirroring and iPad

## Related sessions to fetch

- [ ] Make your UIKit app more flexible (WWDC25) — scene lifecycle migration, tab groups
- [ ] Elevate your tab and sidebar experience in iPadOS (WWDC24)
- [ ] Get the most out of Device Hub (WWDC26)
- [ ] Explore advanced App Intents features for Siri and Apple Intelligence (WWDC26)
- [ ] Doc: "Transitioning to the UIKit scene-based lifecycle"
- [ ] Doc: "Automatic trait tracking"
- [ ] Doc: "Adapting your app when traits change"

## Chapter summary (Summary tab)

- **0:00 Introduction** — A modern UIKit app dynamically adjusts to its environment. iOS 27 introduces new requirements and APIs across tab bars, navigation bars, menus, Apple Intelligence, plus an agentic modernization skill.
- **0:34 App adaptivity** — iPhone apps are now fully resizable in iPhone Mirroring on Mac and on iPad. Four areas to audit: scene lifecycle, main screen references, idiom checks, orientation checks.
- **2:10 Legacy API: App lifecycle** — UIScene lifecycle is required when building with the latest SDKs; apps without it won't launch. Migrate from app lifecycle first.
- **2:51 Legacy API: Main screen** — `UIScreen.main` always refers to the device's main screen; in resizable environments your scene may run elsewhere. Use window scene's screen, trait collection for scale, effective geometry for space.
- **5:46 Full-screen mode for games** — `UIRequiresFullscreen` is honored on iPhone in resizable environments, enabling discrete resizing that respects supported orientations so games render at full quality.
- **6:17 Legacy API: User interface idiom** — the idiom trait is no longer meaningful for layout. An iPhone app on iPad stays in phone idiom but is fully resizable. Use size classes.
- **7:06 Legacy API: Interface orientation** — supported orientations are a preference only, ignored in resizable environments (iPhone Mirroring always reports portrait). Replace orientation checks with size classes.
- **7:55 UIView Body protocols for motion & location** — `UIView` conforms to the new CoreMotion/CoreLocation Body protocols, keeping data in the correct coordinate space regardless of orientation.
- **8:19 Test your resizable iPhone app** — Xcode 27's Device Hub app and Previews let you drag simulator edges to test any size. Verify on real devices for iPhone Mirroring and iPad.
- **9:18 Tab bars and sidebars** — opt into sidebar via `sidebar.preferredPlacement = .sidebar`; the system decides when to show it; check `sidebar.isAvailable`; pin a tab with `prominentTabIdentifier`.
- **10:52 Navigation bars** — bars slide away during scroll; override via `barMinimizationBehavior`. The `.automatic` scroll edge effect has new visuals; re-evaluate previous `.soft` overrides.
- **12:37 Menus** — element images may not appear by default in some contexts (menu bars on iPadOS/macOS). Use `preferredImageVisibility` to override.
- **13:01 Integrate with Apple Intelligence** — menus auto-display an Ask Siri button; Siri loads resources from drag handlers; drag sessions can start without a gesture, so avoid animations/modal UI in `sessionWillBegin`.
- **14:07 Agentic coding** — Xcode 27's app modernization skill converts `mainScreen` calls, replaces orientation checks with size class checks, and migrates to scene lifecycle. Skills can be exported for other tools.
- **15:32 Next steps** — build with the iOS 27 SDK, test in Device Hub and iPhone Mirroring on macOS 27, identify flexibility gaps, try the agentic modernization skill.

## Code

See `code.md` — 11 snippets extracted from the Code tab.
