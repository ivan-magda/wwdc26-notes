# Code samples — Session 283

Extracted from the Code tab. Timestamps map to the transcript chapters.

## Enable high frame rate tracking

```swift
// Create reference object configuration
var configuration = ReferenceObject.Configuration()
configuration.highFrameRateTrackingEnabled = true

// Load the reference object with ARKit API
let refObjURL = Bundle.main.url(forResource: "flashlight", withExtension: ".referenceobject")
let refObject = try? await ReferenceObject(from: refObjURL!, configuration: configuration)
```

## Extended training mode via the command line

```bash
% xrun createml objecttracker --source flashlight.usdz --output flashlight.referenceobject --training-mode extended --all-angles
```

## Object pose spaces (rendered vs. metric)

```swift
// Obtain anchor transform with display corrections
let renderingPose = myObjectAnchor.coordinateSpace(correction: .rendered)

// Obtain anchor transform in metric space
let metricPose = myObjectAnchor.coordinateSpace(correction: .none)
```

## Object tracking on iOS

```swift
import ARKit
import RealityKit

class ObjectTrackingARSessionDelegate: NSObject, ARSessionDelegate {
    let arView = ARView(frame: .zero)
    var entities: [UUID: AnchorEntity] = [:]

    func start() throws {
        let stationaryObject = try ARReferenceObject(archiveURL:
            Bundle.main.url(forResource: "stationary", withExtension: "referenceobject")!)
        let movingObject = try ARReferenceObject(archiveURL:
            Bundle.main.url(forResource: "moving", withExtension: "referenceobject")!)

        let configuration = ARWorldTrackingConfiguration()
        configuration.detectionObjects = [stationaryObject]   // Low frame rate
        configuration.trackingObjects = [movingObject]        // High frame rate

        arView.session.delegate = self
        arView.session.run(configuration)
    }

    func session(_ session: ARSession, didAdd anchors: [ARAnchor]) {
        for case let anchor as ARObjectAnchor in anchors {
            let entity = AnchorEntity(anchor: anchor)
            entities[anchor.identifier] = entity
            arView.scene.addAnchor(entity)
        }
    }

    func session(_ session: ARSession, didUpdate anchors: [ARAnchor]) {
        for case let anchor as ARObjectAnchor in anchors {
            entities[anchor.identifier]?.isEnabled = anchor.isTracked
        }
    }

    func session(_ session: ARSession, didRemove anchors: [ARAnchor]) {
        for case let anchor as ARObjectAnchor in anchors {
            if let entity = entities.removeValue(forKey: anchor.identifier) {
                arView.scene.removeAnchor(entity)
            }
        }
    }
}
```

## Discover and connect a spatial accessory

```swift
import ARKit
import GameController

// Generic accessory discovery
if let device = GCSpatialAccessory.spatialAccessories.first {

    // Resolves the .referenceaccessory bundle automatically
    let accessory = try await Accessory(device: device)
    let provider = AccessoryTrackingProvider(accessories: [accessory])
    try await arkitSession.run([provider])
}

// Update tracked accessories without restarting the session
try await provider.updateAccessories([newAccessory])
```
