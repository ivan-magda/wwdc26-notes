---
title: "Design no-code games with Reality Composer Pro 3 — Full Digest"
session: WWDC26 · 252
url: https://developer.apple.com/videos/play/wwdc2026/252/
duration: 19m
speakers: Saschka Unseld (Creative Director, Apple)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 252 — Design no-code games with Reality Composer Pro 3

## TL;DR

A creative director, not an engineer, builds a working visionOS RealityKit game
end to end **without writing a line of code**, using **ScriptGraph** — Reality
Composer Pro 3's node-based visual scripting. Three big ideas:

1. **Event-driven nodes.** Every behavior is the same shape: an **event node**
   (e.g. `On Drag`, a custom event, a scene event) → some **logic/math nodes** →
   **Set nodes** that write back into components. That's the whole mental model.
2. **Tight design-loop iteration.** Play in the viewport, then **Preview on
   Device** to test on Vision Pro for real, tuning **public Input variables**
   (like `dragSpeed`) live. **Live preview** lands later this year.
3. **It scales past prototypes.** Subgraphs and **Prototyped Subgraphs** for
   reusable logic, **Custom Events** so multiple entities' graphs talk to each
   other, material/shader parameter driving for character reactions, and **Scene
   Events** that bridge ScriptGraph to **SwiftUI** (a speech bubble) when you do
   want code.

The running demo: a sleeping squirrel found a nut and dozed off before nightfall;
the player reaches out, steals the nut, and drags it to guide the squirrel home.

---

## The wish, and what ScriptGraph is

Saschka frames his whole process as starting from "a wish that something might
exist," then working until it does. The wish here: on Vision Pro, literally reach
out with your hand, steal a squirrel's nut, and drag it to lead the squirrel back
to its nest before winter.

**ScriptGraph** is Reality Composer Pro's node-based visual scripting. Games are
built through **event-driven logic**: pinch events animate leaves open/closed,
drag-gesture events move the nut, custom events scroll the level at waypoints.
The standout feature for him is that graphs are testable and iterable **directly
in Reality Composer Pro and on Vision Pro**.

(If you want fundamentals first, the talk points to a separate Reality Composer
Pro 3 session.)

## Make the nut interactive, then draggable

The squirrel and nut use a deliberately flat **cut-out look** — simple textured
planes. To make the nut respond to the player, three components go on the entity:

- **Input Target Component** — makes it a gaze target.
- **Collision Component** — defines the size of that gaze target.
- **Hover Effect Component** — highlights it on gaze.

Then a **Scripting Component** is added and, in the Inspector, assigned the new
`dragNut` ScriptGraph (created via right-click → New → Script Graph in the
Project Browser). The graph runs on whichever entity owns the Scripting
Component.

The first logic is minimal and shows the core pattern:

- **`On Drag`** event node listens for the pinch-and-drag.
- A **Set Transform** node writes into the nut's transform component.
- Wire the event's **trigger** to the Set Transform's trigger (so it fires each
  drag update), and wire the event's **Scene Location** output into the Set
  Transform's **translation** input.

That's a complete draggable object: event → Set node modifying a component.

## Iterate: viewport, device, and a public Input variable

Press **Play** at the top of the workspace to test in the viewport — click-drag
the nut right there. Going further, switch **Simulation Mode** to **Preview on
Device**, pick the Vision Pro, and Play: the squirrel and nut appear next to you,
and you can gaze + tap-drag the nut for real. (**Live preview** — full live
iteration — is coming later this year.)

The first-pass drag felt like too much hand movement, so:

- Multiply `On Drag`'s **Scene Translation** by a **Multiply by Number** node to
  make it more responsive and kill the initial jump.
- Expose the factor as a **public Input variable** `dragSpeed` (type number,
  default 1.3), fed in via an **Input** node.

Because it's public, `dragSpeed` shows up in the nut's Scripting Component, so he
tunes it **while wearing the headset** (and brings up Mac Virtual Display to see
changes): 1.5 → too much, 1.1, 1.15 — "that's it."

### Overrides

When he edits `dragSpeed`, the name goes **bold** — meaning the value is applied
as an **Override**, not baked into the script. Overrides are per-Scripting-
Component variations: multiple nuts can share one ScriptGraph yet each carry its
own `dragSpeed`.

## Make it physical

Plain translation felt bland; he wants to **toss** the nut. So:

