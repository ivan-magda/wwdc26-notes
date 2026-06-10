---
title: "Build real-time apps and services with gRPC and Swift — Full Digest"
session: WWDC26 · 265
url: https://developer.apple.com/videos/play/wwdc2026/265/
duration: 24m
speakers: George (Swift Server team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 265 — Build real-time apps and services with gRPC and Swift

## TL;DR

George from the Swift Server team takes one iOS go-karting app, "SwiftKart," from
static mock data to a live, server-fed experience using **gRPC Swift** end to end.

Four headline ideas:

1. **Stop hand-writing networking code.** Define the service once in a Protocol Buffers
   (`.proto`) spec — typed *functions* with typed request/response messages, not URLs —
   and a build plugin generates the Swift client and server. The spec is the source of
   truth, so the two sides can't drift.
2. **gRPC = OpenAPI's RPC sibling.** Same "generate code from a spec" idea (Apple's other
   talk, *Meet Swift OpenAPI Generator*, covers the HTTP/OpenAPI route), but APIs are
   modeled as remote procedure calls over HTTP/2 with a compact **binary** wire format
   (~half the size of equivalent JSON because fields are tagged by number, not name).
3. **Streaming is first class.** Beyond unary request/response there are client-streaming,
   server-streaming, and **bidirectional** RPCs. The demo's `FollowRace` is bidirectional:
   the app subscribes/unsubscribes to event types over time while the server pushes live
   kart positions and standings.
4. **Same language on both ends, all the way to prod.** The server is also Swift, built
   with the generated `SimpleServiceProtocol`, then containerised (multi-stage
   `swift:latest` → `swift:slim`) and deployed to **Google Cloud Run** with `--use-http2`,
   after which the client just flips `transportSecurity` from `.plaintext` to `.tls`.

gRPC Swift isn't a toy: it already underpins Apple's open-source **Containerization**
framework (host ↔ Linux VM over vsock), **Private Cloud Compute**, **iCloud Keychain/Photos**,
**SharePlay file sharing**, and internal OS build/release infrastructure.

---

## 1. Why generate code from a spec

Hand-crafting networking code against human docs is slow and brittle: docs go stale, you
make mistakes, and you end up with something that "seems to work." When a service is
instead defined by a separate **specification** that acts as the source of truth, you can
**generate** the code to talk to it — saving time and eliminating a whole class of errors,
and that payoff scales across every API you integrate.

For HTTP APIs the Swift answer is **OpenAPI** (see *Meet Swift OpenAPI Generator*). This
session takes the alternative: **gRPC**, a CNCF project and widely-adopted industry
standard. Same generate-from-spec workflow, but your API is expressed as **functions with
inputs and outputs** rather than HTTP verbs and paths.

A remote procedure call: the client sends a **request message**, the server runs the
function, and returns a **response message**. For the demo, `ListRaces(limit)` returns the
race schedule.

## 2. Defining the service in Protocol Buffers

gRPC services are specified in **Protocol Buffers** (Protobuf) `.proto` files. The first
cut defines one unary RPC plus its messages:

```protobuf
edition = "2024";

import "google/protobuf/timestamp.proto";

service SwiftKartService {
  rpc ListRaces(ListRacesRequest) returns (ListRacesResponse);
}

message ListRacesRequest {
  int32 limit = 1 [default = 100];
}

message ListRacesResponse {
  repeated Race races = 1;
}

message Race {
  string name = 1;
  string location = 2;
  google.protobuf.Timestamp start_time = 3;
  int32 laps = 4;
  string championship = 5;
}
```

Key Protobuf concepts on display:

- Each message field has a **unique field number** (the `= 1`, `= 2`…) — this is what
  travels on the wire, not the field name.
- Fields can have **defaults** (`limit` defaults to 100).
- `repeated` means a list.
- **Well Known Types** like `google.protobuf.Timestamp` are predefined and `import`ed.

## 3. Wiring up Xcode codegen

Two SPM dependencies:

