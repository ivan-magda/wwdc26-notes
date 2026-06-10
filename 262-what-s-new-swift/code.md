# Code samples — Session 262

Extracted from the Code tab (36 snippets). Grouped by topic; labels derived from
content. Apple's running example is a rocket-launch app. Minor typos in the source
extraction are preserved verbatim.

## Ignored Task error warning (before)

```swift
Task {
    do {
        try lander.fly(to: moon)
    }
    catch {
        lander.abort()
    }
}
```

## async in defer + saving a Task to check its error later

```swift
let landingTask = Task {
    try lander.fly(to: moon)
}

defer {
    await orbiter.rendezvous(with: lander)
}

try await orbiter.justHangOut(waitingFor: landingTask)
```

## weak let and ~Sendable

```swift
final class Spacecraft: Sendable {
    ...
    weak let dockedAt: SpaceStation?
    ...
}

class Mission: ~Sendable { ... }

class CrewedMission: Mission, @unchecked Sendable { ... }
```

## Second (internal) memberwise initializer for mixed-visibility structs

```swift
struct Briefing {
    internal var topic: String
    internal var scheduledAt: Date
    private  var attendees: [Person] = []
}

// Generated memberwise initializers:
// extension Briefing {
//     private init(topic: String, scheduledAt: Date, attendees: [Person] = []) {
//          self.topic = topic
//          self.scheduledAt = scheduledAt
//          self.attendees = attendees
//     }
//
//     internal init(topic: String, scheduledAt: Date) {
//          self.topic = topic
//          self.scheduledAt = scheduledAt
//          self.attendees = []
//     }
// }
```

## Availability boilerplate (before anyAppleOS)

```swift
extension Mission {
    @available(macOS 27, iOS 27, watchOS 27, tvOS 27, visionOS 27, *)
    func showStatus() { ... }

    @available(macOS 27, iOS 27, watchOS 27, visionOS 27, *)
    @available(tvOS, unavailable)
    func launch() { ... }

    #if os(macOS) || os(iOS) || os(watchOS) || os(tvOS) || os(visionOS)
    func makeLiveActivityWidget() -> some Widget { ... }
    #endif
}
```

## anyAppleOS availability + #if os(anyAppleOS)

```swift
extension Mission {
    @available(anyAppleOS 27, *)
    func showStatus() { ... }

    @available(anyAppleOS 27, *)
    @available(tvOS, unavailable)
    func launch() { ... }

    #if os(anyAppleOS)
    func makeLiveActivityWidget() -> some Widget { ... }
    #endif
}
```

## @diagnose: ignore / enable / promote-to-error

```swift
@diagnose(DeprecatedDeclaration, as: ignored, reason: "Flying with surplus hardware")
func makeApolloSoyuzMission() -> Mission {
    CrewedMission(
        rocket: makeSaturnIRocket(),
        payload: makeApolloCSM(),
        crew: [.daniellePoole, .nathanMorrison]
    )
}

@diagnose(StrictMemorySafety, as: warning)
func uplinkCommand(from receiver: inout Receiver, to computer: inout Computer) {
    let commandSize = receiver.receiveInt()
    receiver.withReceivedData(byteCount: commandSize) {
        computer.receiveUplinkedCommand($0)
    }
}

@diagnose(ErrorInFutureSwiftVersion, as: error)
func fetchPosition() -> (x: Double, y: Double, z: Double) {
    return self.rotation
}
```

## Module selector (::) — resolving name conflicts

```swift
import Rocket
import GiftShopToys

let rocket1 = SaturnV()            // could mean `Rocket::SaturnV` or `GiftShopToys::SaturnV`
let rocket2 = Rocket.SaturnV()     // prefers `Rocket::Rocket.SaturnV`
let rocket3 = Rocket::SaturnV()    // correctly finds `Rocket::SaturnV`
```

## Module selector on a member (disambiguating extension methods)

```swift
//
// Module Chemistry
//

public protocol Flammable { ... }

extension Flammable {
    /// Set `self` on fire.
    public func fire() { ... }
}

//
// Module HumanResources
//

import Chemistry

public protocol Employee { ... }

extension Employee {
    /// Remove `self` from job.
    public func fire() { ... }
}

public class LaunchPadTechnician: Employee, Flammable { ... }

//
// Module main
//

import HumanResources
import Chemistry

let launchPadTechnician = LaunchPadTechnician(...)

launchPadTechnician.HumanResources::fire()
```

