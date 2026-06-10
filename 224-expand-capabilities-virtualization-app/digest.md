---
title: "Expand the capabilities of your Virtualization app — Full Digest"
session: WWDC26 · 224
url: https://developer.apple.com/videos/play/wwdc2026/224/
duration: 21m
speakers: Ronnie Misra
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 224 — Expand the capabilities of your Virtualization app

## TL;DR

Ronnie Misra walks through five ways to make a Virtualization app more capable, plus a
grab-bag of smaller wins at the end. The headline themes:

1. **Automate Virtual Mac setup** — new `VZMacGuestProvisioningOptions` programmatically
   drives Setup Assistant on first boot: creates a user account, optionally turns on
   auto-login and SSH. No more clicking through setup by hand. Great for CI/automation.
2. **Pass host USB devices into VMs** — the new **Accessory Access** framework lets a
   macOS or Linux guest claim a host USB accessory (e.g. a thumb drive), with explicit
   per-app user consent and hot plugging — no static VM config changes.
3. **Custom network topologies** — integrate the **vmnet** framework with Virtualization
   to control exactly how VMs talk to each other and the host: DHCP config, TCP/UDP port
   forwarding, shared networks across multiple VMs/processes.
4. **Efficient disk images** — **DiskImageKit** (macOS 27) brings layered, sparse ASIF
   images: base / cache / overlay layers, cheap copy-on-write snapshots, and read-only
   base layers shared across VMs.
5. **Custom Virtio devices** — `VZCustomVirtioDevice` (macOS 27) lets you build your own
   paravirtualized device (crypto coprocessor, ML accelerator, custom protocol) for
   low-latency, high-throughput host↔Linux-guest communication.

Most demos run on top of Apple's existing macOS virtual machine sample app.

---

## 1. macOS guest provisioning

Installing macOS into a virtual Mac still leaves you at Setup Assistant — the same flow
as a physical Mac. Fine interactively, painful for automation. The framework now lets you
attach **provisioning options** when you *start* a virtual Mac. You supply full name,
username, and password, and optionally enable auto-login or remote login via SSH; on the
guest's first boot these are fed straight into Setup Assistant.

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

In the demo, double-clicking the modified sample app boots a freshly-installed VM,
Setup Assistant silently creates the `jappleseed` account, auto-logs in, and System
Settings confirms Remote Login is on — all from one boot.

Two important caveats:

- **First boot only.** If the guest already has a user account, provisioning options on
  later boots are ignored.
- **Handle passwords carefully.** Don't hardcode — read from Keychain, a config file, or
  an environment variable, and think through the security implications.

## 2. Accessory Access (USB pass-through)

Some VM workflows need a host-connected USB accessory available inside the guest — e.g.
using a USB drive from within a VM — while keeping the user in control. **Accessory
Access** is a new framework for making USB devices available to **macOS and Linux** VMs.

Design principles:

- **Explicit per-app control** — users see which apps use which devices, and can attach
  or detach at any time.
- **Hot plugging** — granting access attaches the device at runtime without touching the
  VM's static configuration.

The demo: plug a USB drive into the Mac, an accessory menu-extra appears (because the app
registered interest in storage devices), pick the disk → the host unmounts it and the
guest mounts it. Eject from inside the VM, release via the menu → the host remounts.

Your app registers a listener with **matching criteria** (device class/subclass, vendor
ID, product ID, or other filters; an empty array means "all USB devices"):

```swift
import AccessoryAccess

let criteria: [AAUSBAccessoryMatchingCriteria] = []
let accessories = try await AAUSBAccessoryManager.shared.registerListener(self, matchingCriteria: criteria)

for accessory in accessories {
    // Handle previously attached accessories.
}
```

`registerListener` returns any accessories already attached to your app. When the user
attaches a new one, your listener's `usbAccessoryDidConnect` fires. VM mutations must run
on the VM's own queue, where you build a passthrough device and attach it to a USB
controller:

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

Requirements and etiquette:

- Add the **Claim USB Accessory** capability to your Xcode target.
- Users can detach at any time — handle those events gracefully.
- Check the Accessory Access documentation for supported device types.

## 3. Advanced network topologies (vmnet)

