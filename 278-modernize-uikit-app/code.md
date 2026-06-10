# Code samples — Session 278

Extracted from the Code tab. Timestamps map to the talk's chapters.

## Use local screen references (replace `UIScreen.main`)

```swift
// Use local screen references
// Access the correct screen through a windowScene
let screen = window?.windowScene?.screen

// Pass in local screen references
func generateThumbnail(_ image: UIImage, screen: UIScreen) -> UIImage {
    // existing code, replacing main screen with local screen reference
    // ...
}
```

## Replace screen scale with `traitCollection.displayScale`

```swift
// Replace the screen's scale with trait collection's displayScale
override func layoutSubviews() {
    super.layoutSubviews()

    // layoutSubviews will be called again automatically when displayScale changes
    let displayScale = traitCollection.displayScale
    // ...
}
```

## Register for trait changes manually

```swift
// Manually register for trait changes
let displayScaleTrait: [UITrait] = [UITraitDisplayScale.self]
registerForTraitChanges(displayScaleTrait) {
    (view: GalleryView, previousTraitCollection: UITraitCollection) in
    view.cache.invalidate()
}
```

## Observe available space via effective geometry

```swift
// UIWindowSceneDelegate
func windowScene(
    _ windowScene: UIWindowScene,
    didUpdateEffectiveGeometry previousEffectiveGeometry: UIWindowScene.Geometry
) {
    let geometry = windowScene.effectiveGeometry
    let availableSpace = geometry.coordinateSpace.bounds
    // ...
}
```

## Check available space from the view (not the screen)

```swift
// Checking available space
override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()

    let availableSpace = view.bounds.size
    // ...
}
```

## Configure motion & heading Body protocols on a view

```swift
// Configure motion and heading bodies
override func viewDidLoad() {
    super.viewDidLoad()

    motionManager.deviceMotionBody = view
    locationManager.headingBody = view
}
```

## Opt an iPhone tab bar into a sidebar

```swift
tabBarController.sidebar.preferredPlacement = .sidebar
```

## Check whether the sidebar can be shown

```swift
tabBarController.sidebar.isAvailable
```

## Pin a prominent tab

```swift
// Set the prominent tab
let tabs = [
    // ...
]
let tabBarController = UITabBarController(tabs: tabs)
tabBarController.prominentTabIdentifier = "cart"
```

## Customize navigation bar minimization

```swift
// Customize bar minimization behavior
override init(
    nibName nibNameOrNil: String?,
    bundle nibBundleOrNil: Bundle?
) {
    super.init(nibName: nibNameOrNil, bundle: nibBundleOrNil)

    navigationItem.barMinimizationBehavior = .always
    navigationItem.barMinimizationSafeAreaAdjustment = .never
}
```

## Export the Xcode app-modernization skill

```bash
xcrun agent skills export
```

---

## Spoken APIs not in the Code tab

These were named in the transcript but have no dedicated snippet:

- `UISceneDelegate` / `UIWindowSceneDelegate` — required for scene lifecycle
- `UIRequiresFullscreen` (Info.plist key) — now enables discrete resizing for games
- Trait-tracked override points: `layoutSubviews`, `updateProperties`, `drawRect`
- `UITraitDisplayScale` — the trait observed for scale changes
- `UIScrollEdgeEffectStyle` values `.automatic` / `.soft` / `.hard` — navigation bar scroll edge effect
- `preferredImageVisibility` — on menu elements
- View Annotations API — annotate views with `AppEntity` for Siri
- Drag delegate `sessionWillBegin` / `sessionDidMove` — move stateful UI to `sessionDidMove`
