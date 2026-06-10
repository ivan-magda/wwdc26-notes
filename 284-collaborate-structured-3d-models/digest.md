---
title: "Collaborate on structured 3D models in visionOS — Full Digest"
session: WWDC26 · 284
url: https://developer.apple.com/videos/play/wwdc2026/284/
duration: 25m
speakers: Bill
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 284 — Collaborate on structured 3D models in visionOS

## TL;DR

A design-review-on-Vision-Pro session. The framing demo is a SharePlay call where a team
inspects an AirPods Pro model together — pulling it apart, slicing through it, and
exploding it into a layout — in shared space. The technical payload is the four pillars
of the Model Manipulator sample app:

1. **Asset preparation** — your USDZ export must preserve a **deep, nested hierarchy**. A flattened-to-root model renders perfectly but is dead to your code: you can't isolate or animate a single part.
2. **Manipulation** — `ManipulationComponent` + `InputTargetComponent`. Put them at the root and the assembly moves as one object; push them down to the children (`openAssembly()`) and every part becomes independently grabbable. Behavior is driven entirely by **where the component lives in the tree**.
3. **Interactive clipping** — `ClippingComponent`, **new in visionOS 27**. A three-state machine (`.off` / `.on` / `.editing`) and six draggable plane affordances let people slice into the interior. The interesting part is the **coordinate-frame math** to make a drag feel natural.
4. **Autoexpansion** — the model explodes itself along the most meaningful axis, chosen automatically via **volume-weighted position variance**, animated with `FromToBy`.

The recurring meta-point: spatial computing is for any data with enough dimensions to
deserve more than a flat screen — CAD, urban planning, logistics, real estate,
production design.

---

## 1. Why Vision Pro for this (intro)

The opening demo: three people in a SharePlay call doing a design review of the AirPods
Pro. Everyone sees the same asset, at the same fidelity, in the same space. The case
pulls closer and unlocks "the way it would on a workbench, except the workbench is
wherever you happen to be." One person lifts out the bottom assembly, rotates it so a
colleague sees exactly the same thing, and **points** — not an annotation or a
screenshot, just a gesture the whole group understands. Then clipping opens a cross
section onto the logic board, the assembly explodes, and someone pulls the motherboard
free to hold it up.

Three Vision Pro capabilities make this work:

- **Real-time collaboration** — multiple people, shared space, same moment.
- **Manipulation of rich representations** — multidimensional data deserves more than a flat screen.
- **Environment lighting** — the physical world grounds every virtual decision.

For the SharePlay plumbing, the talk points to **"Share visionOS experiences with nearby
people"** (WWDC25). The four build pillars follow: asset prep, manipulation, clipping,
autoexpansion.

## 2. Asset preparation — hierarchy is everything

"Assets without structure are hard to reason about and hard to use in code." Without a
hierarchy your code can't decide what to hide, show, or make manipulable.

The cautionary example: an engine block exported **without preserving structure** —
everything flattened up to the root as `InteriorPart_01`, `InteriorPart_03`, … part 25,
no sub-assemblies, no grouping. It looks completely fine in the viewport and renders
correctly, but the structure that would make it interactive is gone. "If I want to
isolate one piston, it's in here somewhere. Was it `InteriorPart_47` or
`InteriorPart_18`? I don't know, and neither does my code."

The fix is a **deep, nested hierarchy** — intentionally complex. Each part is its own
named, organized, grouped node, so you can find it in code, isolate it, highlight it,
and let a person pull it free. For the more technical export considerations the talk
points to **"Optimize your 3D assets for spatial computing"** (WWDC24).

## 3. Manipulation — component placement drives behavior

A good hierarchy lets people select or move an individual part using Vision Pro's
natural input, via RealityKit's `ManipulationComponent`. Deep-dive reference: **"What's
new in RealityKit"** (WWDC25).

The whole trick is **where the component lives in the tree**:

- Attach `ManipulationComponent` at the **root** → the whole assembly orients, moves, and scales as one object with natural hand movements.
- Move it **down to the children** → the top enclosure can be pulled away while the bottom stays put; two collaborators can rotate the two ear buds at the same time.
- Move it **back up to the root** → the spread moves as a single object again, internal relationships preserved. The hierarchy and geometry never changed — only the component placement.

### openAssembly()

```swift
func openAssembly() {
    components[ManipulationComponent.self] = nil
    components[InputTargetComponent.self] = nil

    for child in assemblyChildren {
        child.components.set(InputTargetComponent())

        var manipulation = ManipulationComponent()
        manipulation.releaseBehavior = .stay
        child.manipulationComponent = manipulation
    }
}
```

Strip the components off the root, then add `InputTargetComponent` + `ManipulationComponent`
to each child. `releaseBehavior = .stay` makes a part **stay where the person puts it**
when they let go.

### closeAssembly()

```swift
func closeAssembly() {
    for child in assemblyChildren {
        child.manipulationComponent = nil
        child.components[InputTargetComponent.self] = nil
    }

    components.set(InputTargetComponent())
    var manipulation = ManipulationComponent()
    manipulation.releaseBehavior = .stay
    manipulationComponent = manipulation
}
```

Same process in reverse: components off the children, back onto the root.

> **Don't forget collision.** The speaker deliberately omits the `CollisionComponent`
> from the slides, but flags it as **critical for event processing** — your entities
> must have collision components or input won't work.

## 4. Interactive clipping (new in visionOS 27)

Any sufficiently complex asset has layers you can't see from the surface — the internal
structure of a building, the routing behind a panel, the infrastructure beneath a city
block. **Clipping** lets people see through the asset, and `ClippingComponent` is a
**new RealityKit capability in visionOS 27**.