- **`grpc-swift-nio-transport`** — high-performance networking on top of open-source SwiftNIO.
- **`grpc-swift-protobuf`** — provides the **`GRPCProtobufGenerator`** build plugin.

Add the plugin under the target's **Build Phases → Run Build Tool Plug-ins**. It scans the
target for `.proto` files and is steered by a small JSON config. Since this is an app, it
only needs clients and messages, not server code:

```json
{
    "generate": {
        "clients": true,
        "servers": false,
        "messages": true
    }
}
```

Recompiling generates the code. (You're prompted to **trust the plugin** the first time —
a security measure.)

## 4. Making the first (unary) call

Import the runtime, networking, and Protobuf modules, then call inside a SwiftUI `.task`:

```swift
import GRPCCore
import GRPCNIOTransportHTTP2
import SwiftProtobuf
```

`withGRPCClient` creates a client bound to a **transport**. The client only knows about the
*server*; it knows nothing about the *service* — that's what the generated
`SwiftKartService.Client(wrapping:)` adds:

```swift
.task {
    do {
        try await withGRPCClient(
            transport: .http2NIOTS(
                address: .ipv4(host: "127.0.0.1", port: 8080),
                transportSecurity: .tls
            )
        ) { client in
            let kart = SwiftKartService.Client(wrapping: client)
            let request = ListRacesRequest()
            let response = try await kart.listRaces(request)
            self.races = response.races.map { race in
                RaceInfo(
                    name: race.name,
                    location: race.location,
                    startTime: race.startTime.date,
                    championship: race.championship,
                    laps: Int(race.laps),
                    drivers: race.drivers
                )
            }
        }
    } catch {
        print("gRPC error: \(error)")
    }
}
```

The response maps cleanly onto the view's own `RaceInfo` model. `.http2NIOTS` is the
Network.framework-backed transport for Apple platforms.

## 5. Reusing the connection — a shared ClientManager

The naive version creates a fresh client (and therefore a fresh connection) every time a
view appears, adding latency. Better: create **one** client, share it through the SwiftUI
**environment**, and disconnect when the app backgrounds.

The `ClientManager` is an `@Observable`, `Sendable` class whose connection state lives in a
`Mutex`. It connects **lazily** on first use and exposes a `withClient { … }` entry point;
`disconnect()` pulls the client out of state and calls `beginGracefulShutdown()`:

```swift
@Observable
final class ClientManager: Sendable {
    fileprivate let state = Mutex(State.disconnected)

    func withClient(
        body: (_ client: GRPCClient<HTTP2ClientTransport.TransportServices>) async throws -> Void
    ) async throws {
        let client = try connectIfNecessary()
        try await body(client)
    }

    func disconnect() {
        let client = self.state.withLock { $0.disconnect() }
        client?.beginGracefulShutdown()
    }
}
```

Inject it at the app entry point and tear the connection down on `scenePhase` `.background`:

```swift
@main
struct SwiftKartApp: App {
    let manager = ClientManager()
    @Environment(\.scenePhase) private var scenePhase

    var body: some Scene {
        WindowGroup {
            RaceScheduleView().environment(manager)
        }
        .onChange(of: scenePhase) { _, newPhase in
            switch newPhase {
            case .background: manager.disconnect()
            case .inactive, .active: break
            @unknown default: break
            }
        }
    }
}
```

Views read it with `@Environment(ClientManager.self) var manager` and replace the raw
`withGRPCClient` call with `manager.withClient { … }`. Because it connects lazily, there's
nothing to do on `.active`.

## 6. The Protobuf wire format (why it's worth it)

Beyond the service API, Protobuf is also a **message interchange format**. SwiftProtobuf
generates Swift types for your messages, so you build and serialize them directly:

```swift
var race = Race()
race.name = "Duck Pond Dash"
race.location = "Apple Park, Cupertino"
race.startTime = .init(roundingTimeIntervalSince1970: 1_781_198_600)
race.laps = 6
race.championship = "Corporate Cup"
race.drivers = ["Monty", "Pepper", "Mycroft", "Pancakes", "Duke", "Kiko", "Sissi", "Bo"]

try race.serializedBytes()
```

