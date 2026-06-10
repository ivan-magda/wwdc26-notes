# Code samples — Session 234

_No Code tab for this session._ This is a design-craft talk with no APIs or code on
the Code tab. The transcript names no concrete framework APIs (no RealityKit /
ShaderGraph symbols are spoken). What follows are the **technical specs and craft
parameters** Michael cited — the closest thing to "code" this session offers, useful
for reproducing the workflow.

## Capture & output specs

- **Immersed field of view:** ~81° visible to the viewer; environment is 360°.
- **Sharpness target:** 40 pixels per degree.
- **Ideal panorama resolution:** 14,400 × 7,200 px (360°).
- **Primary camera:** tripod, leveled, **1 m** off the ground, deep depth of field.
- **Secondary camera (optional):** **2 m** off the ground, triggered simultaneously —
  fills parts of the scene not visible from the primary view in post.
- **Exposure:** bracketed exposures to cover the large dynamic range (sun → shadows).
- **Rig/lens:** must cover all views and produce a stitched 360° panorama.

## Reference / measurement capture

- Lighting reference shot **with** primary photography: Macbeth charts + chrome and gray
  spheres.
- Secondary photography for reference and measurement.
- Photogrammetry + LiDAR → point clouds that can be meshed as 3D-asset starting points
  and used for distance/slope measurements.
- Video of moving scene elements → reference for believable shader effects.
- Note any sounds tied to motion → source matching spatial audio.

## Post-production fidelity checks

- Transfer the refined panorama to **textures in UV space** over a 3D mesh (needed for
  parallax/depth).
- Fill non-visible areas using secondary photography + CG renders.
- **A/B** the 3D asset against the panorama to verify texture-transfer quality.
- Keep sharpness consistent between neighboring elements to convey scale.
- **Flop** the scene to re-see the composition with fresh eyes.
- Push **extreme gamma and gain** to expose color/value inconsistencies and texture-
  transfer data loss across displays.

## Real-time motion techniques (Bora Bora)

- **Clouds / wind:** UV flow maps; weight flow speed across the sky for depth/scale.
- **Cloud shadows:** scrolling mask that darkens terrain textures (not rendered lights);
  direction/speed matched to the clouds above so they feel connected.
- **Tree shadows:** pre-rendered into flip-book textures that darken terrain textures —
  trades soft-shadow rendering cost for a believable approximation.
- **Palm fronds:** reduced mesh complexity + UV flow map for wind; trunks/fronds sway
  more gently than leaflets via **hierarchical vertex animation + layered sine waves**
  (stack low- and high-frequency motion → non-repetitive variety).
- **Water:** HSB modulated over time to fake subsurface scattering; waves via layered
  normal maps + scrolling textures.
- **Spatial audio:** point emitters placed in the scene (e.g. a rippling-water emitter
  where the river flows around rocks).

## Spoken APIs

_None._ The talk references no concrete framework symbols.
