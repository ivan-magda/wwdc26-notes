---
title: "Speedrun your game port with agentic coding — Full Digest"
session: WWDC26 · 357
url: https://developer.apple.com/videos/play/wwdc2026/357/
duration: 28m
speakers: David Srour
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 357 — Speedrun your game port with agentic coding

## TL;DR

**Game Porting Toolkit 4** turns a generic coding agent into a game-porting
specialist by shipping Apple's porting expertise as **agentic skills** — packaged
as a Claude Code plugin. The story has three moving parts:

1. **Expert skills** inject platform knowledge, best practices, and anti-pattern
   detection so the agent writes *correct* Metal/Apple code, not just code that
   compiles. Throughout the talk, "with skill" vs "without skill" comparisons show
   the agent silently getting things wrong (wrong offsets, missing residency,
   blanket barriers) without them.
2. **Workflow skills + the porting assistant agent** impose a structured
   **Discover → Plan → Execute-and-Validate** methodology, automatically loading
   the right expert skills per milestone and validating each milestone against
   **ground-truth captures** from the original (Windows/D3D12) build.
3. **New macOS 27 tooling** — `gpucapture` / `gpudebug` command-line tools and an
   extended **Metal HUD** — closes the loop so the agent can capture, inspect, and
   debug GPU frames **fully autonomously**, without a human driving Xcode.

Demo: David Srour ports Microsoft's open-source **MiniEngine** (D3D12) from Windows
to a native **Metal 4** Mac app — windowing, scene rendering, post-processing,
dynamic lights, game controllers, MetalFX — using **Claude Code**. He closes by
showing the same skills adding a Metal 4 backend to **Godot** (production engine,
existing Metal 3 backend) in a few days.

---

## 1. What's in Game Porting Toolkit 4

A typical port means scoping the work, getting a build going, converting shaders,
bringing up the renderer, remapping inputs, adding native polish, and optimizing —
months of manual platform work. GPTK 4 compresses this with three pieces:

- **Expert skills** — technical guidance for a specific domain (windowing,
  resources, shaders, sync, controllers, MetalFX). They provide platform knowledge,
  apply best practices, and flag common porting anti-patterns.
- **Workflow skills** — a structured approach (discover, plan, validate).
- **Porting assistant agent** — orchestrates the methodology and ensures the
  relevant expert skills load at the right time, *without relying on whether the
  model decides to use them*. The agent also persists what it learns across
  milestones so nothing is lost between sessions.

Everything ships as a plugin from the Game Porting Toolkit marketplace on GitHub:

```text
/plugin marketplace add apple/game-porting-toolkit
/plugin install game-porting-skills@game-porting-toolkit
```

The presenter uses **Claude Code** for the entire demo.

## 2. The porting assistant workflow

Three stages:

- **Discover** — the discover workflow skill scans the codebase, grabs **reference
  captures** from the evaluation environment (the original working build), and asks
  about your preferences.
- **Plan** — you and the assistant define **milestone goals**, because porting a
  whole title is too big for one session. The output is a comprehensive plan:
  goals → milestones, each with expert skills mapped to it.
- **Execute-and-Validate** — for each milestone the agent makes the changes (you
  guide it), then runs a multi-point **validation checklist**:
  - app launches properly
  - Metal validation on API usage and shaders
  - visual correctness via screen captures
  - comparison against **ground-truth references**
  - code review against known anti-patterns from the skills used
  - memory issue checks, and more

This validation loop is what keeps the port moving and is where the human addresses
last concerns per milestone.

## 3. Windowing and frame pacing

The first goal: get a window so there's something to draw into. Several skills
combine here:

- **window skill** — window creation and lifecycle
- **"translating to Metal" skill** — maps D3D12 swap-chain concepts
- **presenting drawables skill** — frame-pacing best practices
- **metal-cpp skill** — correct Metal object lifetime patterns

The hard part is following *all* the best practices, not just some. Concretely the
skills set up:

- **Metal Display Link** for the render loop
- lifecycle handling (focus changes, fullscreen)
- layer resolution + color space tuned for games
- **direct-to-display presentation** for lower latency
- **drawable lifetime management** to avoid stuttering
- keeping textures **resident** so the GPU can access them

