# Code samples — Session 252

This is a no-code session: nearly everything is built visually in ScriptGraph.
The Code tab has a single snippet — the Swift side of the SwiftUI speech-bubble
bridge — and the rest of the "code" in this talk is ScriptGraph node wiring (see
the Spoken nodes/APIs list below).

## 17:22 — Subscribing to a ScriptGraph Scene Event from Swift (speech bubble)

The `squirrelTalk` Scene Event is sent from ScriptGraph with a `sayThis` String
payload. Swift subscribes to it, stores the text, and renders a SwiftUI
`Attachment` over the squirrel entity.

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

---

## Spoken APIs — ScriptGraph nodes & components used in the build

Components added to make an entity interactive / physical:

- **Input Target Component** — makes the entity a gaze target
- **Collision Component** — defines the size of the gaze target
- **Hover Effect Component** — highlights the entity on gaze
- **Scripting Component** — attaches a ScriptGraph to the entity (assign in the Inspector)
- **Physics Body Component** — opts the entity into the physics simulation

ScriptGraph nodes:

- **On Drag** (event node) — exposes `Scene Location`, `Scene Translation`, and an `isEnd` bool
- **Set Transform** — writes translation into the entity's transform component
- **Multiply by Number** — scales the drag translation by `dragSpeed`
- **Input** node — feeds a script Input variable (e.g. `dragSpeed`, type number, public, default 1.3) into the graph
- **Add Force** — applies an additive force to the physics body
- **Set Variable** — stores `targetPosition` / computes `dragDelta` (current minus previous)
- **Set PhysicsBodyComponent** — toggles gravity and raises linear damping while dragging
- **Compose Subgraph** / **Convert to Prototyped Subgraph** — group + reuse logic ("Check for Change")
- **Custom Event** via a **Custom Node Library** — `nutIsDragged` with a `nutPosition` property; **Sync Nodes** to publish; **Send** / **On** event nodes to emit/listen across entities
- **Set Material Parameter** — sets a Bool param `isNutDragged`, consumed by the Shader Graph's public input to swap squirrel textures
- **Send Scene Event** — `squirrelTalk` carrying a `sayThis` String, bridged to Swift/SwiftUI
