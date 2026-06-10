---
title: "WWDC26 Platforms State of the Union Recap — Full Digest"
session: WWDC26 · 122
url: https://developer.apple.com/videos/play/wwdc2026/122/
duration: 5m
sources: transcript.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 122 — WWDC26 Platforms State of the Union Recap

## TL;DR

The recap is structured around three words Apple repeats at the end: **"Intelligence
rebuilt. Platforms refined. Tools transformed."**

1. **Intelligence rebuilt** — Apple Intelligence is rebuilt from the ground up on new
   Apple Foundation Models built *with Google*, leveraging the tech behind the **Gemini**
   family. The Foundation Models framework gains **image input**, **cloud model provider**
   integration (bring your own frontier model), and **dynamic profiles** for agents/skills.
   A brand-new **Core AI** framework becomes the recommended way to run on-device models.
   **App Intents** + a new **View Annotations API** wire your app's content and actions
   into Siri and let users act on what's on screen by asking.
2. **Platforms refined** — **Liquid Glass** design (more consistent, personalizable,
   readability-tuned), a uniform tighter macOS corner radius, sharper icons + **Icon
   Composer** refraction effects, **resizable iOS apps** on iPad/Mac, faster and richer
   **SwiftUI**, and a **Spatial Preview Framework** that streams 3D models from Mac into
   Vision Pro.
3. **Tools transformed** — **Xcode 27** (Apple Silicon-only and 30% smaller, themes,
   iCloud sync), faster **Xcode Cloud**, a new **Device Hub** that replaces Simulator,
   and **agentic coding** with agents from Anthropic, OpenAI, and Google, plus a plugin
   system built on the **agent-client protocol** and **MCP**.

This is a top-of-funnel index, not a deep dive — every item points to a dedicated
session (see the fetch queue below).

---

## 1. Intelligence rebuilt

**Apple Intelligence has been rebuilt from the ground up.** [00:08] Apple says it worked
*with Google*, "leveraging the technologies behind their Gemini family of models," to
create the latest **Apple Foundation Models** powering Apple Intelligence. (This is the
headline ecosystem signal of the recap — see Open Questions on how to characterize it.)

The **Foundation Models framework** expands [00:22]:

- **Image input** — multimodal prompts on top of last year's text-only framework.
- **Cloud model support** — for complex tasks needing frontier models, the API can
  integrate with "the cloud model provider of your choice." This is the bring-your-own-
  provider story detailed in session 241 (Anthropic + Google Swift packages, PCC).
- **Dynamic profiles** — build AI agents and skills "with a lot less code" by swapping
  tools in and out and updating instructions on the fly. (Session 241 covers
  `LanguageModelSession.DynamicProfile` in depth.)

**Core AI** is "an entirely new framework, designed to be the best way to run on-device
models in your apps." [00:46] Built into the OS, tuned for Apple Silicon. Note: session
241 referenced a `CoreAILanguageModel` (ANE-backed) inside Foundation Models — Core AI
appears to be the broader framework underneath it. Worth disentangling (see Open Questions).

**App Intents + View Annotations** [00:57]: App Intents make your content discoverable
and your actions available through natural language in Siri. The new **View Annotations
API** lets people act on what's on screen just by asking. The framing: in-app and
system-wide intelligence together unlock experiences "neither could deliver alone — your
apps made more powerful by intelligence and intelligence made more meaningful by your apps."

## 2. Platforms refined

**Liquid Glass** [01:23] — "more consistent, more personalizable, and better tuned to
maintain exceptional readability." Concrete bits:

- Every window on **macOS** shares the same tighter corner radius.
- App icons get sharper rendering automatically; new **refraction effects** in **Icon Composer**.

**Resizable iOS apps** [01:40] — iOS apps are now resizable, so users get larger displays
when running an iPhone app on iPad or on Mac via **iPhone mirroring**. A new **resizable
iOS Simulator** and previews make multi-size testing easy.

**SwiftUI** [01:54] — "more speed, richer interactions, and new capabilities":

- Drag-to-reorder and swipe actions now work in **any container**.
- Nested layouts resize **up to twice as fast**; async image **caches automatically**.
- Toolbars get finer control over what stays visible as space shrinks.

**Spatial Preview Framework** [02:11] — on Apple Vision Pro, streams 3D models from your
Mac into the space around you.

## 3. Tools transformed — the Xcode daily experience

Xcode has "two big stories this year: the Daily Experience and Agentic Coding." [02:19]

Daily experience [02:24]:

- Projects load faster.
- **Xcode 27 is 30% smaller because it's Apple Silicon only.**
- Settings sync via **iCloud**.
- Fully customizable toolbar; color flows through the whole app via **themes** — *Emerald,
  Neon Noir, Coral Reef*.
- **Xcode Cloud**: easier setup, builds **up to twice as fast**, plus Apple Vision Pro and
  Metal-on-Apple-Silicon support.
- **Device Hub** replaces Simulator [02:50] — virtual and physical devices in one place;
  pinch-to-zoom, live resizing, and full control over real hardware from the Mac.

## 4. Tools transformed — agentic coding

Building on last year's AI coding assistance [03:02], Apple is "working with the leading
model providers to bring their agents into Xcode: **Anthropic, OpenAI, and now Google**."

- Agent conversations now **behave like any other file** — open, split, and stack them in
  the Navigator.
- **Plan-first**: "the agent lays out its approach before a single line of code gets
  written, so you stay the architect."
- Agents can **run your tests**, try things in **playgrounds**, **customize previews** to
  validate UI across light/dark mode, orientations, text sizes, and localizations, and even
  **drive your running app** — tap, scroll, swipe, type — to test end-to-end.
- **Built-in specialists** [03:43] — Xcode 27 ships with the expertise of Apple's engineers
  and designers as specialists for **SwiftUI, accessibility, sizing, testing, and
  performance** that agents can draw on.
- **Extensible** [03:56] — plugins bring skills, **MCP tools**, and any agent via the
  **agent-client protocol (ACP)**. **Figma** and **GitHub** ship their own, one click to set up.

"Xcode is more open, more capable, and a whole lot smarter." [04:07]

## Wrap-up

"Intelligence rebuilt. Platforms refined. Tools transformed." [04:11] Full video is on the
Apple Developer app, website, YouTube, and BiliBili.
