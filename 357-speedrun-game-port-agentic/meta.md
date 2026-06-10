# Session 357 — Speedrun your game port with agentic coding

- **URL:** https://developer.apple.com/videos/play/wwdc2026/357/
- **Duration:** 28m
- **Speakers:** David Srour (Engineer, Metal Ecosystem Team)

## Description

Game Porting Toolkit 4 introduces agentic skills — expert skills and workflow
skills — plus a porting assistant agent that gives a coding agent the domain
knowledge to port a game to Apple platforms far faster and at higher quality.
David Srour ports Microsoft's MiniEngine (a D3D12 open-source engine) from
Windows to macOS using Claude Code, walking through windowing, Metal 4 scene
rendering, autonomous GPU debugging, game controllers, and MetalFX.

## Key topics

- **Game Porting Toolkit 4** distributed as a Claude Code plugin from a GitHub marketplace (`apple/game-porting-toolkit`)
- **Two skill kinds:** *expert skills* (technical guidance, best practices, anti-pattern flags) and *workflow skills* (structured discover → plan → execute-and-validate process)
- **Porting assistant agent** orchestrates which skills load at each milestone — does not rely on the model choosing to use them
- **Three-stage workflow:** Discover (scan codebase + capture reference frames from the evaluation environment), Plan (collaborative milestone goals), Execute-and-Validate (structured validation checklist: app launch, Metal API/shader validation, screen captures vs ground truth, anti-pattern review, memory checks)
- **Windowing & frame pacing:** Metal Display Link render loop, lifecycle events, layer config for games, direct-to-display presentation, drawable lifetime management to avoid stutter
- **Metal 4 scene rendering:** GPU resources (storage modes, residency sets, constant buffers), shader pipeline (argument buffer offsets, descriptor tables, root-signature translation via Metal Shader Converter), explicit producer-consumer barrier model mapping D3D12 states
- **Autonomous GPU debugging:** new `gpucapture` + `gpudebug` command-line tools on macOS 27 let the agent capture and inspect frame traces without manual Xcode interaction
- **Game controllers:** XInput → GCController with dynamic capability/button querying and connect/disconnect handling
- **MetalFX:** temporal upscaling (jitter in pixel space, motion vectors, MIP bias, history reprojection) and frame interpolation (dedicated present thread, frame timing/order); new Metal HUD overrides on macOS 27 to validate and tune
- **Scales to production engines:** also demoed adding a Metal 4 backend to Godot (alongside its existing Metal 3 backend) in a few days

## Related sessions to fetch (referenced in this talk)

- [ ] Go further with Metal 4 games

## Chapter summary (Summary tab)

- **0:00 Introduction** — Game Porting Toolkit 4 introduces agentic skills (expert + workflow) giving a coding agent the domain knowledge to accelerate porting a game to Apple platforms.
- **1:06 Porting assistant workflow** — orchestrates the full process across three stages: Discover (scan codebase, capture reference frames), Plan (collaborative milestone goals), Execute-and-Validate (structured validation: app launch, Metal API correctness, screen captures, anti-pattern review, memory issues).
- **6:25 Windowing and frame pacing** — window / Metal swap chain / presenting drawables / metal-cpp skills guide a correct render loop via Metal Display Link, lifecycle handling, game-tuned layer config, direct-to-display presentation for lower latency, drawable lifetime management to avoid stuttering.
- **8:28 Scene rendering with Metal 4** — GPU resources / shader pipeline / Metal Shader Converter / synchronization skills guide three milestones: resource management (storage modes, residency sets, constant buffers), shader pipeline (argument buffer offsets, descriptor tables, root-signature translation), command encoding with the explicit barrier model mapping D3D12 states to producer-consumer sync.
- **15:16 Debugging with GPU command-line tools** — new `gpucapture` and `gpudebug` CLI tools on macOS 27 enable fully autonomous agent-driven GPU debugging; the agent captures a frame trace and inspects pipeline state, textures, and dispatch dimensions without Xcode.
- **19:09 Game controllers and MetalFX** — game controller skill ports Windows XInput to GCController (discovery, dynamic button-layout querying, connect/disconnect); MetalFX upscaling + frame interpolation skills handle jitter, motion vectors, mip bias, history reprojection, present thread; new Metal HUD overlays on macOS 27 to validate and tune.
- **25:11 Next steps** — download/install Game Porting Toolkit 4 skills from the GitHub repo, invoke the porting assistant on your codebase, let it guide a structured porting plan with clear milestones.

## Code

See `code.md` — 5 snippets from the Code tab (plugin install + four with-skill / without-skill comparisons).
