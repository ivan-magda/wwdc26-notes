# Code samples — Session 275

Extracted from the Code tab. Timestamps are approximate, mapped to the transcript.

## ~3:39 — Activity as a @Model (auto Observable conformance)

```swift
import Foundation
import SwiftData

// SwiftData automatically generates Observable conformance
@Model
class Activity {
    var name: String
    var isComplete: Bool = false
    var dateCreated = Date.now
    var dateEdited = Date.now
}
```

## ~5:48 — Making TripCollection Codable

```swift
enum TripCollection: String, CaseIterable, RawRepresentable, Codable {
    case springEscapes
    case summerVibes
    case fallGetaways
    case winterRetreats
}
```

## ~10:33 — Trip model with a to-many relationship and inlined thumbnail

```swift
import Foundation
import SwiftData

@Model
class Trip {
    var name: String
    var collection: TripCollection
  
    var photo: TripImage
    var thumbnailData: Data?
  
    @Relationship(deleteRule: .cascade, inverse: \Activity.trip)
    var activities: [Activity] = []
  
    private(set) var creationDate = Date.now
    var subtitle: String?
    var isComplete: Bool = false
}
```

## ~13:21 — App entry point with ModelContainer and seed data

```swift
import SwiftUI
import SwiftData

@main
struct WishlistApp: App {
    let container: ModelContainer = {
        do {
            let modelContainer = try ModelContainer(for: Trip.self, Activity.self, TripImage.self, Goal.self, TripGoal.self, ActivityGoal.self)
            try SampleData.seedIfNeeded(in: modelContainer.mainContext)
            return modelContainer
        } catch {
            fatalError("Could not create model container: \(error)")
        }
    }()

    var body: some Scene {
        WindowGroup {
            ContentView()
                .preferredColorScheme(.dark)
        }
        .modelContainer(container)
    }
}
```

## ~16:27 — GoalsView: two @Query macros with predicates

```swift
@Query(filter: #Predicate<Goal> { $0.isAchieved }, sort: \Goal.dateAchieved, order: .reverse)
private var achievedGoals: [Goal]

@Query(filter: #Predicate<Goal> { !$0.isAchieved }, sort: \Goal.sortOrder)
private var upcomingGoals: [Goal]
```

## ~16:58 — RecentTripsPageView: FetchDescriptor with fetchLimit

```swift
import SwiftUI
import SwiftData

struct RecentTripsPageView: View {
    // Fetch most recent trips in reverse chronological order
    @Query(FetchDescriptor<Trip>(sortBy: [SortDescriptor(\Trip.creationDate, order: .reverse)], fetchLimit: 5))
    private var trips: [Trip]

    @Namespace private var namespace

    var body: some View {
        TabView {
            ForEach(trips) { trip in
                NavigationLink {
                    TripDetailView(trip: trip)
                        .navigationTransition(
                            .zoom(sourceID: trip.id, in: namespace))
                } label: {
                    TripImageView(trip: trip)
                        .overlay(alignment: .bottomLeading) {
                            VStack(alignment: .leading) {
                                Text("RECENTLY ADDED")
                                    .font(.subheadline)
                                    .fontWeight(.bold)
                                    .foregroundStyle(.limeGreen)

                                Text(trip.name)
                                    .font(.title)
                                    .fontWidth(.expanded)
                                    .fontWeight(.medium)
                                    .foregroundStyle(.primary)
                            }
                            .padding(.horizontal)
                            .padding(.bottom, 54)
                        }
                        .matchedTransitionSource(id: trip.id, in: namespace)
                }
                .buttonStyle(.plain)
            }
        }
        .tabViewStyle(.page)
        .containerRelativeFrame([.horizontal, .vertical]) { length, axis in
            if axis == .vertical {
                return length / 1.3
            } else {
                return length
            }
        }
    }
}
```

## ~17:35 — TripCollectionView: query built dynamically in the initializer

```swift
init(tripCollection: TripCollection, cardSize: TripCard.Size, namespace: Namespace.ID) {
    _trips = Query(filter: #Predicate<Trip> { $0.collection == tripCollection }, sort: \Trip.name)
    self.tripCollection = tripCollection
    self.cardSize = cardSize
    self.namespace = namespace
}
```

## ~18:12 — SearchResultsListView: predicate-driven trip + activity search

```swift
import SwiftUI
import SwiftData

private struct SearchResultsListView: View {
    @Query(sort: \Trip.name) private var trips: [Trip]
    @Query(sort: \Activity.name) private var activities: [Activity]

    var searchText: String
    var namespace: Namespace.ID

    init(searchText: String, namespace: Namespace.ID) {
        self.searchText = searchText
        self.namespace = namespace

        if searchText.isEmpty {
            _trips = Query(FetchDescriptor(sortBy: [SortDescriptor(\Trip.creationDate, order: .reverse)], fetchLimit: 3))
            _activities = Query(filter: #Predicate<Activity> { _ in false })
        } else {
            // All trips whose name matches searchText, sorted lexicographically
            let tripSearchPredicate = #Predicate<Trip> { $0.name.localizedStandardContains(searchText) }
            _trips = Query(filter: tripSearchPredicate, sort: \Trip.name)
            // All matching activities that belong to a trip
            let activitySearchPredicate = #Predicate<Activity> { $0.trip != nil && $0.name.localizedStandardContains(searchText) }
            _activities = Query(filter: activitySearchPredicate, sort: \Activity.name)
        }
    }

    var body: some View {
        List {
            if !trips.isEmpty {
                TripSearchSectionView(trips: trips, namespace: namespace, title: searchText.isEmpty ? "Recent Trips" : "Trips")
            }

            if !activities.isEmpty {
                ActivitySearchSectionView(activities: activities)
            }
        }
        .overlay {
            if trips.isEmpty && activities.isEmpty {
                ContentUnavailableView(
                    "No results for “\(searchText)”",
                    systemImage: "magnifyingglass",
                    description: Text("Check spelling or try a new search.")
                )
            }
        }
        .listStyle(.plain)
    }
}
```

## ~19:53 — Surfacing runtime errors with state + .alert(error:)

```swift
var body: some View {
    HStack(alignment: .firstTextBaseline, spacing: 17) {
        Group {
            if isEditing {
                rowContentWhenEditing
            } else {
                rowContentWhenNotEditing
            }
        }
        .transition(.opacity.animation(.snappy))
        .animation(.snappy, value: isEditing)
    }
    .onDisappear {
        do {
            try updateGoalAchievements()
        } catch {
            updateError = error
            reportError(error)
        }
    }
    .alert(error: $updateError) {
        // Customize the presentation of the error
    }
}
```

## ~21:08 — Re-enabling dateEdited side effects with withContinuousObservation

```swift
init(activity: Activity, isLast: Bool, isEditing: Bool) {
    activity.token = withContinuousObservation(options: .didSet) { event in
        _ = activity.name
        _ = activity.isComplete

        if event.matches(\Activity.name) {
            activity.dateEdited = .now
        }

        if event.matches(\Activity.isComplete) {
            activity.dateEdited = .now
            activity.trip?.isComplete = activity.trip?.activities.isEmpty == false
            && activity.trip?.activities.allSatisfy { $0.isComplete } == true
        }
    }
    self.activity = activity
    self.isLast = isLast
    self.isEditing = isEditing
}
```