Virtualization makes basic NAT / bridge networking for isolated VMs easy, but advanced
workflows (e.g. testing a server VM from clients on the same or a different network) need
more control. Since **macOS 26**, the **vmnet** framework lets you build custom topologies
for macOS and Linux VMs: control how VMs communicate, configure DHCP, and add TCP/UDP host
port-forwarding rules.

The pipeline: create a vmnet **network configuration**, customize it, build a vmnet
**network** from it, wrap it in a **device attachment**, set that on a network device
configuration, add it to the VM configuration, then construct the VM. To put a second VM
on the same network, repeat the steps reusing the **same network object**.

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

Lifecycle gotcha: a vmnet network is a **reference-counted Objective-C object**. It
disappears when the last reference is released and is **not persisted** when your app
quits — if you want a consistent network, persist the settings yourself. To run VMs in
separate processes on the same network, transfer the network across XPC using
`vmnet_network_copy_serialization` / `vmnet_network_create_with_serialization`.

## 4. DiskImageKit (macOS 27)

Virtualization supports raw disk images — simple, widely compatible, one disk block per
file block. But raw images can't represent sparsity (a 100 GB disk is a 100 GB file) and
snapshots require copying the whole disk.

**DiskImageKit** (macOS 27) makes disk-image management efficient. It supports the **Apple
Sparse Image Format (ASIF)** introduced in macOS 26, and lets you build a **stack** of
images so writes land in an overlay while the base stays untouched. Raw images are still
supported.

Layer model:

- **Base layer** — bottom of the stack, any format DiskImageKit supports.
- **Upper layers** — always ASIF, either **cache** or **overlay**:
  - **Cache layer** improves performance when lower layers live on slow storage (e.g. a
    remote network filesystem). A miss is satisfied from below, then cached for next time.
  - **Overlay layer** implements copy-on-write for snapshots — writes land in the topmost
    writable overlay encountered while traversing the stack.
- **Read-only layers can be shared by multiple concurrent stacks**, so multiple VMs reuse
  shared content while keeping their writes separate.

ASIF images are sparse: they can logically represent more blocks than they store, and
unstored blocks read back as zero-filled. The walkthrough example: base has blocks 0, 1, 4;
cache is empty; overlay has updated block 4 and a new block 5. A read of block 0 traverses
down to the base, gets cached on the way back; a write of block 2 lands in the overlay.
(Layers may have different logical sizes.)

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

Practical notes:

- **Shallow stacks perform better** — deeper stacks cost you.
- A VM is more than disk images. A virtual Mac has an **auxiliary storage file**, and an
  EFI-boot VM has an **EFI variable store file**. Cloning a VM with a shared base layer
  means **duplicating those other files** too.

## 5. Custom Virtio devices (macOS 27)

The framework already ships a wide range of standard device classes, but some use cases
need something specialized: a custom performance-critical host↔guest protocol, a
coprocessor (e.g. a Virtio crypto device), or efficient guest access to ML accelerators.

**Virtio** is the industry standard for paravirtualized devices and underpins many of the
built-in Virtualization devices. In **macOS 27** you can implement **your own** Virtio
devices for custom communication with your **Linux** guests — ideal for low-latency,
high-throughput channels.

How it works: Virtio uses memory buffers shared between guest and host, organized into
**Virtio queues**, designed to minimize context switches. The guest driver notifies the
host device when it enqueues data; the host can fire an **interrupt** to notify the guest.

You configure a device with its Virtio device identity, PCI class/subclass, queue count,
and a delegate provider:

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

When the VM starts, the `VZCustomVirtioDevice` is created and the configuration delegate's
`didCreateDevice` fires — set the device's delegate (and keep a reference so you can
trigger guest interrupts later):

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

The device delegate's `didReceiveNotificationFor` is where you drain and process the
queue, returning each element when done:

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

Remember: **custom devices require a custom guest driver**, and you should follow Virtio
best practices in that driver to make optimal use of the queues.

## 6. Other advancements (briefly mentioned)

- **iCloud support** in the VM — valuable for desktop experiences; access iCloud data and
  services inside the guest.
- **EFI Secure Boot** — hardens Linux VMs with modern security features.
- **Metal features** for macOS guests — argument buffers and indirect command buffers.