Result: a smooth color-clear animation with steady frame pacing on the Metal HUD —
presentation solid before any real rendering begins.

## 4. Scene rendering with Metal 4

Target for "first playable": lighting, shadows, SSAO, tone mapping. Metal 4 raises
the bar with **explicit memory management** and a **new command structure**. Scene
rendering breaks into three milestones — GPU resources, shader pipelines, command
encoding.

### GPU resources

The resources skill teaches:

- which **storage modes** to use, including options specific to Apple silicon's
  **tile architecture**
- the recommended **constant buffer** allocation pattern (in Metal 4, constants
  go through buffers)
- registering every resource in a **residency set** for GPU access, done **early**
  in the app

```cpp
// With skill
residencySet->addAllocation(texture);
residencySet->commit();
// ...
argumentTable->setAddress(texture->gpuAddress(), bindPoint);

// Without skill
argumentTable->setAddress(texture->gpuAddress(), bindPoint);
```

Without the skill the agent just gets something compiled; the GPU can't read the
texture → incorrect results, no error.

### Shader pipelines (Metal Shader Converter)

The shader pipeline + converter skills wire HLSL shaders through Metal's binding
model: creating pipeline states with Metal 4's compiler, the **descriptor table**
encoding rules, translating **D3D12 root signatures** through the Metal Shader
Converter runtime, and Metal 4's **argument buffer layout** model.

```cpp
// With skill
IRRootSignatureGetResourceLocations(m_MtlCurIRRootSig, locations);
size_t offset = locations[i].topLevelOffset;

// Without skill
size_t offset = paramIndex * descriptorSize;
```

Copying MiniEngine's `index * size` math assumes a layout the converter may not
produce → wrong offsets, incorrect rendering, again no error.

### Command encoding (synchronization)