On the wire, messages serialize to **binary** keyed by **field number** rather than field
name, making a Protobuf message **roughly half the size of the equivalent JSON**. That
matters most for **mobile apps on poor networks**, and it pays off equally in
service-to-service and interprocess communication.

That's not theoretical for Apple: gRPC Swift powers the open-source **Containerization**
framework (host ↔ Linux VM over virtual sockets), and cloud services including **Private
Cloud Compute**, **iCloud Keychain and Photos**, and **SharePlay file sharing**, plus
internal OS build/release systems.

## 7. The Swift server

The server mirrors the client's simplicity — a `GRPCServer` with a transport and a list of
services, then `serve()`:

```swift
let server = GRPCServer(
    transport: .http2NIOPosix(
        address: .ipv4(host: "127.0.0.1", port: 8080),
        transportSecurity: .plaintext
    ),
    services: [Service()]
)
try await server.serve()
```

A service is just a type conforming to the **generated** `SimpleServiceProtocol`. Each
unary RPC is an `async` function from request to response:

```swift
struct Service: SwiftKartService.SimpleServiceProtocol {
    private let database = RaceDB()

    func listRaces(
        request: ListRacesRequest,
        context: ServerContext
    ) async throws -> ListRacesResponse {
        var response = ListRacesResponse()
        response.races = await database.listRaces(atMost: request.limit)
        return response
    }
}
```

