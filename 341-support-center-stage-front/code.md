# Code samples — Session 341

Extracted from the Code tab. Timestamps map to the transcript chapters.

## Select the camera and set a dynamic aspect ratio

```swift
// Select the Center Stage front camera

import AVFoundation

let deviceDiscoverySession = AVCaptureDevice.DiscoverySession(deviceTypes: [.builtInUltraWideCamera], mediaType: .video, position: .front)

guard let camera = deviceDiscoverySession.devices.first else {
    print("Failed to find the capture device")
    return
}

// Find a format that supports the 4x3 aspect ratio

for format in camera.formats {
    if format.supportedDynamicAspectRatios.contains(.ratio4x3) {
        try! camera.lockForConfiguration()
        camera.activeFormat = format
        camera.unlockForConfiguration()
        break
    }
}

// Set dynamic aspect ratio

try! camera.lockForConfiguration()

let timestamp = try! await camera.setDynamicAspectRatio(.ratio4x3)
print("Applied dynamic aspect ratio at timestamp: \(timestamp)")

camera.unlockForConfiguration()
```

## Configure and run the smart framing monitor (Auto Zoom / Auto Rotate)

```swift
// Find a format that supports smart framing

import AVFoundation

for format in camera.formats {
    if format.isSmartFramingSupported {
        try! camera.lockForConfiguration()
        camera.activeFormat = format
        camera.unlockForConfiguration()
        break
    }
}

// Configure the smart framing monitor

let monitor = camera.smartFramingMonitor!

try! camera.lockForConfiguration()
monitor.enabledFramings = monitor.supportedFramings
camera.unlockForConfiguration()

// Monitor framing recommendations

observation = monitor.observe(\.recommendedFraming, options: [.new,]) { monitor, change in
    if let framing = monitor.recommendedFraming {

        Task {
            try! camera.lockForConfiguration()
            try! await camera.setDynamicAspectRatio(framing.aspectRatio)
            camera.videoZoomFactor = CGFloat(framing.zoomFactor)
            camera.unlockForConfiguration()
        }

    }
}

// Start the smart framing monitor

try! monitor.startMonitoring()

// Stop the smart framing monitor

observation?.invalidate()
observation = nil

monitor.stopMonitoring()
```

## Enable Center Stage for video calls

```swift
// Find a format that supports Center Stage

import AVFoundation

for format in camera.formats {
    if format.isCenterStageSupported {
        try! camera.lockForConfiguration()
        camera.activeFormat = format
        camera.unlockForConfiguration()
        break
    }
}

// Turn on Center Stage

AVCaptureDevice.centerStageControlMode = .cooperative
AVCaptureDevice.isCenterStageEnabled = true
```

---

## Useful API facts surfaced by the code + transcript

- Camera is `AVCaptureDevice` with `.builtInUltraWideCamera` + `.front`.
- Dynamic aspect ratio: `format.supportedDynamicAspectRatios` (e.g. `.ratio4x3`); `await camera.setDynamicAspectRatio(_)` returns the timestamp of the first buffer where the change takes effect.
- Five aspect ratios: 3x4, 4x3, 9x16, 16x9, 1x1. Square formats only (1280–4032); 4032 photo format supports only 3x4 / 4x3.
- Smart framing: `format.isSmartFramingSupported`, `camera.smartFramingMonitor`, `enabledFramings` / `supportedFramings`, KVO on `recommendedFraming` (gives `aspectRatio` + `zoomFactor`); requires the 4032 photo format. Apply ratio first, then zoom, for a smooth transition. `startMonitoring()` / `stopMonitoring()`.
- Center Stage: `format.isCenterStageSupported`, `AVCaptureDevice.centerStageControlMode` (`.cooperative` / `.app`), `AVCaptureDevice.isCenterStageEnabled`.
- Sensor orientation compensation: `cameraSensorOrientationCompensationEnabled` on `AVCapturePhotoOutput` (HEIC/JPEG/uncompressed only; never RAW/ProRAW).
- Video stabilization: `connection.preferredVideoStabilizationMode = .lowLatency`; recordings also support `cinematicExtended` / `cinematicExtendedEnhanced`.
