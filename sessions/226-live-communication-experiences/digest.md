---
title: "Create live communication experiences — Full Digest"
session: WWDC26 · 226
url: https://developer.apple.com/videos/play/wwdc2026/226/
duration: 17m
speakers: Yaseen (Apple)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 226 — Create live communication experiences

## TL;DR

**LiveCommunicationKit** is the modern, system-integrated way to build live audio/video
conversation apps. Adopt it and your app's conversations get the *same* native UI as the
phone: a full-screen Lock Screen presentation with name/photo/controls, the Dynamic
Island, Phone-app Recents, contact details, and Siri redial.

Four ideas carry the whole talk:

1. **One conversation object** — a `Handle`-and-capabilities value that lives only while
   someone is in it. Capabilities (`.video`, `.pausing`, `.merging`, `.unmerging`) decide
   which system controls and gestures appear, and can change mid-conversation.
2. **One manager, one delegate, one code path** — you report state/events *up* through
   `ConversationManager`; the system sends `ConversationAction`s *down* to your delegate.
   Whether an interaction starts from system UI or your own buttons, it lands in the same
   `perform(action:)` switch — no duplicated state, no app/system drift.
3. **A simple lifecycle** — idle → joining → joined → (pause/resume) → leaving → left.
4. **It replaces CallKit's `CXProvider`** — "now is a great time to move over," per Apple.

The running example is a group of college friends (David, Ryan, Andre, Adam) planning an
Iceland reunion trip on an audio conversation app.

---

## 1. Core concepts: handles, capabilities, lifecycle

A **conversation** represents a single real-time interaction between people and exists
only as long as someone is in it — when everyone leaves, it's gone. It has two parts:

**Handles** identify the people. Each handle has three properties:

- **kind** — `.phoneNumber`, `.emailAddress`, or a generic string. Setting the right kind
  lets the system match the handle to a saved contact and show that contact's name + photo
  in the system UI.
- **value** — the identifier itself (phone/email/string). This is what the system uses to
  look up the contact and what your app gets back when someone redials from Recents.
- **display name** — the fallback the system shows when it can't match a contact. Set it to
  whatever name your app already knows so the UI always has something to display.

**Capabilities** tell the system what the conversation can do, so the conversation UI only
offers what your app actually supports:

- The standard in-conversation controls are **Mute, Speaker, Keypad, More**.
- `.pausing` → a long-press on Mute holds the conversation; without it the long-press does
  nothing.
- `.video` → marks this as a video conversation. (The video *button* itself is enabled by
  the manager's `supportsVideo` configuration, covered later.)
- Capabilities can change over a conversation's life — e.g. an audio call upgrading to
  video — and the system reflects updates immediately.

**Lifecycle states:**

- **idle** — your app first reports the conversation; the device starts ringing. App can
  begin local setup.
- **joining** — person answers; UI shows "connecting" while your app finishes setup. No
  audio/video capture yet.
- **joined** — setup done; your app starts capturing/sending media; conversation is live.
  Route changes (AirPods, car Bluetooth) arrive as notifications so you can update capture.
- **pause/resume** — with `.pausing`, the system's hold control asks your app to pause its
  media streams and report the new state, then to resume on the way back.
- **leaving** — conversation ends; your app tears down connections.
- **left** — teardown complete; the conversation is over.

## 2. Architecture: ConversationManager + delegate + actions

- **`ConversationManager`** is how your app reports conversations and events *to* the
  system. Every new conversation or change goes through it — that's what drives the Lock
  Screen, Dynamic Island, and everywhere else.
- **The delegate** is where your app responds. When something needs to happen, the system
  delivers a **`ConversationAction`** to a delegate method and your app does the work to
  fulfill it.
- The unification is the key idea: a system-UI interaction (accept from Lock Screen, end
  from Dynamic Island) *and* an in-app button tap both flow through the **same delegate
  callback**, so there's exactly one place for each action's logic — no duplicated state,
  no app/system desync.

Create the manager once at launch (it's needed for the app's lifetime) with a
configuration:

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

What the configuration controls:

- **ringtone + icon** the system presents alongside your conversations across system UI.
- **group limits** — `maximumConversationGroups` and `maximumConversationsPerConversationGroup`
  cap how many merged groups can exist and how many conversations each holds.
- **`includesConversationInRecents`** — whether conversations appear in Phone Recents; pass
  `false` for something like a one-time room that doesn't support redialing.
- **`supportsVideo`** — enables the system video button.
- **`supportedHandleTypes`** — which handle kinds your app supports.

The configuration can be updated at any time during the app's lifetime.

> Background: to keep conversations alive when backgrounded or the device is locked,
> register the **Audio** and **Voice over IP** background modes in the app target's
> capabilities in Xcode.

## 3. Incoming conversations (PushKit → Lock Screen)

Flow: the caller's app builds a payload of two fields — a **handle** (caller's phone
number) and a **conversation UUID** — sends it to your server, which forwards a VoIP push
to the recipient's device.