Note `.http2NIOPosix` here (vs. the client's `.http2NIOTS`) — the POSIX transport is what
runs on Linux servers.

## 8. The four RPC types, and a bidirectional stream

`ListRaces` is a **unary** RPC: one request, one response. gRPC's standout feature is
**first-class streaming**, which yields three more shapes:

- **Client-streaming** — many request messages, one response (e.g. each kart streaming
  telemetry up to the server).
- **Server-streaming** — one request, many responses (e.g. a live text commentary feed).
- **Bidirectional** — both sides stream freely.

The live-updates feature is **bidirectional**: the client tells the server which event
types it's subscribed to and can change that subscription over time; the server streams
back the matching events.

### Defining FollowRace

The `stream` keyword on input and output makes it bidirectional. Note the `enum` for event
types and the **`oneof`** response (Protobuf's tagged union — "just like a Swift enum with
associated values"):

```protobuf
service SwiftKartService {
  rpc ListRaces(ListRacesRequest) returns (ListRacesResponse);
  rpc FollowRace(stream FollowRaceRequest) returns (stream FollowRaceResponse);
}

message FollowRaceRequest {
  string race_name = 1;
  repeated RaceEventType event_types = 2;
}

enum RaceEventType {
  RACE_EVENT_TYPE_UNSPECIFIED = 0;
  RACE_EVENT_TYPE_KART_LOCATIONS = 1;
  RACE_EVENT_TYPE_STANDINGS = 2;
}

message FollowRaceResponse {
  oneof event {
    KartLocations locations = 1;
    Standings standings = 2;
  }
}
```

(The `Race` message also gains a `repeated string drivers = 6;` field at this point.)

### Implementing it on the server

A streaming RPC's signature differs from the unary one: the request is an
`RPCAsyncSequence` to **read** from, and the response is an `RPCWriter` to **write** to.
Because the server must consume incoming subscription changes *and* push events at the same
time, it uses a **task group**, with the current subscription set guarded by a `Mutex`
(two tasks touch it concurrently). Ending the request stream is the signal to cancel and
stop sending:

```swift
func followRace(
    request: RPCAsyncSequence<FollowRaceRequest, any Error>,
    response: RPCWriter<FollowRaceResponse>,
    context: ServerContext
) async throws {
    try await withThrowingTaskGroup { group in
        var iterator = request.makeAsyncIterator()
        guard let first = try await iterator.next() else { return }
        let eventTypes = Mutex(Set(first.eventTypes))

        group.addTask {
            let events = tracker.events(forRace: first.raceName).filter { event in
                eventTypes.withLock { $0.contains(event.type) }
            }
            for await event in events {
                var message = FollowRaceResponse()
                switch event {
                case .locations(let locations): /* map → KartLocations.Kart */ break
                case .standings(let standings):  /* map → Standings.Entry */    break
                }
                try await response.write(message)
            }
        }

        while let next = try await iterator.next() {
            eventTypes.withLock { $0 = Set(next.eventTypes) }
        }

        group.cancelAll()
    }
}
```

(Full mapping body is in `code.md`.) Restart the server and the new RPC is live.

### Calling it from SwiftUI

The generated `followRace` takes **two closures**: one that **writes** request messages,
one that **handles** the response stream. To send a fresh subscription whenever the
leaderboard toggles, the view bridges the SwiftUI `showLeaderboard` state into an
`AsyncStream<Bool>` (storing its continuation, yielding on `.onChange`) and iterates that
stream inside the request closure:

```swift
.task {
    let (stream, continuation) = AsyncStream.makeStream(of: Bool.self)
    self.continuation = continuation
    continuation.yield(showLeaderboard)

    try await manager.withClient { client in
        let kart = SwiftKartService.Client(wrapping: client)
        try await kart.followRace { requestStream in
            for await showLeaderboard in stream {
                var message = FollowRaceRequest()
                message.raceName = race.name
                message.eventTypes = [.kartLocations]
                if showLeaderboard { message.eventTypes.append(.standings) }
                try await requestStream.write(message)
            }
        } onResponse: { responseStream in
            for try await message in responseStream.messages {
                if let event = message.event { await handleEvent(event) }
            }
        }
    }
}
```

`handleEvent` switches over the `oneof` (`FollowRaceResponse.OneOf_Event`) and updates the
map annotations or the standings list. The demo shows karts moving around Apple Park in
real time, with a live leaderboard.

## 9. Deploying to the cloud

The server runs locally so far. To make it available to everyone, deploy to Linux. **No
code changes** — just package and ship:

1. **Containerfile** — a multi-stage build. Compile in `swift:latest`, then copy *only* the
   binary into a slim `swift:slim` runtime image so you don't ship the whole toolchain:

   ```dockerfile
   FROM swift:latest AS builder
   WORKDIR /app
   COPY Package.swift Package.resolved .
   COPY Sources/ Sources/
   RUN swift build -c release --product server
   RUN cp "$(swift build -c release --show-bin-path)/server" /usr/bin/server

   FROM swift:slim
   COPY --from=builder /usr/bin/server /usr/bin/server
   EXPOSE 8080
   ENTRYPOINT ["/usr/bin/server"]
   ```

2. **Deploy** — the demo uses Google Cloud Run (AWS, Fly.io, etc. work similarly). gRPC
   needs HTTP/2, hence `--use-http2`:

   ```bash
   gcloud run deploy wwdc-demo-server \
     --image us-central1-docker.pkg.dev/wwdc26/wwdc-demo-server/wwdc-demo-server:latest \
     --region us-central1 \
     --use-http2 \
     --allow-unauthenticated
   ```

3. **Point the client at it** — swap the transport target to the deployed DNS name and flip
   security to TLS:

   ```swift
   static func makeTransport() throws -> HTTP2ClientTransport.TransportServices {
       try .http2NIOTS(
           target: .dns(host: "wwdc-demo-server-863666503339.us-central1.run.app"),
           transportSecurity: .tls
       )
   }
   ```

Now spectators on the deployed app see the live Finite Loops race.

## 10. Next steps & what's beyond the demo

gRPC Swift is "prototype to production." Beyond the basics, the talk name-drops:

- Integration with other Swift packages: **Swift OTel** (observability) and
  **Swift Service Lifecycle**.
- Advanced connection management: **custom transports**, **name resolvers**, and
  **client-side load balancing**.
- The project is **open source** on GitHub with tutorials and examples, and welcomes
  contributions.
