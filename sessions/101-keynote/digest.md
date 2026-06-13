---
title: "WWDC26 Keynote — Full Digest"
session: WWDC26 · 101
url: https://developer.apple.com/videos/play/wwdc2026/101/
duration: 01:16:02
sources: transcript.md
compiled: 2026-06-13
note: Transcript is machine-generated from the keynote audio (whisper.cpp ggml-large-v3) with no speaker diarization. Quotes are not attributed to named execs unless the transcript self-identifies the handoff. Product names and numbers heard via whisper may be mis-transcribed; uncertain ones are flagged inline.
---

# WWDC26 · 101 — Keynote

> The ~76-minute WWDC26 Keynote. Software-only this year: no new hardware was
> announced. Apple framed the releases around three themes: responsiveness and
> ease-of-use refinements, trust and safety for kids, and "a big leap forward for
> Apple Intelligence." The marquee news is a re-architected Apple Intelligence built
> in collaboration with Google's Gemini technology, and an all-new "Siri AI." The
> naming year confirms **iOS 27** and **macOS GoldenGate**.

## TL;DR

- **Apple Foundation Models are now built with Google.** Apple states it "embarked on
  a deep collaboration with Google, leveraging the technologies behind their Gemini
  family of models" to create the next generation of Apple Foundation Models, adapted
  to run on-device and on servers via Private Cloud Compute. This is the single
  biggest announcement of the keynote.
- **"Siri AI"** — an entirely new Siri rebuilt on Apple Intelligence: conversational,
  with personal context, App Actions, on-screen awareness, image understanding, and
  broad world knowledge. New dedicated **Siri app** (history synced privately via
  iCloud). Across iOS, iPadOS, macOS, watchOS, visionOS, CarPlay, AirPods.
- **Siri AI ships in beta later this year, English-first.** Developers can try the new
  Siri **today**. It will **not be available initially in the EU** (iOS/iPadOS), and
  **not in China** while Apple works through regulatory requirements.
- **macOS GoldenGate** is the next macOS. **iOS 27** is the next iOS and supports the
  **same models as iOS 26, back to iPhone 11**.
- **Big performance numbers:** apps launch up to **30% faster**, new photos appear up
  to **70% faster**, AirDrop up to **80% faster**, iPad-to-external-drive up to **5x
  faster**.
- **Major child-safety expansion:** **Ask to Browse** (web approval), contact-approval
  controls, category **time allowances**, schedules, redesigned Screen Time, and a
  **Declared Age Range API** for developers.
- **Apple Intelligence across apps:** Safari (auto tab grouping, **Notify Me**,
  **Describe an Extension**), **agentic password updates** in Passwords, a re-imagined
  **Image Playground** (photorealistic generation), and **spatial reframing** in Photos.
- **For developers:** Foundation Models framework gains **image input, custom
  tools/skills, and server models** via the same Swift API; a new **Core AI** framework
  runs other models locally on Apple Silicon; **Xcode** coding assistant can localize a
  whole app and supports **Gemini** plus Figma/GitHub; new **Device Hub**.
- **Timeline:** developer betas **today**, public beta **next month**, general
  availability **this fall**.

## 1. Open and the year's three themes

Tim Cook opened at Apple Park. App Store scale stat: "well over **1,000 submissions to
the App Store every hour**." Apple cited **20 Apple Developer Academies** worldwide that
have "helped tens of thousands of students."

Craig Federighi laid out three focus areas for this year's releases:
1. Responsiveness and ease-of-use ("sweating the details").
2. Trust and safety, "building on our commitment to provide a safe and trusted platform
   for kids."
3. "A big leap forward for Apple Intelligence, with an innovative architecture that
   unlocks a new Siri across platforms."

The releases span **iOS, iPadOS, watchOS, tvOS, visionOS, and macOS**.

**macOS naming gag:** after the usual bit about the marketing team, the next macOS is
revealed as **macOS GoldenGate**.

## 2. Refinements — design, performance, and "sweating the details"

### Design (Liquid Glass, year two)
- **Liquid Glass refinements.** Apple "tuned Liquid Glass so it diffuses complex content
  behind it much more effectively while also creating more depth and separation."