## Task cancellation shield (before)

```swift
// Radio for help

extension Radio {
  func send(_ data: [UInt8] {
    if Task.isCancelled { return }
    // ...
  }
}

extension EmergencyTransponder {
  func sendSOS() {
    radio.send(makeSOSPacket())
  }
}
```

## Task cancellation shield (with withTaskCancellationShield)

```swift
// Radio for help

extension Radio {
  func send(_ data: [UInt8] {
    if Task.isCancelled { return }
    // ...
  }
}

extension EmergencyTransponder {
  func sendSOS() {
    withTaskCancellationShield {
    	radio.send(makeSOSPacket())
    }
  }
}
```

## mapKeyedValues (before — manual dictionary construction)

```swift
// Map values with keys

func makeCalendarDisplayNames(for missions: [Mission: LaunchWindow]) -> [Mission: String] {
    let new: [Mission: String] = .init(
        uniqueKeysWithValues: missions.lazy.map { mission, launchWindow in
            (mission, makeDisplayName(for: mission, in: launchWindow))
        }
    )
    return new
}
```

## mapKeyedValues (after)

```swift
// Map values with keys

func makeCalendarDisplayNames(for missions: [Mission: LaunchWindow]) -> [Mission: String] {
    missions.mapKeyedValues { mission, launchWindow in
        makeDisplayName(for: mission, in: launchWindow)
    }
}
```

## FilePath components

```swift
// FilePath handling macOS-named resources

var path: FilePath = "/var/www/static"
path.components.append("WWDC")
print(path.components)
// [ "var", "www", "static", "WWDC" ]

var path: FilePath = "/var/www/static/..namedresource/rsrc"
print(path.components)
// [ "var", "www", "static" ]
```

## Swift Testing — issue severity (.warning)

```swift
// Issue severity

@Test(arguments: allRockets)
func testBurn(rocket: Rocket) throws {
    rocket.burn(for: .seconds(150))
    let remaining = rocket.propellantKg / rocket.totalPropellantKg

    if remaining < 0.10 {
        Issue.record(
            "\(rocket.name) remaining fuel is below 10% reserve target",
            severity: .warning
        )
    }

    #expect(remaining > 0.02, "\(rocket.name) propellant critically low - abort")
}
```

## Swift Testing — Test.cancel

```swift
// Test Cancellation

@Test(arguments: allRockets)
func testBurn(rocket: Rocket) throws {
    // solid-fuel rocket engines can't be stopped
    if rocket.engineType == .solid {
        try Test.cancel("\(rocket.name) has solid fuel")
    }

    rocket.burn(for: .seconds(150))
    let remaining = rocket.propellantKg / rocket.totalPropellantKg

    if remaining < 0.10 {
        Issue.record(
            "\(rocket.name) remaining fuel is below 10% reserve target",
            severity: .warning
        )
    }

    #expect(remaining > 0.02, "\(rocket.name) propellant critically low - abort")
}
```

## XCTest interop — XCTAssert from Swift Testing

```swift
// XCTest interoperability: Using XCTest from Swift Testing

func checkedTransmitAndReceive(on radio: Radio,
                               packet: Packet,
                               expectedByteCount: Int) throws -> [UInt8] {
    try radio.transmit(bytes: packet.data)
    let bytes = try radio.receive()
    XCTAssertEqual(bytes.count, expectedByteCount)
    return bytes
}

@Test
func pingTest() throws {
    let radio = Radio()
    let bytes = try checkedTransmitAndReceive(on: radio, packet: .ping, expectedByteCount: 8)
    #expect(bytes == [0x00, 0x00, 0xf0, 0x37, 0x0f, 0xc7, 0x00, 0x01])
}
```

## XCTest interop — #expect from XCTestCase

```swift
// XCTest interoperability: Using Swift Testing from XCTest

class RadioTests: XCTestCase {
    func testPingPacketTransmission() {
        let radio = Radio()
        let bytes = try checkedTransmitAndReceive(on: radio,
                                                  packet: .ping,
                                                  expectedByteCount: 8)

        #expect(bytes == [0x00, 0x00, 0xf0, 0x36, 0x0f, 0xc7, 0x00, 0x02])
    }
}
```

