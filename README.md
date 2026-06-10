# WWDC26 session notes

Notes on 100+ sessions of WWDC 2026 (June 8–12). One folder per session: Apple's full transcript, the session metadata, the code from the session's Code tab, and an LLM-written digest that condenses them into a few minutes of reading.

Start with a session's `digest.md`; open `transcript.md` when you need the speaker's exact wording.

## What's in a session folder

```
223-live-activities-essentials/
├── digest.md       LLM-written summary; start here
├── transcript.md   Apple's verbatim session transcript
├── meta.md         description, key topics, chapter summary
└── code.md         snippets from the session's Code tab
```

The frontmatter of every file links to its source page on developer.apple.com. Sessions without a Code tab have a `code.md` that says so instead of holding snippets.

## For LLMs

[`llms.txt`](llms.txt) indexes every digest with a one-line description, in the [llmstxt.org](https://llmstxt.org) format. Point an agent at that one file and it can pull the sessions it needs instead of crawling the whole repo.

## Apple's content and copyright

Transcripts, session descriptions, and code samples are Apple Inc.'s content. I reproduce them here for personal study and reference; the canonical versions live on [developer.apple.com](https://developer.apple.com/videos/wwdc2026/), linked from every file. The digests are derivative summaries of that content. This project has no affiliation with Apple.

## Sessions

The topic groups are mine, not Apple's.

### Keynote and event videos

- [102 · Platforms State of the Union](102-platforms-state-of-the-union/digest.md)
- [122 · WWDC26 Platforms State of the Union Recap](122-wwdc26-platforms-state-union/digest.md)
- [121 · Announcing Apple's next big step for Siri and iPhone](121-announcing-apple-next-big/digest.md)
- [394 · Get ready for WWDC26](394-ready-wwdc26/digest.md)
- [397 · Dub Dub Daily: Day 2](397-dub-dub-daily-day/digest.md)

### Apple Intelligence and Foundation Models

- [241 · What's new in the Foundation Models framework](241-foundation-models/digest.md)
- [242 · Build agentic app experiences with the Foundation Models framework](242-agentic-app-experiences-foundation/digest.md)
- [319 · Build with the new Apple Foundation Model on Private Cloud Compute](319-apple-foundation-model-private/digest.md)
- [339 · Bring an LLM provider to the Foundation Models framework](339-bring-llm-provider-foundation/digest.md)
- [334 · Build AI-powered scripts with the fm CLI and Python SDK](334-ai-powered-scripts-fm/digest.md)
- [243 · Debug and profile agentic app experiences with Instruments](243-debug-profile-agentic-app/digest.md)
- [298 · Meet the Evaluations framework](298-evaluations-framework/digest.md)
- [299 · Create robust evaluations for agentic apps](299-robust-evaluations-agentic-apps/digest.md)
- [335 · Improve your prompts by hill-climbing with Evaluations](335-improve-prompts-by-hill/digest.md)
- [237 · What's new in image understanding](237-image-understanding/digest.md)
- [297 · Best practices for integrating visual intelligence in your app](297-best-practices-integrating-visual/digest.md)
- [375 · Create high quality images using Image Playground](375-high-quality-images-image/digest.md)
- [246 · LLM search using Core Spotlight](246-llm-search-core-spotlight/digest.md)

### Siri, App Intents and Shortcuts

- [240 · Build intelligent Siri experiences with App Schemas](240-intelligent-siri-experiences-app/digest.md)
- [343 · Explore advanced App Intents features for Siri and Apple Intelligence](343-advanced-app-intents-features/digest.md)
- [344 · Code-along: Make your app available to Siri](344-code-along-app-available/digest.md)
- [345 · Discover new capabilities in the App Intents framework](345-capabilities-app-intents-framework/digest.md)
- [295 · Validate your App Intents adoption with AppIntentsTesting](295-validate-app-intents-adoption/digest.md)
- [310 · What's new in Shortcuts](310-what-s-new-shortcuts/digest.md)

### Machine learning and MLX

- [324 · Meet Core AI](324-core-ai/digest.md)
- [325 · Dive into Core AI model authoring and optimization](325-dive-core-ai-model/digest.md)
- [326 · Integrate on-device AI models into your app using Core AI](326-integrate-device-ai-models/digest.md)
- [232 · Run local agentic AI on the Mac using MLX](232-run-local-agentic-ai/digest.md)
- [233 · Explore distributed inference and training with MLX](233-distributed-inference-training-mlx/digest.md)
- [328 · Explore numerical computing in Swift with MLX](328-numerical-computing-swift-mlx/digest.md)
- [330 · Optimize custom machine learning operations with Metal tensors](330-optimize-custom-machine-learning/digest.md)

### Swift and SwiftData

- [262 · What's new in Swift](262-what-s-new-swift/digest.md)
- [265 · Build real-time apps and services with gRPC and Swift](265-real-time-apps-services/digest.md)
- [267 · Migrate to Swift Testing](267-migrate-swift-testing/digest.md)
- [274 · What's new in SwiftData](274-what-s-new-swiftdata/digest.md)
- [275 · Code-along: Add persistence with SwiftData](275-code-along-add-persistence/digest.md)

### SwiftUI and UI frameworks

- [269 · What's new in SwiftUI](269-what-s-new-swiftui/digest.md)
- [321 · Dive into lazy stacks and scrolling with SwiftUI](321-dive-lazy-stacks-scrolling/digest.md)
- [322 · Compose advanced graphics effects with SwiftUI](322-compose-advanced-graphics-effects/digest.md)
- [271 · Code-along: Build powerful drag and drop in SwiftUI](271-code-along-powerful-drag/digest.md)
- [272 · Use SwiftUI with AppKit and UIKit](272-swiftui-appkit-uikit/digest.md)
- [278 · Modernize your UIKit app](278-modernize-uikit-app/digest.md)
- [289 · Modernize your AppKit app](289-modernize-appkit-app/digest.md)
- [370 · Elevate your app's text experience with TextKit](370-elevate-app-text-experience/digest.md)
- [372 · Unwrap PaperKit](372-unwrap-paperkit/digest.md)
- [203 · Read between the strokes with PencilKit](203-read-between-strokes-pencilkit/digest.md)

### Developer tools

- [258 · What's new in Xcode 27](258-xcode-27/digest.md)
- [259 · Xcode, agents, and you](259-xcode-agents/digest.md)
- [227 · Create UI prototypes using agents in Xcode](227-ui-prototypes-agents-xcode/digest.md)
- [213 · Translate your app using agents in Xcode](213-translate-app-agents-xcode/digest.md)
- [260 · Get the most out of Device Hub](260-most-out-device-hub/digest.md)
- [261 · Build, deliver, and automate with Xcode Cloud](261-deliver-automate-xcode-cloud/digest.md)
- [268 · Profile, fix, and verify: Improve app responsiveness with Instruments](268-profile-fix-verify-improve/digest.md)
- [222 · Meet the new MetricKit](222-meet-new-metrickit/digest.md)

### visionOS and spatial computing

- [287 · Build next-generation experiences with visionOS 27](287-next-generation-experiences-visionos/digest.md)
- [279 · Explore advances in RealityKit](279-advances-realitykit/digest.md)
- [234 · Design immersive environments for visionOS apps and the spatial web](234-immersive-environments-visionos-apps/digest.md)
- [280 · Iterate your spatial scenes faster with Reality Composer Pro 3](280-iterate-spatial-scenes-faster/digest.md)
- [281 · Extend Reality Composer Pro 3 functionality with Xcode](281-extend-reality-composer-pro/digest.md)
- [393 · Supercharge your spatial workflows with Reality Composer Pro 3](393-supercharge-spatial-workflows-reality/digest.md)
- [282 · Discover the Spatial Preview framework](282-spatial-preview-framework/digest.md)
- [283 · Explore enhancements to visionOS object tracking](283-enhancements-visionos-object-tracking/digest.md)
- [284 · Collaborate on structured 3D models in visionOS](284-collaborate-structured-3d-models/digest.md)
- [285 · Discover USDKit and what's new in OpenUSD](285-usdkit-openusd/digest.md)
- [286 · Use foveated streaming to bring immersive content to visionOS](286-foveated-streaming-bring-immersive/digest.md)
- [320 · Explore immersive website environments in visionOS](320-immersive-website-environments-visionos/digest.md)
- [338 · Build live production tools for Apple Immersive Video](338-live-production-tools-apple/digest.md)

### Games and graphics

- [356 · Bringing Cyberpunk 2077 to Mac](356-bringing-cyberpunk-2077-mac/digest.md)
- [357 · Speedrun your game port with agentic coding](357-speedrun-game-port-agentic/digest.md)
- [358 · Make your game great with touch](358-game-great-touch/digest.md)
- [359 · Build real-time neural rendering pipelines with Metal](359-real-time-neural-rendering/digest.md)
- [388 · Find and fix performance issues in your Metal games](388-find-fix-performance-issues/digest.md)
- [252 · Design no-code games with Reality Composer Pro 3](252-no-code-games-reality/digest.md)

### Camera, media and music

- [303 · Build a responsive camera app that launches quickly](303-responsive-camera-app-that/digest.md)
- [304 · Implement high resolution photo capture](304-implement-high-resolution-photo/digest.md)
- [305 · Enhance RAW image processing with Core Image](305-enhance-raw-image-processing/digest.md)
- [341 · Support the Center Stage front camera in your iOS app](341-support-center-stage-front/digest.md)
- [256 · Discover generated subtitles and subtitle styles](256-generated-subtitles-subtitle-styles/digest.md)
- [253 · Meet the Music Understanding framework](253-music-understanding-framework/digest.md)
- [254 · Integrate MusicKit into your app](254-integrate-musickit-app/digest.md)
- [312 · Meet the Now Playing framework](312-now-playing-framework/digest.md)

### Web and Safari

- [204 · What's new in WebKit for Safari 27](204-webkit-safari-27/digest.md)
- [314 · Learn CSS Grid Lanes](314-learn-css-grid-lanes/digest.md)
- [315 · Rediscover the HTML select element](315-rediscover-html-select-element/digest.md)
- [215 · Get started with the HTML Model Element](215-started-html-model-element/digest.md)
- [216 · Create web extensions for Safari](216-web-extensions-safari/digest.md)

### App Store and business

- [205 · Enhance your presence on the App Store](205-enhance-presence-app-store/digest.md)
- [210 · What's new in Apple In-App Purchase](210-apple-app-purchase/digest.md)
- [309 · Explore Retention Messaging in App Store Connect](309-retention-messaging-app-store/digest.md)
- [391 · Offer subscriptions to groups and organizations](391-offer-subscriptions-groups-organizations/digest.md)
- [378 · Unlock in-game content with StoreKit and Background Assets](378-unlock-game-content-storekit/digest.md)

### App services and system experiences

- [277 · WidgetKit foundations](277-widgetkit-foundations/digest.md)
- [223 · Live Activities essentials](223-live-activities-essentials/digest.md)
- [226 · Create live communication experiences](226-live-communication-experiences/digest.md)
- [209 · What's new in Wallet](209-what-s-new-wallet/digest.md)
- [207 · Deliver workout insights with HealthKit workout zones](207-deliver-workout-insights-healthkit/digest.md)
- [212 · Rev up your CarPlay app](212-rev-up-carplay-app/digest.md)
- [369 · Find your accessory with Bluetooth Channel Sounding](369-find-accessory-bluetooth-channel/digest.md)

### macOS, virtualization and containers

- [224 · Expand the capabilities of your Virtualization app](224-expand-capabilities-virtualization-app/digest.md)
- [389 · Discover container machines](389-container-machines/digest.md)

### Security and device management

- [201 · Secure your apps with App Attest](201-secure-apps-app-attest/digest.md)
- [347 · Secure your app: mitigate risks to agentic features](347-secure-app-mitigate-risks/digest.md)
- [379 · Meet Trust Insights](379-trust-insights/digest.md)
- [206 · What's new in managing Apple devices](206-managing-apple-devices/digest.md)
- [230 · What's new in assessment on macOS](230-assessment-macos/digest.md)

### Design

- [250 · Principles of great design](250-principles-great/digest.md)
- [251 · Communicate your brand identity on iOS](251-communicate-brand-identity-ios/digest.md)
- [290 · Craft clear names for features and labels in your app](290-craft-clear-names-features/digest.md)
- [292 · Design intuitive search experiences](292-intuitive-search-experiences/digest.md)

### Accessibility

- [219 · Enhance the accessibility of your reading app](219-enhance-accessibility-reading-app/digest.md)
- [220 · Refine accessibility for custom controls](220-refine-accessibility-custom-controls/digest.md)
- [221 · Prepare your tvOS apps for Dynamic Type](221-prepare-tvos-apps-dynamic/digest.md)