- **New clarity-to-tint slider** in Settings: adjust Liquid Glass "anywhere from ultra
  clear to fully tinted." For developers who already adopted Liquid Glass, these
  customizations "apply in your apps right away."
- **macOS structure changes:** a more uniform **toolbar** across the top of apps;
  **sidebars now expand to the very edges of the window**; sidebar icons **regain their
  color**; every macOS window now has the **same tighter corner radius**.
- **App icons** now integrate **additional layers of Liquid Glass directly into the icon
  artwork**, making icons "appear sharper and more defined"; they also support an
  "all-clear look."

### Performance / fundamentals
Apple says it optimized memory usage, CPU utilization, networking, and display
rendering. Stated numbers (all "up to"):
- **iPhone and iPad apps launch up to 30% faster** (via preloading key data; applies to
  third-party apps too).
- **New photos appear up to 70% faster** in your library.
- **AirDrop transfers up to 80% faster.**
- **Browsing/transferring files from iPad to an external drive is up to 5x faster**, "as
  fast as the Finder on Mac."
- **CPU scheduler:** the advanced scheduler from newer iPhones is further optimized and
  **brought back to older models, all the way to iPhone 11**.
- **iOS 27 is supported on iPhone 11** and the same models as iOS 26: "iOS 27 is
  available to more users than any iOS release ever."

### Connectivity, messaging, search
- **Smarter network transitions** between cellular and Wi-Fi (e.g., not clinging to a
  coffee-shop or airline Wi-Fi).
- **New per-message send indicator** in Messages for low-bandwidth sends, so you know
  what has and hasn't been delivered.
- **Rebuilt search foundation** on iOS, iPadOS, and macOS powering **Spotlight, Photos,
  and Mail**. The re-architected **search index** is "more stable, more efficient, and
  more comprehensive"; new content is indexed "almost immediately." Mail gets a
  **completely new ranking system** for top hits.

### App features grab-bag (the "you asked for this" list)
- **Photos — iCloud Shared Albums:** friends on **Android or Windows** can join and
  contribute; Shared Albums now support **full-resolution sharing**.
- **Health — Cycle Tracking** adds support for **perimenopause and menopause**
  (notifications when patterns are suggestive, symptom logging, educational info).
- **AirPods — custom EQ.**
- **Apple Vision Pro — panoramas to spatial scenes** "with incredible depth and
  realism"; panoramas can be used as your environment.
- **Maps — Flyover boost** using aerial imagery combined with "vision intelligence
  models," rendering cities "in stunning, sharp detail."

## 3. Trust and safety for kids

Grounded in two principles: parents decide what works for their family, and features
are shaped by expert health research. Apple is working with the **American Academy of
Pediatrics** to adapt its **Family Media Plan** into a guide using Apple's child-safety
features. Research framing: kids under 18 benefit from age-based protections; under 13,
device access should be limited and expanded "when a child is ready"; experts recommend
children under 13 not use social media.

Announced controls (built on the **child account**, which can be converted from an
existing account):
- **Content:** a setup assistant lets parents start kids with only the content they're
  comfortable with (a few essential apps, a recommended set, or specific ones), adding
  more over time.
- **Ask to Buy** (apps) is joined by **Ask to Browse** (websites): parents approve new
  websites; works across iPhone, iPad, and Mac with Safari. **Both on by default for
  kids under 13**; can be enabled for teens.
- **Who kids can talk to:** start with immediate family, add contacts; kids can be
  required to **ask for permission before connecting with anyone new**.
- **Communication safety** already blurs nudity (including on live FaceTime); this year
  it also **intervenes before kids see gore or violent content** in shared images/videos.
- **Time allowances** for **entertainment, games, and social media**, with a combined
  daily recommendation plus per-category recommendations, based on a child's age and
  developed with clinical/child-development experts including the AAP.
- **Schedules:** choose which apps are available at different times of day (e.g., during
  school); schedules work hand-in-hand with time allowances.
- **Screen Time completely redesigned**: at-a-glance usage, adjust access "with just a
  tap."
- **New website** highlighting all child-safety features and how to get started.