**PushKit** wakes the app and delivers the payload to the delegate. Critical constraint:
your app **must report the conversation before the delegate method returns**, or the system
terminates the app.

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

Once reported, the system rings and shows the full-screen Lock Screen presentation. When
the person slides to answer, the system delivers a **`JoinConversationAction`**. Every
answer/pause/merge arrives as an action, all routed through one `perform(action:)`:

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
    default:
      action.fail()
    }
  }
}
```

Handling a join: verify the manager tracks a matching conversation (else `fail()`), report
that it started connecting (→ joining state), then do async server/media setup inside a
`Task` to keep the delegate responsive, report connected, and `fulfill`. On failure, fail
the action so the system can clean up its side.

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

Ending: tapping End moves the conversation to leaving, the system sends an
**`EndConversationAction`**, your app tears down the media stream and fulfills it, and the
conversation disappears from system UI.

## 4. Outgoing conversations (in-app, Siri, Recents)

When a conversation starts *inside* your app, report it to the system so people can keep
talking while using other apps. You build a **`StartConversationAction`** and call
`manager.perform`; the manager updates the system UI then forwards the action to your
delegate — handled the same way as a join.

```swift
let startAction = StartConversationAction(
  conversationUUID: UUID(),
  handles: [Handle(type: .phoneNumber, value: "+1-650-555-0199", displayName: "Ryan Notch")],
  isVideo: false
)

try await manager.perform([startAction])
```

Add `StartConversationAction` to the same delegate switch (route start actions to
`handleStartAction`). After the action is handled, the recipient answers, or your app
reports the conversation went unanswered or failed.

**Redial from Spotlight / Recents / Siri:**

- Support the **start-call intent**, delivered to your app's scene to continue as an
  **`NSUserActivity`**.
- When conversations are saved to Recents, Apple Intelligence already knows about them, but
  to surface your app's own representation, **donate your own intent at the end of each
  conversation**.
- Use **stable handles** (not transient tokens) so redialing works.
- Deep-dive: **"Get to know App Intents."**

## 5. Group conversations

Groups bring in multiple participants and track two member lists:

- **`members`** — everyone who's been invited (tells the system how many participants).
- **`activeRemoteMembers`** — only those with media actively flowing (which ones are
  actually sending).

Start a group by creating a handle per participant and one `StartConversationAction` with
all invited members:

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

As people join, report an update naming the `localMember`, the new
`activeRemoteMembers`, and the supported capabilities (including `.merging` / `.unmerging`):

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

### Merging and unmerging

When two parallel conversations exist (the main group, plus a side conversation), declaring
the `.merging` capability enables the system's merge control. Tapping it delivers a
**`MergeConversationAction`** carrying two UUIDs — `conversationUUID` and
`conversationUUIDToMergeWith`. Add it to the delegate switch, then look up both local
conversations, combine the streams on your server, report the updated membership, and
fulfill (fail if either conversation is missing or anything throws):

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

**Unmerging follows the identical delegation pattern** — once you've written the merge
handler, the unmerge one is familiar. It lets a subset split back into their own
conversation while others continue.

## Next steps (Apple's closing checklist)

- Adopt `ConversationManager` and report your first incoming conversation on the Lock
  Screen.
- Donate intents so Siri knows how to start conversations.
- Replace transient tokens with stable handles for Recents redialing.
- Keep conversation membership updated.