## Subprocess 1.0 — line-by-line output streaming

```swift
// Subprocess output streaming

let result = try await Subprocess.run(.name("ls"),
                                      input: .none,
                                      output: .sequence,
                                      error: .string(limit:4096)) { execution in
		execution.standardOtput.strings().filter { $0.hasSuffix(".obj") }
}

for try await objectFiles in result.closureOutput {
  	print("Object file: \(objectFile)")
}
```

## ProgressManager — concurrency / subprogress

```swift
// Progress reporting - Concurrency

let manager = ProgressManager(totalCount: 100)
try await rocket.launch(mission.subprogress(assigningCount: 100))

extension Rocket {
    func launch(_ progress: consuming Subprogress? = nil) async throws {
        let stage = progress?.start(totalCount: 3)
        try await ignite(); stage?.complete(count: 1)
        try await liftoff(); stage?.complete(count: 1)
        try await stageSeparation(); stage?.complete(count: 1)
    }
}
```

## ProgressManager — observing fractionCompleted

```swift
// Progress reporting - progress reporting

let manager = ProgressManager(totalCount: 100)
try await rocket.launch(mission.subprogress(assigningCount: 100))

Task {
    for await update in Observations({ mission.fractionCompleted }) {
        print("🚀 Mission \(Int(update * 100))%")
    }
}
```

## ProgressManager — type-safe metadata

```swift
// Progress reporting - metadata

extension Rocket {
    func ascend(_ progress: consuming Subprogress) async throws {
        let stage = progress.start(totalCount: 3)
        stage.detlaV = 3_400; try await burn(); stage.complete(count: 1)
        stage.detlaV = 2_100; try await stageSeparation(); stage.complete(count: 1)
        stage.detlaV = 1_800; try await coast(); stage.complete(count: 1)
    }
}

print("Δv to orbit: \(mission.summary(of: \.deltaV)) m/s")
```

## Optimizer: generic histogram (baseline)

```swift
func histogram<Values>(of values: Values) -> [256 of Int] where Values: Sequence<UInt8> {
    var result = makeInts(randomized: false)

    for value in values {
        result[Int(value)] += 1
    }

    return result
}

func makeInts(randomized: Bool) -> [256 of Int] {
    if randomized {
        InlineArray { _ in Int.random(in: (.min)...(.max)) }
    } else {
        InlineArray(repeating: 0)
    }
}
```

## Optimizer: after inlining makeInts

```swift
func histogram<Values>(of values: Values) -> [256 of Int] where Values: Sequence<UInt8> {
    var result = if false {                                                  //
                     InlineArray { _ in Int.random(in: (.min)...(.max)) }    //
                 } else {                                                    // Inlined code
                     InlineArray(repeating: 0)                               //
                 }                                                           //

   for value in values {
        result[Int(value)] += 1
    }
    return result
}

func makeInts(randomized: Bool) -> [256 of Int] {
    if randomized {
        InlineArray { _ in Int.random(in: (.min)...(.max)) }
    } else {
        InlineArray(repeating: 0)
    }
}
```

## Optimizer: inlined and constant-folded

```swift
func histogram<Values>(of values: Values) -> [256 of Int] where Values: Sequence<UInt8> {
    var result = InlineArray(repeating: 0)    // Inlined and optimized code

   for value in values {
        result[Int(value)] += 1
    }
    return result
}

func makeInts(randomized: Bool) -> [256 of Int] {
    if randomized {
        InlineArray { _ in Int.random(in: (.min)...(.max)) }
    } else {
        InlineArray(repeating: 0)
    }
}
```

## @inline(never)

```swift
@inline(never)
func makeInts(randomized: Bool) -> [256 of Int] {
    if randomized {
        InlineArray { _ in Int.random(in: (.min)...(.max)) }
    } else {
        InlineArray(repeating: 0)
    }
}
```

## @inline(always)

```swift
@inline(always)
func makeInts(randomized: Bool) -> [256 of Int] {
    if randomized {
        InlineArray { _ in Int.random(in: (.min)...(.max)) }
    } else {
        InlineArray(repeating: 0)
    }
}
```

## Specialization (what the compiler generates implicitly)

