# Session 265 — Build real-time apps and services with gRPC and Swift

- **URL:** https://developer.apple.com/videos/play/wwdc2026/265/
- **Duration:** 24m
- **Speakers:** George (Swift Server team)

## Description

Build real-time experiences in your apps and services with gRPC Swift. Instead of
hand-crafting networking code, define your service API as typed functions in a
Protocol Buffers (`.proto`) specification and let the `GRPCProtobufGenerator` build
plugin generate the Swift client and server code. The session walks an iOS go-karting
app from static mock data to live, server-fetched content: a unary `ListRaces` request,
a bidirectional-streaming `FollowRace` RPC that pushes live kart positions and
standings, and finally containerising the Swift server and deploying it to Google
Cloud Run over TLS.

## Key topics

- gRPC as a CNCF-standard remote procedure call framework — APIs defined as typed
  functions (not HTTP endpoints), code generated from a Protobuf spec
- Protocol Buffers: messages, field numbers, default values, Well Known Types
  (`google.protobuf.Timestamp`, `google.protobuf.Duration`), `repeated`, `enum`, `oneof`
- Xcode setup: `grpc-swift-nio-transport` + `grpc-swift-protobuf` packages,
  `GRPCProtobufGenerator` Run Build Tool plugin, JSON config (`clients`/`servers`/`messages`)
- `withGRPCClient` + generated `SwiftKartService.Client(wrapping:)` for unary calls
- Connection reuse via a shared `ClientManager` in the SwiftUI environment; lazy connect,
  `beginGracefulShutdown()` on `scenePhase` `.background`
- Protobuf binary format ~half the size of equivalent JSON (field numbers over names)
- Four RPC types: unary, client-streaming, server-streaming, bidirectional streaming
- Server-side bidirectional streaming with `RPCAsyncSequence`, `RPCWriter`, task groups,
  `Mutex`-protected subscription state
- `SimpleServiceProtocol` server implementation; `GRPCServer` + `.http2NIOPosix` transport
- Containerising a Swift server (multi-stage `swift:latest` → `swift:slim`) and deploying
  to Google Cloud Run (`gcloud run deploy --use-http2`), switching client to TLS
- gRPC Swift powers Apple's Containerization framework, Private Cloud Compute, iCloud
  Keychain/Photos, SharePlay file sharing, and internal OS build/release systems

## Related sessions to fetch (referenced in this talk)

- [ ] Meet Swift OpenAPI Generator (the HTTP/OpenAPI alternative, by teammate Si)

## Chapter summary (Summary tab)

- **0:00 Introduction** — Why hand-crafting networking code is error-prone, and how
  generating code from a service specification saves time and eliminates mistakes.
- **1:39 Meet gRPC** — CNCF-standard RPC framework using Protocol Buffers to define APIs
  as typed functions rather than HTTP endpoints.
- **2:13 App overview and demo setup** — A go-karting iOS app demo; gRPC will replace
  static mock data with live server-fetched content.
- **3:30 Defining the ListRaces RPC** — The RPC and its request/response messages defined
  in a `.proto` file: fields, field numbers, types, and Well Known Types.
- **4:30 Setting up Xcode to generate gRPC code** — Add `grpc-swift-nio-transport` and
  `grpc-swift-protobuf`; configure the `GRPCProtobufGenerator` build plugin.
- **7:50 Managing the gRPC client lifecycle** — Shared `ClientManager` reuses connections
  across views and disconnects when the app backgrounds, reducing latency.
- **9:36 Protobuf message format and binary efficiency** — Field numbers instead of names
  make messages roughly half the size of equivalent JSON.
- **12:33 Implementing a bidirectional streaming RPC** — `FollowRace` defined, implemented
  on the Swift server with async sequences and task groups, wired into the iOS app to
  stream live kart positions and standings.
- **20:11 Deploying the service** — Containerise and deploy the Swift server, then connect
  over TLS to the live production service.
- **23:11 Next steps** — Recap of the workflow; pointers to prototype your own
  integrations and explore the open-source GitHub repository.

## Code

See `code.md` — snippets extracted from the Code tab (proto definitions, client and
server Swift, Containerfile, deploy command).
