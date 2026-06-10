# Code samples — Session 226

Extracted from the Code tab. Timestamps map to the transcript chapters.

## Set up a ConversationManager (~6:42)

```swift
import LiveCommunicationKit

let configuration = ConversationManager.Configuration(
  ringtoneName: "SampleRingtone.caf",
  iconTemplateImageData: UIImage(named: "SampleIcon")?.pngData(),
  maximumConversationGroups: 1,
  maximumConversationsPerConversationGroup: 2,
  includesConversationInRecents: true,
  supportsVideo: true,
  supportedHandleTypes: [.phoneNumber, .emailAddress]
)

let manager = ConversationManager(configuration: configuration)
manager.delegate = self
```

## Report an incoming conversation from a VoIP push (~9:20)

```swift
import LiveCommunicationKit
import PushKit

final class SamplePushHandler: NSObject, PKPushRegistryDelegate {
  func pushRegistry(
    _ registry: PKPushRegistry,
    didReceiveIncomingVoIPPushWith payload: PKPushPayload,
    metadata: PKVoIPPushMetadata) async {

    guard let (handle, uuid) = parseConversationPayload(from: payload) else { return }

    let capabilities = [.video, .pausing, .merging]
    let update = Conversation.Update(members: [handle], capabilities: capabilities)
    try? await manager.reportNewIncomingConversation(uuid: uuid, update: update)
  }
}
```

## Implement the delegate (route actions) (~9:57)

```swift
import LiveCommunicationKit

final class SampleDelegate: ConversationManagerDelegate {
  func conversationManager(
    _ manager: ConversationManager,
    perform action: ConversationAction
  ) {
    switch action {
    case let action as JoinConversationAction:
      handleJoinAction(action)
    default:
      action.fail()
    }
  }
}
```

## Handle the join action (connect the media stream) (~10:13)

```swift
extension SampleDelegate {
 func handleJoinAction(_ action: JoinConversationAction) {
    guard let conversation = manager.conversations.first(where: {$0.uuid == uuid })else {
      return action.fail()
    }

    manager.reportConversationEvent(.conversationStartedConnecting(.now), for: conversation)

    Task {
      do {
        try await setupMediaStream(with: action.conversationUUID)
        manager.reportConversationEvent(.conversationConnected(.now), for: conversation)
        action.fulfill(dateConnected: .now)
      } catch {
        action.fail()
      }
    }
  }
}
```

## Route end actions (~11:17)

```swift
final class SampleDelegate: ConversationManagerDelegate {
  // …
  func conversationManager(
    _ manager: ConversationManager,
    perform action: ConversationAction
  ) {
    switch action {
    case let action as JoinConversationAction:
      handleJoinAction(action)
    case let action as EndConversationAction:
      handleEndAction(action)
    default:
      action.fail()
    }
  }
}
```

## Build a StartConversationAction (outgoing) (~12:14)

```swift
let startAction = StartConversationAction(
  conversationUUID: UUID(),
  handles: [Handle(type: .phoneNumber, value: "+1-650-555-0199", displayName: "Ryan Notch")],
  isVideo: false
)
```

## Perform the start action (~12:20)

```swift
try await manager.perform([startAction])
```

## Route start actions (~12:29)

```swift
final class SampleDelegate: ConversationManagerDelegate {
  // …
  func conversationManager(
    _ manager: ConversationManager,
    perform action: ConversationAction
  ) {
    switch action {
    case let action as JoinConversationAction:
      handleJoinAction(action)
    case let action as EndConversationAction:
      handleEndAction(action)
    case let action as StartConversationAction:
      handleStartAction(action)
    default:
      action.fail()
    }
  }
}
```

## Start a group conversation (~13:51)

```swift
let adam = Handle(type: .emailAddress,
                  value: "adam.halwani@icloud.com",
                  displayName: "Adam Halwani")
let david = Handle(type: .emailAddress,
                   value: "david@example.com",
                   displayName: "David Evans")
let ryan = Handle(type: .phoneNumber,
                  value: "+16505550199",
                  displayName: "Ryan Notch")

let startAction = StartConversationAction(
  conversationUUID: UUID(),
  handles: [david, ryan],
  isVideo: false
)
try await manager.perform([startAction])
```

## Report group membership updates (~14:04)

```swift
let update = Conversation.Update(
  localMember: adam,
  members: [david, ryan],
  activeRemoteMembers: [david, ryan],
  capabilities: [.merging, .pausing, .unmerging]
)

manager.reportConversationEvent(
  .conversationUpdated(update),
  for: conversation
)
```

## Route merge actions (~15:28)

```swift
final class SampleDelegate: ConversationManagerDelegate {
  func conversationManager(
    _ manager: ConversationManager,
    perform action: ConversationAction
  ) {
    switch action {
    case let action as JoinConversationAction:
      handleJoinAction(action)
    case let action as EndConversationAction:
      handleEndAction(action)
    case let action as StartConversationAction:
        handleStartAction(action)
    case let action as MergeConversationAction:
      handleMergeAction(action)
    default:
      action.fail()
    }
  }
}
```

## Handle the merge action (combine streams) (~15:43)

```swift
extension SampleDelegate {
  func handleMergeAction(_ action: MergeConversationAction) {
    let sourceUUID = action.conversationUUID
    let targetUUID = action.conversationUUIDToMergeWith
    guard manager.conversations.contains(where: { $0.uuid == sourceUUID }),
          manager.conversations.contains(where: { $0.uuid == targetUUID }) else {
      return action.fail()
    }

    Task {
      do {
        let update = try await combineStreams(from: sourceUUID, into: targetUUID)
        manager.reportConversationEvent(.conversationUpdated(update), for: target)
        action.fulfill()
      } catch {
        action.fail()
      }
    }
  }
}
```

---

## Useful API facts surfaced by the code

- `ConversationManager.Configuration` fields: `ringtoneName`, `iconTemplateImageData` (PNG data), `maximumConversationGroups`, `maximumConversationsPerConversationGroup`, `includesConversationInRecents`, `supportsVideo`, `supportedHandleTypes`.
- `Handle(type:value:displayName:)` — `type` is `.phoneNumber`, `.emailAddress`, or generic.
- Capabilities seen: `.video`, `.pausing`, `.merging`, `.unmerging`.
- Incoming: `manager.reportNewIncomingConversation(uuid:update:)` from the PushKit VoIP delegate.
- Events reported via `manager.reportConversationEvent(_:for:)` — `.conversationStartedConnecting(.now)`, `.conversationConnected(.now)`, `.conversationUpdated(update)`.
- Actions: `JoinConversationAction`, `EndConversationAction`, `StartConversationAction`, `MergeConversationAction` (carries `conversationUUID` + `conversationUUIDToMergeWith`); each has `.fulfill(...)` / `.fail()`.
- `Conversation.Update(localMember:members:activeRemoteMembers:capabilities:)` — `members` = all invited, `activeRemoteMembers` = media actively flowing.
- Outgoing/start is performed with `try await manager.perform([startAction])`.