```swift
func histogram<Values>(of values: Values) -> [256 of Int] where Values: Sequence<UInt8> {
    var result = makeInts(randomized: false)

    for value in values {
        result[Int(value)] += 1
    }

    return result
}

// Note: Specialized function doesn't actually have a directly callable name.
func `histogram of [UInt8]`(of values: [UInt8]) -> [256 of Int] {    //
    var result = makeInts(randomized: false)                         //
                                                                     //
    for value in values {                                            //
        result[Int(value)] += 1                                      // Specialized code
    }                                                                //
                                                                     //
    return result                                                    //
}                                                                    //
```

## @specialized(where Values == [UInt8])

```swift
@specialized(where Values == [UInt8])
func histogram<Values>(of values: Values) -> [256 of Int] where Values: Sequence<UInt8> {
    var result = makeInts(randomized: false)

    for value in values {
        result[Int(value)] += 1
    }

    return result
}

// Note: Specialized function doesn't actually have a directly callable name.
func `histogram of [UInt8]`(of values: [UInt8]) -> [256 of Int] {    //
    var result = makeInts(randomized: false)                         //
                                                                     //
    for value in values {                                            //
        result[Int(value)] += 1                                      // Specialized code
    }                                                                //
                                                                     //
    return result                                                    //
}                                                                    //
```

## Iterable protocol (noncopyable / non-escapable, batched spans)

```swift
protocol Iterable<Element, Failure>: ~Copyable, ~Escapable {
    associatedtype Element: ~Copyable
    associatedtype IterableIterator: IterableIteratorProtocol<Element, Failure>, ~Copyable, ~Escapable
    associatedtype Failure: Error = Never

    func makeIterableIterator() -> IterableIterator

    var underestimatedCount: Int { get }
}

protocol IterableIteratorProtocol<Element, Failure>: ~Copyable, ~Escapable {
    associatedtype Element: ~Copyable
    associatedtype Failure: Error = Never

    mutating func nextSpan(maximumCount: Int) throws(Failure) -> Span<Element>

    mutating func skip(by maximumOffset: Int) throws(Failure) -> Int
}
```

## UniqueBox with get/set (the copy-heavy version)

```swift
@safe public struct UniqueBox<Value>: ~Copyable {
    private let valuePointer: UnsafeMutablePointer<Value>

    public init(_ value: consuming Value) {
        valuePointer = UnsafeMutablePointer.allocate(capacity: 1)
        valuePointer.initialize(to: value)
    }

    public var value: Value {
        get { valuePointer.pointee }
        set { valuePointer.pointee = newValue }
    }

    deinit {
        valuePointer.deinitialize(count: 1)
        valuePointer.deallocate()
    }
}
```

## UniqueBox with borrow/mutate accessors (and ~Copyable Value)

```swift
@safe public struct UniqueBox<Value: ~Copyable>: ~Copyable {
    private let valuePointer: UnsafeMutablePointer<Value>

    public init(_ value: consuming Value) {
        valuePointer = UnsafeMutablePointer.allocate(capacity: 1)
        valuePointer.initialize(to: value)
    }

    public var value: Value {
        borrow { valuePointer.pointee }
        mutate { &valuePointer.pointee }
    }

    deinit {
        valuePointer.deinitialize(count: 1)
        valuePointer.deallocate()
    }
}
```

## Repeated dictionary lookup in a loop (baseline)

```swift
func updateCount<Key: Hashable>(
    for key: Key,
    from sets: [Set<Key>],
    in counts: inout [Key: Int]
) {
    for set in sets {
        if set.contains(key) {
            counts[key, default: 0] += 1
        }
    }
}
```

## Old workaround: hoist the lookup via an inout-param helper

```swift
func updateCount<Key: Hashable>(
    for key: Key,
    from sets: [Set<Key>],
    in counts: inout [Key: Int]
) {
    func updateCountImpl(count: inout Int) {
        for set in sets {
            if set.contains(key) {
                count += 1
            }
        }
    }

    updateCountImpl(count: &counts[key, default: 0])
}
```

## MutableRef: hold a dictionary lookup open across the loop

```swift
func updateCount<Key: Hashable>(
    for key: Key,
    from sets: [Set<Key>],
    in counts: inout [Key: Int]
) {
    var countRef = MutableRef(&counts[key, default: 0])

    for set in sets {
        if set.contains(key) {
            countRef.value += 1
        }
    }
}
```
