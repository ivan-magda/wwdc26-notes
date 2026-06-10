# Code samples — Session 285

Extracted from the Code tab. Timestamps map to the transcript. Curly quotes and a stray
extra paren that bled into the extraction have been normalized to valid Swift.

## 8:15 — Create or open a USD stage

```swift
import USDKit

// Create a new empty in-memory stage

let stage = USDStage()

// Open a stage from a file on disk

let url = URL(fileURLWithPath: "/ALab/entry.usda")
let stage = try USDStage.open(url)
```

## 8:44 — Traverse the hierarchy, then define and reference an asset

```swift
// Traverse all prims looking for the oscilloscope
for prim in stage.descendants {
    if prim.name == "scope" {
        // There it is! 🔬
    }
}

// It wasn't there — define a new Xform prim for it

let scope = stage.definePrim(at: "/World/scope", type: "Xform")

// Add a file reference to the prim

try scope.references.add("/ALab/assets/scope.usda")
```

## 9:37 — Move the prim with a transform operation

```swift
// Creates xformOp:translate and updates xformOpOrder automatically

scope.addTransformOperation(type: .translate)
scope["xformOp:translate", as: USDValue.Vec3d.self] = [2.5, 0.0, -1.0]
```

## 10:42 — Apply the AccessibilityAPI schema and set label/description

```swift
// Apply the multi-apply AccessibilityAPI schema with instance name "default"

try scope.applyAPISchema("AccessibilityAPI", instanceName: "default")

// Create the label and description attributes

scope.makeAttribute(named: "accessibility:default:label", as: .string)
scope.makeAttribute(named: "accessibility:default:description", as: .string)

// Set their values

scope["accessibility:default:label", as: String.self] = "Oscilloscope"
scope["accessibility:default:description", as: String.self] =
    "Vintage signal analyzer with a 3D wireframe display, topped by a color bar test monitor"
```

## 12:05 — Export a compressed USDZ package

```swift
let output = URL(fileURLWithPath: "/ALab/alab_compressed.usdz")

// Export the stage as a USDZ package

try stage.exportPackage(
    to: output,
    options: [
        .preferSmallTextureFiles(quality: .standard),   // compress textures (AVIF)
        .preferSmallMeshFiles                            // compress mesh geometry
    ]
)
```

---

## Useful API facts surfaced by the code

- **Stage entry points:** `USDStage()` (in-memory) and `try USDStage.open(url)` (from disk; throws on file access).
- **Traversal:** `stage.descendants` yields prims; each has a `.name`.
- **Authoring prims:** `stage.definePrim(at:type:)` creates a typed prim (e.g. `"Xform"`).
- **Composition:** `prim.references.add(_:)` pulls in another layer/file as a lightweight reference rather than copying data.
- **Transforms:** `prim.addTransformOperation(type: .translate)` creates `xformOp:translate` and maintains `xformOpOrder`; the value is read/written via a typed subscript with `USDValue.Vec3d.self`.
- **Schemas:** `prim.applyAPISchema(_:instanceName:)` applies a multi-apply API schema (here `AccessibilityAPI`); attribute names follow the spec, e.g. `accessibility:<instance>:label` / `:description`.
- **Attributes:** `prim.makeAttribute(named:as:)` plus a typed subscript `prim["name", as: T.self]` for get/set.
- **Export/compression:** `stage.exportPackage(to:options:)` with `.preferSmallTextureFiles(quality:)` and `.preferSmallMeshFiles`. Same compression also available in Preview's UI and the `usdcrush` CLI.
