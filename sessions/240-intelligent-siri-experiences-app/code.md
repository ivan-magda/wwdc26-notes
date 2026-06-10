# Code samples — Session 240

Extracted from the Code tab of the session page. Labels derived from each snippet's content.

## Contributing message content with an indexed App Entity

```swift
// Contributing message content to Apple Intelligence

@AppEntity(schema: .messages.message)
struct MessageEntity: IndexedEntity {

    // The text content of the message
    @Property(indexingKey: \.textContent)
    var body: AttributedString?
}
```

## EntityStringQuery — resolve entities from arbitrary string input

```swift
// An interface that locates entities using arbitrary string input

struct ContactQuery: EntityStringQuery {
    func entities(matching string: String) async throws -> [ContactEntity] {
        let predicate = #Predicate<Person> { person in
            person.name.localizedStandardContains(string)
        }
        let descriptor = FetchDescriptor<Person>(predicate: predicate)
        let matches = try modelContext.fetch(descriptor)
        return matches.map(\.entity)
    }
}
```

## Onscreen awareness — view annotations for multiple visible items

```swift
// Working across apps - View annotations

List {
    ForEach(messages) { message in
        MessageRow(message: message)
            .appEntityIdentifier(
                EntityIdentifier(
                    for: MessageEntity.self,
                    identifier: message.id
                )
            )
    }
}
```

## Exporting content to another app with Transferable

```swift
// Working across apps - Exporting content to another app

extension ContactEntity: Transferable {

    static var transferRepresentation: some TransferRepresentation {
        IntentValueRepresentation(
            exporting: \.person
        )
    }
}
```

## IntentValueQuery — match incoming content to an existing entity

```swift
// Working across apps - IntentValueQuery

struct ContactEntityQuery: IntentValueQuery {

    func values(for input: [IntentPerson]) async throws -> [ContactEntity] {
        let names = input.map(\.displayName)
        let descriptor = FetchDescriptor<Contact>()
        let contacts = try model.mainContext.fetch(descriptor)
        let matches = contacts.filter { contact in
            names.contains(where: { name in
                contact.name.localizedStandardContains(name)
            })
        }
        return matches.map(\.entity)
    }
}
```

## IntentValueRepresentation(importing:) — create a new entity from incoming content

```swift
// Working across apps - IntentValueRepresentation

extension ContactEntity: Transferable {

    static var transferRepresentation: some TransferRepresentation {
        IntentValueRepresentation(exporting: \.person, importing: { intentPerson in
            let contact = Contact(importing: intentPerson)
            ContactManager.shared.contacts.append(contact)
            return contact.entity
        })
    }
}
```

---

## Useful API facts surfaced by the code

- `@AppEntity(schema: .messages.message)` macro applies a Messages-domain schema to an entity, and `@Property(indexingKey:)` marks which property is searchable in Spotlight's semantic index.
- `EntityStringQuery` requires `entities(matching:)` returning `[Entity]` — full control, no semantic understanding.
- `IndexedEntity` is the conformance that puts entities into the system semantic index for semantic resolution + content Q&A.
- Onscreen view annotation: `.appEntityIdentifier(EntityIdentifier(for: …, identifier: …))` ties a SwiftUI row to its entity for "this"/"that" resolution.
- Content transfer rides on the standard `Transferable` protocol via `IntentValueRepresentation(exporting:)`; add an `importing:` closure to create a new entity instead of resolving one.
- `IntentValueQuery.values(for:)` is the import-side match: takes intent parameter values (e.g. `[IntentPerson]`) and returns existing entities.
- `IntentPerson` is the cross-app exchange type for a contact/person; `displayName` is its readable label.
