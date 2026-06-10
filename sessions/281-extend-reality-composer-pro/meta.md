# Session 281 — Extend Reality Composer Pro 3 functionality with Xcode

- **URL:** https://developer.apple.com/videos/play/wwdc2026/281/
- **Duration:** 22m
- **Speakers:** Niklas
- **Availability note:** Niklas states up front this is "a feature that will be available later this year."

## Description

Extend Reality Composer Pro 3 with plug-ins written in Xcode so artists and content
creators can work with project-specific data directly in the editor. The session walks
through the plug-in mechanism (a shared git repo, a dynamic library built from custom
Swift, and a trust-on-load model), then shows four extension points in a "Chaparral
Village" game: custom **components**, custom **systems**, custom **animation actions**
on the sequencer timeline, and custom **Script Graph nodes** exposed via the
`@Scriptable` macro. The running example is a cauldron whose water level and vortex
swirl are tuned live in the editor.

## Key topics

- Plug-in architecture: one git repo shared by the Reality Composer Pro project and the Xcode project; custom Swift compiled into a dynamic library (`RCPCustomComponents.framework`); editor loads and **trusts** plug-ins at runtime
- Two Xcode schemes share the same component/system code: `ChaparralVillage` (the app) and `RCPCustomComponents` (the editor plug-in)
- Data flow: editor saves to JSON on disk (custom merge tool for fewer git conflicts) → exported as a **Reality File** for the app to load
- Custom **components** (`Component` + `Codable`; optional `CodingKeys` to hide runtime-only props) and custom **systems** that run *inside the editor* for live iteration — no build-and-deploy loop
- `RealityComposerProPlugin` protocol + `@_cdecl("createRealityComposerProPlugin")` C-exported entry point returning a raw pointer for the DLL interface; `context.register…` calls in `setup`
- Driving a **ShaderGraphMaterial** from a component (set named parameters like Level Radius, Lowest Point, etc.) for the vortex effect
- Debugging the editor: set a breakpoint, attach Xcode to the editor application
- Custom **animation actions** via `EntityAction` + `Codable`; `animatedValueType` returns `Transform.self`; static `subscribe()` hooks `.updated` events to interpolate over normalized time; drag onto sequencer timeline
- Custom **Script Graph nodes** via the `@Scriptable` macro (from `RealityKitScripting` / `RealityKitScriptingMacros`); register an `RKS.Configuration` + `Module` holding the generated `SchemaProvider.schema` on the main thread
- Rebuilding the plug-in requires restarting Reality Composer Pro (re-trust + a diff dialog of component changes)

## Related sessions to fetch (referenced in this talk)

- [ ] Iterate your spatial scenes faster with Reality Composer Pro 3
- [ ] Design no-code games with Reality Composer Pro 3
- [ ] Explore advances in RealityKit
- [ ] Supercharge your spatial workflows with Reality Composer Pro 3

## Chapter summary (Summary tab)

- **0:00 Introduction** — Overview of the Reality Composer Pro 3 plugin system; Xcode and the editor share a project so engineers and artists collaborate, with custom components, systems, animation actions, and Script Graph nodes all running live in the editor.
- **2:00 Extending the editor** — How the plugin system works: editor and Xcode share one git repository; custom Swift compiles into a dynamic library; the editor loads and trusts plugins at runtime. Two schemes (`ChaparralVillage`, `RCPCustomComponents`) share the component/system code.
- **4:51 Custom components and systems** — Build a `Cauldron` component and `CauldronSystem` with RealityKit, expose them via a `RealityComposerProPlugin`, and watch the water level update in real time as artists adjust the inspector.
- **10:32 Controlling the water surface** — Extend `Cauldron` with vortex shader properties and update the system to drive a `ShaderGraphMaterial` at runtime, letting artists control vortex depth and rotation speed from the inspector.
- **13:19 Custom animation actions** — Implement `EntityAction` to make a `SetWaterLevelAction` that animates the water level on the sequencer timeline, subscribe to animation update events to interpolate, and register the action with the plugin.
- **17:12 Custom Script Graph nodes** — Use the `@Scriptable` macro to expose a component to Script Graphs, register a scripting module in plugin setup, and see generated nodes appear for no-code artist workflows.
- **21:16 Next steps** — Recap of plugin capabilities (components, systems, animation actions, Script Graph nodes); pointers to "Explore advances in RealityKit" and "Supercharge your spatial workflows with Reality Composer Pro 3."

## Code

See `code.md` — 10 snippets extracted from the Code tab.