### ClippingComponent properties

It has four properties; the sample uses three:

- **`bounds`** — the one you work with most. An **axis-aligned bounding box in entity local space**; anything outside it is discarded by the renderer each frame.
- **`shouldClipChildren`** — defaults **`false`**. If you add clipping to a parent assembly and the children aren't clipping, this is why. The sample sets it `true`.
- **`shouldClipSelf`** — defaults **`true`**, almost always what you want.

### Six planes, six numbers

The six faces of the bounding box become six interactive plane entities — one per axis,
positive and negative, each a different color. Grab the +x face and pull it to reveal
more interior, or push it back out to restore. **Each plane controls exactly one scalar
value in the bounds.** That is the entire interaction model: six planes, six numbers.

### Three-state machine

- **`.off`** — no clipping; only the outside of the assembly is visible.
- **`.on`** — clipped to the bounding box; inner workings and sub-assembly layout show.
- **`.editing`** — the clipping planes are visible and interactive; moving them changes the bounds and reveals more or less of the interior.

Three components back the states in the sample:

- **`.off`** → `ClippingBoundsCache` (custom) remembers the last-edited bounds and feeds them to `ClippingComponent` when switching to `.on`.
- **`.on`** → `ClippingComponent` (RealityKit) is created and added; out-of-bounds geometry is discarded.
- **`.editing`** → `ClippingTransformSync` (custom) tracks the assembly transform and updates the `ClippingControl` (the entity managing the interactive planes) when it changes.

### Four coordinate frames

The heart of the section. There are four frames:

1. **World** — where everything sits.
2. **Model** — where the model lives; the frame `ClippingComponent` operates in. **Bounds changes must be made here.**
3. **Clipping Control** — where the editing planes are placed.
4. **Clipping Plane** — where the editing planes live and where **drag gesture events are expressed.**

The job: take the drag-gesture change, express it in the **Model** frame (constrained),
update the bounds, then convert it **back** to the **Clipping Plane** frame to move the
visual plane. Gesture details reference: **"Better Together: SwiftUI and RealityKit"**
(WWDC25).

### The drag → bounds pipeline (4 steps)

A drag gesture is attached to each clipping plane. From the raw delta to a bounds update:

1. Gesture arrives in the **Clipping Plane** frame (example delta `{0.5, -0.75, 0.1}`).
2. Transform it into the **World** frame — same vector, different representation, so the numbers change.
3. Transform from World into the **Model** frame.
4. **Constrain** the delta to the correct direction (+x, -y, …) via **projection**.

The "math magic" is just **projection**: how long the drag delta is along the direction
we care about — "the shadow of the drag delta vector cast on the direction vector." For
the +x plane the direction is the plane normal `{1, 0, 0}`:

- Take the unit direction (vector divided by its length squared — trivial here since it's already a unit axis).
- **Dot product** the drag delta with the direction vector → a scalar = how much the bounding box should change.
- Multiply the direction vector by that scalar → the **constrained delta in the Model frame**, ready to update `ClippingComponent.bounds`.

Then repeat the constraint to move the **visual plane**: transform the constrained delta
from Model back into the **Clipping Plane** frame and project onto the plane's normal
again. Because it's projected, the plane moves only along its normal instead of wherever
the hand went — which is what makes the gesture feel natural. Summary: **6 planes, 4
coordinate frames**, with simple transforms between each keeping the individual
calculations easy to reason about.

## 5. Autoexpansion — picking the axis with math

Automatic expansion fans the sub-assemblies apart to reveal inner structure — useful for
mechanical assemblies, buildings, "really any asset where understanding how the parts
relate to the whole would be helpful."

The problem: when an assembly loads, its children sit where the file defines them — for a
well-built asset that means **overlapping / nested**, correct but useless for
exploration. Expansion fans them apart along a **single axis**. The goal is an intuitive
direction **without forcing the person to pick it** — so the code decides, with math.

### Variance and weighting (the primer)

- **Deviation** — how far a value is from the average.
- **Variance** — square each deviation, add them up, divide by the count. A low variance means the values are clustered; a high variance means they're spread out.
- **Weighted variance** — multiply each squared deviation by a weight (importance) before averaging. Lets some values matter more than others.

### Volume-weighted position variance

The chosen heuristic: compute the **volume-weighted position variance along each axis**
(x, y, z) and **expand along the axis with the largest variance**. Each sub-assembly
contributes its position, weighted by its **volume**.

Worked through on the AirPods Pro:

- **x axis** — most sub-assemblies sit at nearly the same x; small variance. The two ear buds are spread on x but their volume isn't enough to outweigh everything clustered at the same place.
- **z axis** — even smaller. The bottom insert has some volume but sits too near the average; the hinge and lid-retention magnet are too small to move the needle.
- **y axis** — the **clear winner**: the large parts are far apart along y, and their larger volume weights that distance heavily.

With y chosen, the code assembles a set of **`FromToBy` animations** to move the
sub-assemblies into position along y — and the interior is exposed for interaction.

## 6. Next steps

Recap: prepare an asset hierarchy, manipulate its parts, clip through a complex assembly,
and expand the parts along an axis for a detailed view — a workflow for building design
review apps. Download the **sample project** from developer.apple.com. The speaker
recommends getting comfortable with **statistics, vector math, and linear algebra**.

Pointers:

- **"Discover the spatial preview framework"** — control models in real time from a Mac app.
- **"Explore enhancements to visionOS object tracking"** — augment a physical object (e.g., a race-car simulator cockpit) by overlaying virtual content and exploring its internal structure.