Metal 4 uses **fully explicit synchronization**. Multiple encoders in a command
buffer need proper barriers between them, and D3D12 ↔ Metal 4 barrier models
differ. The sync skill maps between them and provides **stage mapping tables**
(D3D12 states don't translate directly).

```cpp
// With skill
m_MtlPendingProducerStages |= MtlProducerStageFromD3D12(OldState);
m_MtlPendingConsumerStages |= MtlConsumerStageFromD3D12(NewState);
// ...
m_ComputeEncoder->barrierAfterStages(
    m_MtlPendingProducerStages,
    m_MtlPendingConsumerStages,
    MTL4::VisibilityOptionDevice);

// Without skill
m_ComputeEncoder->barrierAfterStages(
    MTL::StageDispatch,
    MTL::StageAll,
    MTL4::VisibilityOptionDevice);
```

Without the skill the agent uses broad blanket barriers at encoder boundaries —
fine for simple cases, silently broken as the pipeline grows.

With the three geometry passes (depth, shadow, color) working, the agent validates
the output against the evaluation environment and confirms all three match.

### Post-processing — skills as guardrails

Post-processing adds **compute dispatches**: an SSAO chain (compute dispatches
needing correct resource setup + sync) and tone mapping (compute → render). The
same rendering skills are reused, now acting as **guardrails**:

- sync skill catches barrier configs that ignore Apple silicon **tile memory**
- resources skill provides safe defaults for resources not yet bound during
  incremental porting
- shader converter skill catches data alignment mismatches between engine and
  shaders

```cpp
// With skill
IRShaderReflection* refl = IRShaderReflectionCreate();
IRObjectGetReflection(compiledObj, IRShaderStageCompute, refl);
// ...
s_RootSignature.Reset(4, 2); // Reflection reveals: 4 params

// Without skill
s_RootSignature.Reset(5, 2);
```

The engine declares 5 root parameters; the HLSL shader declares 4. Reflection from
the converter resolves the real layout; carrying over the engine's count shifts the
sampler table to the wrong offset.

This produces **first light**, but with wrong lighting and stretched wall
textures. SSAO is visibly working on the drapes, so it's close but broken.

## 5. Autonomous GPU debugging (macOS 27)

Normally you'd capture a frame in Xcode and diagnose by hand. An agent couldn't do
that on its own. **macOS 27 introduces two command-line tools** for fully
autonomous agent workflows:

- **`gpucapture`** — capture a GPU frame
- **`gpudebug`** — analyze the capture

With the **debugging rendering issues skill** (a structured symptoms → root-cause
methodology), the agent:

1. takes the human's description of the visual symptoms
2. runs `gpucapture` to capture a trace
3. uses `gpudebug` to inspect resource bindings, constants, resource contents, and
   data flow through the pipeline — everything you'd check in Xcode
4. traces where output diverges from the evaluation environment, identifies the
   problem, and implements a fix

The corrected build matches the expected lighting and texture maps. During
validation the agent uses the tools again to compare dispatch calls, pipelines, and
**dispatch dimensions** against the original trace — the tedious side-by-side
capture comparison now handled autonomously.

### Dynamic lights

Final rendering goal: **128 point and spot lights** via a compute **light culling
pass** that mixes buffers and textures, plus new light-data resources. The shader
converter + sync skills handle culling; the resources skill covers light data. The
**residency pattern pays off** (new light resources made GPU-accessible
automatically), the sync skill catches invalid render-stage flags set on compute
encoders, and the converter skill ensures correct binding for mixed resource types.

Result: **first playable** — the Sponza scene properly lit with directional and
dynamic lighting, using all the Metal 4 rendering features.

## 6. Game controllers

Add gamepad support for better camera control. The game controller skill covers
**GCController** discovery, the input model, and porting from Windows:

- Windows **XInput** gives a fixed controller layout; the skill teaches the agent
  to **check what each controller actually supports** instead.
- Same for inputs: query what's available on the connected device rather than
  hardcoding a button map.
- Controllers connect/disconnect at any time; the skill provides the right
  **discovery and disconnect patterns** to handle that gracefully.

Result: device discovery, thumbstick mapping, and connect/disconnect events all
working.

## 7. MetalFX — upscaling + frame interpolation

Two distinct MetalFX features, two milestones, two skills:

- **Temporal upscaling** — render lower-res, reconstruct a higher-quality image;
  done right it can match or exceed native quality. The upscaling skill:
  - configures **jitter in pixel space** (naive normalized values disable temporal
    accumulation)
  - sets up **motion vectors** with the right scale/conventions (avoids ghosting)
  - provides a good starting **MIP bias** for the scale factor
  - sets up **history reprojection** so the scaler accumulates detail across frames
- **Frame interpolation** — doubles effective frame rate by generating every other
  frame, at the cost of some input latency. The frame interpolation skill:
  - sets up a **dedicated present thread** (presenting from the render thread gives
    uneven spacing)
  - configures precise **timing** so interpolated + rendered frames are evenly spaced
  - gets the **presentation order** right so synthesized frames interleave correctly

(Deep-dive referenced: **"Go further with Metal 4 games."**)

### Metal HUD debugging on macOS 27

macOS 27 extends the Metal HUD to validate/tune MetalFX integration:

- displays the upscaler's **exposure** parameter (confirm it's fed to the API)
- shows **jitter sequence** info; out-of-range jitters in red with a scatter plot
- **overrides** that take effect while the app runs: visualize MetalFX-observed
  exposure, toggle the jitter scatter plot, and **override jitter multipliers and
  motion vector scales**

Debugging workflow shown: wobble artifacts in motion → HUD shows the motion-vector
**X-scale axis is negated** → flip it positive in overrides → wobble fixed. Then
blurriness on textured objects → tune **jitter multipliers** → sharper. If a HUD
override fixes the output, that pinpoints the bug — trace it back to the code that
computes jitter / motion vectors and fix the source.

## 8. Scaling beyond the demo

Beyond MiniEngine, the porting assistant was pointed at **Godot** — a production
engine with an existing **Metal 3** backend — and asked to add **Metal 4** alongside
it. Same workflow, same skills, same ground-truth validation; up and running in a
few days. The point: the skills scale from small engines to production-grade
projects. The human focuses on architectural decisions, reviewing output, and
game-specific context; the skills handle the platform knowledge.