**For developers (kids):** a full suite of APIs/resources to protect against nudity and
violent content and to require parental approval of new contacts, plus the
**Declared Age Range API**, which lets apps use a child's age range "in a
privacy-preserving way" to tailor the experience.

## 4. Apple Intelligence — the new architecture

Apple positioned this against rivals "racing forward… pursuing AI for the sake of AI."
Its stated vision: AI integrated deep into everyday products, grounded in personal
context and apps, with privacy at every step.

**Foundation Models + Google.** Key line from the transcript: "This year, we embarked on
a deep collaboration with Google, leveraging the technologies behind their **Gemini**
family of models. Together, we created the next generation of **Apple Foundation
Models** for our integrated Apple Intelligence experiences and adapted these new models
to run on-device and on servers using Private Cloud Compute."

Model lineup as described:
- New **server and on-device models** with "state-of-the-art understanding and
  reasoning" and **multimodality** — image understanding and generation (realistic
  images, photo edits, answers about visual content).
- A **second, more powerful on-device model** optimized for "the most capable of our
  Apple Silicon systems." It can **understand and generate speech** plus understand text
  and images, enabling higher-accuracy system-wide dictation, better natural-language
  understanding, and "incredibly expressive voices."

**System layer:**
- A new **system orchestrator** that "securely coordinates" the models' capabilities.
- **Personal context understanding** via **Spotlight and its Semantic Index**; works
  with any app that integrates with it.
- **Broad world knowledge** — goes to the web for up-to-date info, then uses Private
  Cloud Compute to generate an answer.
- **App Actions** — draws on an **app toolbox** to find the right tools from your apps
  (e.g., drafting an email, editing/sharing photos).
- **On-screen awareness** — tailors assistance to the current app and activity.

**Privacy framing:** on-device processing plus **Private Cloud Compute**, with data
"not stored or accessible to Apple or anyone else," used only to execute the request,
and verifiable by outside experts.

## 5. Siri AI

Apple rebuilt Siri "with powerful AI at the core" and calls the new version **Siri AI**.
Accessed the same ways as today (e.g., "Hey Siri"), it adds personal context, App
Actions, on-screen awareness, image understanding, and world knowledge.

- **Conversational:** back-and-forth, detailed answers, in-depth plans, brainstorms,
  document feedback. Goes "well beyond quick questions or one-shot tasks."
- **Dedicated Siri app:** revisit or start conversations; history is **synced privately
  with iCloud** so you can move iPhone → iPad → Mac. (Apple Watch reaches it via a new
  **app grid**.)
- **New expressive, customizable voice** (adjust expressivity and pace) on products
  supporting the most advanced on-device model.
- **System-wide dictation** gets "a major boost in accuracy" (spelling, punctuation,
  capitalization), built into the keyboard.
- Updates extend to **CarPlay and AirPods**.