- Add a **Physics Body Component** to put the nut in the physics simulation.
- Drive it with an **Add Force** node instead of setting the transform directly.

Add Force is **additive**, so he needs the drag's change over time, which the
gesture doesn't give by default:

- Store the drag translation into a `targetPosition` variable with **Set
  Variable**.
- Compute `dragDelta` = current position − previous position.
- Trigger **Add Force**, wiring `dragDelta` (×  a multiplier for weight) into it.

Now the nut feels physical and falls when tossed — but it's hard to lift, because
gravity constantly pulls it down. Fix with a **Set PhysicsBodyComponent** node,
which changes physics settings dynamically:

- Turn **gravity off** while the nut is being dragged.
- Raise **linear damping** (more friction, slows faster, less finicky).

This logic fires on pick-up and on drop. Result feels much nicer.

> Core recap: ScriptGraphs **listen to event nodes**, **do logic** on the data,
> and **use Set nodes to modify components**. From that one idea, a lot is
> possible.

## Advanced: Subgraphs and Prototyped Subgraphs

Graphs get messy over time. A chunk that "checks whether the drag's `isEnd` bool
just changed, and if so triggers logic" became unreadable. Fixes:

- Select the nodes → right-click → **Compose Subgraph** → name it "Check for
  Change." Instantly tidier.
- Since "trigger when a bool changes" is needed everywhere: right-click →
  **Convert to Prototyped Subgraph**. It lands in the asset browser and now
  appears in the **add-node menu** alongside built-in nodes — reusable across all
  scripts.

## Advanced: Custom Events between entities

The squirrel doesn't react when its nut is stolen. To make it look at the dragged
nut, the squirrel gets its **own** ScriptGraph — but it needs to know the nut is
being dragged. Solution: a **Custom Event**.

- Create a **Custom Node Library** in the Project Browser; add a **Custom Event**
  named `nutIsDragged`.
- Add a **property** `nutPosition` so the event carries the nut's location.
- Click **Sync Nodes** to publish the custom node.

Then:

- In the **nut's** graph, a **Send "nutIsDragged"** node fires when dragged,
  passing the nut's world position.
- In the **squirrel's** graph, an **On "nutIsDragged"** node listens and uses
  `nutPosition` to drive the squirrel's **rotation** — a snappy flip that suits
  the cut-out style.

## Advanced: driving materials for reactions

He also makes the squirrel look **upset** when robbed, by driving its **Material**
from ScriptGraph:

- The squirrel's **Shader Graph** material has a public input `isNutDragged` that
  picks between two squirrel textures.
- In the squirrel's ScriptGraph, a **Set Material Parameter** node sets a **Bool**
  param `isNutDragged`, targeting the entity with the squirrel's **Model
  Component**, triggered to report whether the nut is dragged.

## Advanced: Scene Events bridge to SwiftUI (and a little code)

To give the squirrel a literal voice, he wants a **SwiftUI speech bubble** (cut-out
style). SwiftUI means running via Xcode — and Reality Composer Pro 3 will
**generate an Xcode project** for him on the spot (switch preview mode to "Run
with Xcode").

The bridge is **Scene Events**, which can be sent *and* listened to from Swift:

- In ScriptGraph, a **Send Scene Event** node named `squirrelTalk` with a String
  variable `sayThis` (e.g. "Hey, that's my nut!").
- In Swift, **subscribe** to `squirrelTalk`, store `sayThis`, and show a SwiftUI
  `Attachment` over the squirrel entity using that text. As a designer, he just
  **prompts Coding Intelligence** to write the subscription glue:

```swift
if let scene = entity.scene {
    scene.subscribe(forEventName: "squirrelTalk", on: { event in
        if let sayThis: String = try? event.value("sayThis") {
            self.sayThis = sayThis
        }
    }).store(in: &cancellables)
}

// ...

} attachments: {
    Attachment(id: "squirrelTalk") {
        SquirrelTalkAttachmentView(text: sayThis)
    }
}
```

## Where it goes next

Teased as further ScriptGraph work (not built on screen): the squirrel walking
and jumping to reclaim its nut, draggable leaves to traverse a whole level, and a
visionOS **ornament** to jump anywhere in the level if you get stuck.

Next steps from Apple: download **Reality Composer Pro 3**, take the advanced-
workflows deep dive, and grab the full **Squirrel Sample Project** from the Apple
Developer site.
