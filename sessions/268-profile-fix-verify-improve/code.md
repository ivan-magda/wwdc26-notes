# Code samples — Session 268

Extracted from the Code tab. Snippets map to the transcript's three-hang walkthrough.
(Smart quotes from the extraction normalized to straight quotes so the Swift compiles.)

## Signpost interval around lasso selection

```swift
// Add signpost interval around Lasso Selection

import os.signpost

let signposter = OSSignposter(subsystem: "Demo App", category: .pointsOfInterest)
var lassoIntervalState: OSSignpostIntervalState? = nil

func lassoSelectionUpdated() {
    lassoIntervalState = signposter.beginInterval("Lasso Selection")
    // Update selection in canvas…
}

func lassoSelectionEnded() {
    // Finalize lasso selection...
    signposter.endInterval("Lasso Selection", lassoIntervalState!)
}
```

## Existentials (the slow path)

```swift
// Existentials

protocol Foo { }

struct TypeA: Foo { }
struct TypeB: Foo { }

func bar(_ foo: any Foo) {

}
```

## Alternative 1 — concrete types

```swift
// Concrete types

protocol Foo { }

struct TypeA: Foo { }
struct TypeB: Foo { }

func bar(_ a: TypeA) {

}

func bar(_ b: TypeB) {

}
```

## Alternative 2 — generics

```swift
// Generics

protocol Foo { }

struct TypeA: Foo { }
struct TypeB: Foo { }

func bar<T: Foo>(_ generic: T) {

}
```

## Alternative 3 — enums

```swift
// Enums

enum Foo {
    case a(TypeA)
    case b(TypeB)
}

struct TypeA { }
struct TypeB { }

func bar(_ enum: Foo) {

}
```

## Thumbnail rendering (inherits the Main Actor — the hang)

```swift
// Thumbnail rendering

let drawingData = note.drawingData
let canvasImages = note.decodeCanvas()
thumbnail = await Task(name: "Render Thumbnail") {
    await renderThumbnail(drawingData: drawingData, canvasImages: canvasImages, size: CGSize(width: 300, height: 240))
}.value
```

## Thumbnail rendering moved off the Main Actor (`@concurrent`)

```swift
// Thumbnail rendering off Main Actor

let drawingData = note.drawingData
let canvasImages = note.decodeCanvas()
thumbnail = await Task(name: "Render Thumbnail") { @concurrent in
    await renderThumbnail(drawingData: drawingData, canvasImages: canvasImages, size: CGSize(width: 300, height: 240))
}.value
```

## File saving on the main thread (synchronous blocking write)

```swift
// File saving

let encoder = PropertyListEncoder()
encoder.outputFormat = .binary
guard let data = try? encoder.encode(snapshots) else { return }
let id = signposter.beginInterval("Writing To File")
try? data.write(to: fileURL, options: .atomic)
signposter.endInterval("Writing To File", id)
```

## File saving moved to the background

```swift
// File saving

Task { @concurrent in
    let encoder = PropertyListEncoder()
    encoder.outputFormat = .binary
    guard let data = try? encoder.encode(snapshots) else { return }
    let id = signposter.beginInterval("Writing To File")
    try? data.write(to: fileURL, options: .atomic)
    signposter.endInterval("Writing To File", id)
}
```

---

## API / technique facts surfaced by the code

- `OSSignposter(subsystem:category:)` with `category: .pointsOfInterest` auto-surfaces intervals in the Points of Interest track; `beginInterval` / `endInterval` bracket a workflow.
- Swift `any`-typed existential parameters can incur runtime cost (`swift_project_boxed_opaque_existential`); concrete types, generics, or enums give the compiler more to optimize.
- `Task(name:)` lets you name a task so it shows up labeled in the Swift Executors instrument.
- The `@concurrent` attribute on a `Task` closure moves its body off the inherited Main Actor onto the global concurrent executor; the compiler still enforces data-race safety.
- Wrapping a synchronous `data.write(to:options: .atomic)` in `Task { @concurrent in … }` pushes encoding + file I/O to the background thread pool, unblocking the Main Actor.
