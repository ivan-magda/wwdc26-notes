# Code samples — Session 224

Extracted from the Code tab of the session page. Timestamps map to the
transcript chapters.

## 1:04 — macOS guest provisioning

```swift
import Virtualization

let provisioningOptions = VZMacGuestProvisioningOptions()
provisioningOptions.fullName = fullName
provisioningOptions.username = username
provisioningOptions.password = password
provisioningOptions.logsInAutomatically = true
provisioningOptions.enablesRemoteLogin = true

let startOptions = VZMacOSVirtualMachineStartOptions()
try startOptions.setGuestProvisioning(provisioningOptions)

try await virtualMachine.start(options: startOptions)
```

## 4:34 — Accessory Access: register a listener

```swift
import AccessoryAccess

let criteria: [AAUSBAccessoryMatchingCriteria] = []
let accessories = try await AAUSBAccessoryManager.shared.registerListener(self, matchingCriteria: criteria)

for accessory in accessories {
    // Handle previously attached accessories.
}
```

## 4:34 — Accessory Access: attach a connected device to the VM

```swift
import AccessoryAccess
import Virtualization

class AccessoryListener: NSObject, AAUSBAccessoryListener {
    func usbAccessoryDidConnect(_ usbAccessory: AAUSBAccessory) {
        virtualMachine.queue.async {
            do {
                let configuration = VZUSBPassthroughDeviceConfiguration(device: usbAccessory)
                let device = try VZUSBPassthroughDevice(configuration: configuration)
                self.virtualMachine.usbControllers.first?.attach(device: device) { error in
                    // Handle error if necessary...
                }
            } catch {
                // Handle error...
            }
        }
    }
}
```

## 8:26 — Advanced networking with vmnet

```swift
import Virtualization
import vmnet

var status: vmnet_return_t = .VMNET_FAILURE
guard let networkConfiguration =
    vmnet_network_configuration_create(.VMNET_SHARED_MODE, &status) else { ... }

guard let network =
    vmnet_network_create(networkConfiguration, &status) else { ... }

let attachment = VZVmnetNetworkDeviceAttachment(network: network)

let networkDeviceConfiguration = VZVirtioNetworkDeviceConfiguration()
networkDeviceConfiguration.attachment = attachment

virtualMachineConfiguration.networkDevices = [networkDeviceConfiguration]

let virtualMachine = VZVirtualMachine(configuration: virtualMachineConfiguration)
```

## 11:35 — DiskImageKit layered (stacked) disk image

```swift
import DiskImageKit
import Virtualization

let baseImage = try DiskImage(opening: .open(url: baseLayerURL, mode: .readOnly))
let cacheImage = try baseImage.appending(.asifLayer(url: cacheLayerURL, type: .cache))
let overlayImage = try DiskImage(opening: .open(url: overlayLayerURL))
let stackedImage = try cacheImage.appending(overlayImage)

let storageDeviceAttachment = try VZDiskImageStorageDeviceAttachment(diskImage: stackedImage)

let storageDeviceConfiguration =
    VZVirtioBlockDeviceConfiguration(attachment: storageDeviceAttachment)

virtualMachineConfiguration.storageDevices = [storageDeviceConfiguration]

let virtualMachine = VZVirtualMachine(configuration: virtualMachineConfiguration)
```

## 15:57 — Custom Virtio device configuration

```swift
import Virtualization

let deviceConfiguration = VZCustomVirtioDeviceConfiguration()

// Virtio entropy device.
deviceConfiguration.deviceID = 4
// PCI class for crypto devices.
deviceConfiguration.pciClassID = 0x10
// PCI subclass for network and computing encryption controllers.
deviceConfiguration.pciSubclassID = 0x00
// An entropy device uses a single Virtio queue.
deviceConfiguration.virtioQueueCount = 1

deviceConfiguration.provider =
    VZCustomVirtioDeviceDelegateProvider(deviceQueue: deviceQueue, delegate: provider)

virtualMachineConfiguration.customVirtioDevices = [deviceConfiguration]

let virtualMachine = VZVirtualMachine(configuration: virtualMachineConfiguration)
```

## 15:57 — Custom Virtio: configuration delegate (didCreateDevice)

```swift
import Virtualization

class DeviceConfigurationDelegate: NSObject, VZCustomVirtioDeviceConfigurationDelegate {
    func customVirtioConfiguration(_ deviceConfiguration: VZCustomVirtioDeviceConfiguration,
                                   didCreateDevice device: VZCustomVirtioDevice) {
        device.delegate = deviceDelegate
        self.device = device
    }
}
```

## 15:57 — Custom Virtio: device delegate (processing the queue)

```swift
import Virtualization

class DeviceDelegate: NSObject, VZCustomVirtioDeviceDelegate {
    func customVirtioDevice(_ device: VZCustomVirtioDevice,
                            didReceiveNotificationFor queue: VZVirtioQueue) {
        while let element = queue.nextElement() {
            // Process element...
            element.returnToQueue()
        }
    }
}
```

---

## Useful API facts surfaced by the code

- Provisioning: `VZMacGuestProvisioningOptions` exposes `fullName`, `username`, `password`, `logsInAutomatically`, `enablesRemoteLogin`; passed via `VZMacOSVirtualMachineStartOptions.setGuestProvisioning(_)`. **Honored on first boot only** — ignored if the guest already has a user.
- Accessory Access: empty `[AAUSBAccessoryMatchingCriteria]` matches all USB devices; `AAUSBAccessoryManager.shared.registerListener(_, matchingCriteria:)` returns already-attached accessories; VM mutations must run on `virtualMachine.queue`; attach via `usbControllers.first?.attach(device:)`. Requires the **Claim USB Accessory** capability.
- vmnet: `vmnet_network_create` → `VZVmnetNetworkDeviceAttachment` → `VZVirtioNetworkDeviceConfiguration`. The network is a reference-counted Obj-C object — **not persisted** when the app quits; serialize via `vmnet_network_copy_serialization` / `vmnet_network_create_with_serialization` to share across processes over XPC.
- DiskImageKit: `DiskImage` + `appending(_)` build a stack; layer types are `.asifLayer(url:type:)` with `.cache` / overlay; upper layers are always ASIF, base can be any supported format. **Shallow stacks perform better.** Cloning a VM with a shared base still requires duplicating the auxiliary storage file and the EFI variable store file.
- Custom Virtio: `VZCustomVirtioDeviceConfiguration` takes `deviceID`, `pciClassID`, `pciSubclassID`, `virtioQueueCount`, and a `VZCustomVirtioDeviceDelegateProvider`; queue work happens in `didReceiveNotificationFor` by draining `queue.nextElement()` and calling `element.returnToQueue()`. **Requires a custom guest driver.** macOS 27.