**Per-platform integration:**
- **iOS:** swipe down from the **Dynamic Island** to search or type to Siri; pull down
  at the bottom into a conversational experience. Demos: World Cup schedule + watch-party
  menu planning (pulling a contact's message into the conversation), Suki Waterhouse
  concert + reminder + music.
- **macOS:** Siri is **integrated into Spotlight** (Spotlight recognizes a query for
  Siri and lets you start a conversation, draggable/resizable). System-wide
  **control-click context menus** to ask Siri about images, files, or text; a new Siri
  field to ask about a multi-file selection. Demo: comparing shed quotes, drafting an
  email "from scratch."
- **iPadOS:** Siri AI with the conversational experience.
- **watchOS:** ask questions and take action on the wrist.
- **visionOS:** a **3D visualization of Siri** you can place in your space; no "Hey
  Siri" needed — "just look at Siri and start speaking."

### Visual intelligence + writing tools
- **iPhone:** a brand-new **Siri mode in the Camera app** — tap the shutter to let Siri
  see what you see; pull down for rich details and follow-ups. Saved to the Siri app.
  Deep image understanding runs on **Private Cloud Compute**. Suggests actions (e.g.,
  nutrition from a plate of food; **split the bill with Apple Cash**).
- **macOS:** dedicated **keyboard shortcut**; select something on screen and type to
  Siri; suggests actions like adding multiple calendar events at once.
- **iPad:** integrated into the **screenshot** experience.
- **visionOS:** ask about things just by looking (demos reference an **REI Flash 22**
  pack as a carry-on and **Loa** [transcript-uncertain] hiking boots fitting inside).
- **Writing with Siri** virtually anywhere you type: generate drafts from a natural-
  language description; in Mail/Messages it can match how you usually communicate with a
  specific person; get feedback on your writing. **Automatic proofreading** is now
  system-wide, "including most third-party apps."

**Availability (Siri AI):** "available in English to start," expanding to more languages.
Developers can try it **today**; **Siri AI launches in beta later this year** for
customers. **Not available initially in the EU** on iOS and iPadOS ("working hard to find
a path forward that preserves our users' privacy and security"); **Siri AI and the other
new Apple Intelligence features will not be available in China** pending regulatory work.

## 6. Apple Intelligence across apps

### Safari
- **Tame your tabs:** Apple Intelligence groups tabs into **topics** by analyzing each
  page; auto-adds related tabs; close a whole topic or save it as a tab group.
- **Notify Me:** describe in natural language what you're waiting for on a page (camp
  sign-ups, restock); Safari monitors it and notifies you of the change.
- **Describe an Extension:** describe what you want and Safari creates a **custom
  extension** that adapts webpages for you (e.g., a recipe save/rate button).
- Privacy contrast drawn against "some browsers with AI that track your every move."

### Passwords
- **Agentic password updates:** automatically update eligible accounts to strong
  passwords "with just a tap." Passwords + Safari + Apple Intelligence "agentically take
  action on your behalf, securely navigating through each website to sign in and fix
  your passwords."

### Messages, Mail, Calendar, Phone
- **Messages:** context-aware **one-tap suggestions** (create a reminder/note); **Search
  for Photos** finds the right images by keywords, locations, and named people.
- **Mail:** more capable contextual **suggestions** to act with favorite apps, including
  third-party apps.
- **Calendar:** add events by **describing them in natural language** (identifies a
  contact, sets location and title); easier event editing (e.g., change a weekly lunch to
  biweekly).
- **Phone — Call Context:** when you call a business, the phone app proactively surfaces
  relevant info (e.g., a flight confirmation code from Mail). It "looks at who you're
  calling, not what you're saying," and **runs entirely on device**.

### Home
- **Notification grouping:** related accessory notifications understood as a **single
  activity** (one notification that updates).
- **Camera clip summaries:** analyzes recorded clips from compatible cameras and
  generates **descriptions**; connects relevant footage across cameras; search clips by
  what was captured; surfaces the most important clips at the top.
- **4K resolution** for recorded clips on supported cameras.

### Shortcuts
- **Describe a shortcut:** Apple Intelligence reasons over a natural-language
  description and assembles the steps. Example builds an automation that triggers when
  you leave work, computes ETA via Maps, and messages it. Tweak by describing changes.

### Image Playground
- Re-imagined with **powerful image models** at the core; makes **high-quality images in
  almost any style, including photorealistic**, via a new generative model on **Private
  Cloud Compute**.
- Use **multiple people from your photo library**; transform photos by natural-language
  description; **circle-to-select** objects to move/resize/edit; choose dimensions
  (landscape/portrait).
- Integrated across the system: Messages backgrounds, **contact posters**, **lock screen
  wallpapers**; personalized suggestions from your photos.
- **Image Playground API** for developers.

### Photos — three new editing features
- **Cleanup** upgraded: better quality, more realistic infill in complex scenes.
- **Extend:** expand images (more breathing room, straighten a horizon) without cropping.
- **Spatial reframing:** shift the perspective of an already-taken photo (touch and
  drag) "as if I had repositioned the camera." Uses **on-device spatial models** for
  real-time preview (built on Apple Vision Pro spatial understanding) plus the
  **image-generation model on Private Cloud Compute** to fill only the gaps. Works on
  "almost any photo," including old ones or those from other cameras.

## 7. Availability, limits, and rollout

- Apple Intelligence app features are **free with the latest software releases** and
  come to **all Apple Intelligence-supported languages**.
- Some features (including **image generation**) have **daily usage limits** because
  they rely on powerful server models.
- **Increased access** is available with **most iCloud+ subscription plans**, which also
  include Apple Intelligence support for **compatible home cameras**.
- Supported on the **same product models that support Apple Intelligence today**. The
  **most powerful on-device model** (expressive voices, more advanced dictation) comes to
  "our most capable iPhone, iPad, and Mac systems."
- **OS betas:** developer betas **today**, **public beta next month**, **users this
  fall**.

## 8. For developers

- **App Intents → Siri:** bring Apple Intelligence into apps with technologies you
  already know. Examples cited: messaging app **Line** indexes content into Spotlight so
  users can ask Siri to find info from conversations; calendar app **Structured** adopts
  App Intents so a user can ask Siri to create an event. (App names transcript-heard.)
- **Foundation Models framework:** now supports **images as input** (in addition to
  text), **custom skills/tools** to extend model capabilities, and **server-based
  models**, all "through the same Swift API." Demo app cited: **Daydream** (fashion app
  using the on-device model). (App name transcript-heard.)
- **Core AI framework (new):** bring **other models to run locally** with the full power
  of Apple Silicon; available on all platforms. (Heard as "core AI"; exact capitalization
  to verify in SOTU.)
- **Xcode / agentic coding:** the coding assistant can **localize an entire app** and
  **interact with simulated devices**; extend with **custom skills**; **choose the model
  and agent of your choice, now including Gemini**; connect to **Figma and GitHub**.
- **Device Hub (all-new):** unifies every device, simulated and real, in one interface;
  simulate multi-touch (swipe, pinch), change app appearance in one click, resize
  dynamically.
- Pointer: "You'll hear much more about our tools, languages, and frameworks in the
  **Platforms State of the Union**."

## Notable details & numbers

- **App Store:** "well over **1,000 submissions every hour**."
- **20 Apple Developer Academies** worldwide.
- Performance "up to" numbers: **30%** faster app launch, **70%** faster new-photo
  appearance, **80%** faster AirDrop, **5x** faster iPad→external drive.
- **iOS 27** supports back to **iPhone 11** (same models as iOS 26) — framed as "more
  users than any iOS release ever."
- **macOS GoldenGate** is the next macOS name.
- **Home cameras:** **4K** clip recording on supported cameras; Apple Intelligence camera
  support tied to **most iCloud+ plans**.
- **Ask to Browse / Ask to Buy:** on by default **under 13**.
- **Image generation** and other server-backed features carry **daily usage limits**;
  iCloud+ raises them.
- **Siri AI:** **English first**; **beta later this year**; **excluded from EU
  (iOS/iPadOS) initially** and from **China** pending regulation; developers get it
  **today**.
- **OS timeline:** developer betas **today** (keynote day), **public beta next month**,
  **GA this fall**. The transcript does not state the calendar date; do not assert one.
- **Tim Cook** delivered a notably reflective close ("the honor of a lifetime… the best
  is still ahead"), which read as valedictory; the keynote did not state any leadership
  change, so treat any succession reading as inference, not fact.
- The keynote ends with an **App Store developer rap montage** name-dropping dozens of
  apps (TikTok, Delta, JetBlue, StockX, Nordstrom, Minecraft, LinkedIn, Evernote,
  GoodNotes, Warframe, and many more) — a closing thank-you to developers, not an
  announcement.

## For developers (consolidated)

- **Foundation Models framework:** image input, custom tools/skills, server models, one
  Swift API.
- **Core AI:** run third-party/other models locally on Apple Silicon, all platforms.
- **App Intents:** the on-ramp for Siri/Spotlight integration and App Actions.
- **Image Playground API:** the new generative image experience for apps.
- **Declared Age Range API** + child-safety APIs (nudity/violence protection, contact
  approval).
- **Xcode:** localize whole app, drive simulated devices, custom skills, model/agent
  choice incl. **Gemini**, Figma/GitHub connectors.
- **Device Hub:** unified real+simulated device testing.
- **Liquid Glass:** the clarity-to-tint customization applies to already-adopting apps
  automatically.
- **Try Siri AI today** as a developer; the rest is detailed in the Platforms State of
  the Union.
