# Code samples — Session 357

Extracted from the Code tab. The plugin install is shell; the rest are
with-skill / without-skill C++ comparisons (metal-cpp) that illustrate what the
expert skills change about the agent's output.

## Install the Game Porting Toolkit plugin (Claude Code)

```text
/plugin marketplace add apple/game-porting-toolkit
/plugin install game-porting-skills@game-porting-toolkit
```

## Residency set — register resources before binding (GPU resources skill)

```cpp
// With skill
residencySet->addAllocation(texture);
residencySet->commit();
// ...
argumentTable->setAddress(texture->gpuAddress(), bindPoint);

// Without skill
argumentTable->setAddress(texture->gpuAddress(), bindPoint);
```

Without the skill the texture is never added to a residency set, so the GPU
cannot read it — silently incorrect results, no error.

## Argument buffer offsets — query from the runtime (shader pipeline / converter skill)

```cpp
// With skill
IRRootSignatureGetResourceLocations(m_MtlCurIRRootSig, locations);
size_t offset = locations[i].topLevelOffset;

// Without skill
size_t offset = paramIndex * descriptorSize;
```

Copying MiniEngine's `index * size` arithmetic assumes a layout the Metal Shader
Converter may not produce — wrong offsets, incorrect rendering with no error.

## Barriers — map D3D12 states to Metal 4 producer-consumer stages (synchronization skill)

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

Without the skill the agent falls back to broad blanket barriers at encoder
boundaries — works for simple cases, silently breaks as the pipeline grows.

## Root signature parameter count — trust shader reflection (shader converter skill)

```cpp
// With skill
IRShaderReflection* refl = IRShaderReflectionCreate();
IRObjectGetReflection(compiledObj, IRShaderStageCompute, refl);
// ...
s_RootSignature.Reset(4, 2); // Reflection reveals: 4 params

// Without skill
s_RootSignature.Reset(5, 2);
```

The engine declares 5 root parameters but the HLSL shader only declares 4;
carrying over the engine's count shifts the sampler table to the wrong offset.
Reflection from the Metal Shader Converter resolves the actual layout.

## Spoken APIs / tools (named in the talk)

- **Game Porting Toolkit 4** — agentic skills plugin (expert + workflow skills), porting assistant agent
- **Metal Display Link** — drives the render loop in the windowing milestone
- **Metal 4** — explicit memory management, new command structure, fully explicit synchronization, residency sets, argument buffers
- **Metal Shader Converter** — runtime translation of HLSL shaders / D3D12 root signatures; reflection API (`IRShaderReflection`, `IRObjectGetReflection`, `IRRootSignatureGetResourceLocations`)
- **`gpucapture`** / **`gpudebug`** — new macOS 27 command-line GPU capture + analysis tools for autonomous agent debugging
- **GCController** — game controller framework (ports from Windows XInput)
- **MetalFX** — temporal upscaling + frame interpolation
- **Metal HUD** — extended on macOS 27 with MetalFX validation/override overlays (exposure, jitter scatter plot, jitter multiplier + motion vector scale overrides)
