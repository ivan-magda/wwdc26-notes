---
title: "Find and fix performance issues in your Metal games — Full Digest"
session: WWDC26 · 388
url: https://developer.apple.com/videos/play/wwdc2026/388/
duration: 21m
speakers: Ruiwei (Metal Tools Team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 388 — Find and fix performance issues in your Metal games

## TL;DR

A practical, end-to-end performance workflow for Metal games built around one
problem: a smooth frame rate has to hold up across hours of play, through thermal
shifts, settings changes, and level transitions — not just in a 30-second capture.
Four headline pieces:

1. **Always-on look-back collection.** In iOS/macOS 27 the system continuously
   records Metal performance + resource metrics and keeps them for *days*. You can
   collect a trace *after* a session wraps, looking back hours, with no pre-session
   setup on macOS (one-time setup on iOS).
2. **`metalperftrace` CLI (macOS 27).** `collect` grabs a look-back trace; `overview`
   prints aggregated resource + per-layer Metal stats, with `--json` for scripts,
   regression tests, or AI agents triaging issues. Traces also open in Instruments.
3. **`StateReporting` API.** The key new framework. It lets you annotate a trace with
   *what the game was doing* — domains (finite state machines) with labels, stable
   metadata, and volatile metadata. This turns "FPS dropped at the 12-minute mark"
   into "FPS drops whenever graphics quality is set to High." Integrated into the HUD,
   `metalperftrace`, and Instruments.
4. **MetricKit field data (iOS/macOS 27).** After shipping, MetricKit now exposes
   Metal frame-rate metrics — including per-StateReporting-state breakdowns — plus
   memory-exception diagnostics, delivered as daily in-process reports.

The through-line: collect long traces → analyze → **contextualize with StateReporting**
→ keep monitoring in the field. StateReporting is what makes every other tool actionable.

---

## The performance cycle (Introduction)

Achieving a smooth game experience is a loop: play test on many devices and form
factors → collect performance data → analyze → identify issues → fix → repeat until
you hit your targets. The hard part is duration: over hours of play, the device heats
up and thermal state shifts, players change graphics settings, and they move between
levels and fights. To genuinely validate smoothness you have to measure across many
scenarios over long periods — which the older quick-glance tools don't capture well.

## 1. Metal performance metrics & the HUD

Metal tools expose a wide range of metrics that give you a baseline for comparing
sessions and spotting issues:

- **Timing metrics** — frames per second, GPU time, frame interval → tell you frame
  pacing and how utilized the GPU is.
- **Display-setup metrics** — layer sizes, composition mode, MetalFX-related metrics →
  confirm display settings are configured correctly.

The **Metal Performance HUD** overlays metrics (FPS, memory usage, frame interval) on
top of game content. Its configuration panel lets you enable/disable individual
metrics or pick a preset. It's great for a quick read during development, **but it
can't save metrics over long durations** — and it's easy to forget exactly where a
frame drop happened during a multi-minute or multi-hour session.

(Reference docs named: "Monitoring your Metal app's graphics performance" and
"Understanding the Metal Performance HUD metrics".)

## 2. Trace collection

Two paths:

**Desk testing — Instruments Game Performance Overview template.** Pick it in the
template selector; it captures aggregated Metal performance metrics plus Time Profiler
CPU samples. Start the game from Instruments or attach to a running session on a
connected device, then hit record. Ideal for sessions of minutes or longer at your
desk.

**Always-on look-back collection (the powerful part).** While the game runs, the
system is *always* recording and saving Metal performance + resource metrics —
aggregated and optional per-frame (CPU, GPU, FPS, memory) — efficiently stored and
kept for **days**. So for a multi-hour session with many state changes, you can look
back in time and collect data *after the fact*, locally, on macOS and iOS.

### macOS — `metalperftrace collect`

No extra configuration. Run `collect` and pass `--last` for a duration (hours to days),
or an explicit `--start`/`--end` range.

```bash
# Collect the last 5 hours
metalperftrace collect /tmp --last 5h
# /tmp/MetalPerfTrace_20260401_094100_to_144100.atrc

# Or an explicit time range
metalperftrace collect /tmp \
  --start 2026-04-01T09:41:00 \
  --end 2026-04-01T12:41:00
```

### iOS — one-time device setup

With Developer Mode on: Developer settings → **Enable Performance Trace** → select
**Lookback Collection** and choose how far back to cover → add the **Performance Trace**
button to Control Center. Play test as usual; afterward, tap the Control Center button
to collect and process the trace for the configured duration. You get a notification
when processing finishes; traces appear in the **Available Trace Files** list and can be
transferred to your Mac for analysis.

## 3. Analyzing traces

### `metalperftrace overview`

```bash
metalperftrace overview /Data/MyGameTrace.atrc
```

The report is two sections per process:

1. **Resource usage** — memory usage, CPU time, disk activity.
2. **Per-layer Metal performance metrics** — FPS, frame counts, and statistical values
   (avg/min/max/stddev) for frame time, CPU begin-to-present, on-GPU time, next-drawable
   wait, etc.

In the demo trace: ~2.1 GB average memory, ~60 average FPS, ~16.74 ms average frame
time. A **predicate** can filter to a specific process when several Metal apps appear in
one trace. Pass **`--json`** for structured output you can pipe into scripts for
regression testing or hand to AI agents to auto-triage issues.

### In Instruments

Open the trace for deeper, more detailed metrics. Data is plotted on a timeline, and
Instruments auto-evaluates values so deviating stats are highlighted (e.g. an FPS drop
marked yellow). The detail view shows min/max/avg/stddev, aggregated for the whole trace
or a selected time range — select a range and stats update automatically. In the demo,
a selected window shows **average FPS dropped to 26** with very low GPU usage.

**The core problem this sets up:** the drop happened around the 12-minute mark, but the
trace alone doesn't say *what the game was doing*. Was it a specific level area? A
graphics-settings change? Average FPS isn't actionable without context.

## 4. Contextualize with StateReporting

**`StateReporting`** is a new API (Swift + Objective-C) for describing your game's
behavior and state over time. Four core concepts:

- **Domain** — a finite state machine for one area of functionality (e.g. a *level*
  domain tracking player progress). Each domain is in exactly one state at a time.
- **State** — starts with a **label** (e.g. `"Level 1"`).
- **Stable metadata** — an immutable dictionary of serializable info about the current
  state, beyond the label (e.g. `id`, `biome`).
- **Volatile metadata** — values that change *within* a state without a transition (e.g.
  player health, player position).

You define multiple orthogonal domains — level, graphics, network — and lay them on the
same timeline as the FPS graph. Suddenly the trace is contextualized: you can see which
state coincides with the frame-rate drop and focus optimization there.

### Reporting (sample is Objective-C)

```objc
#import <StateReporting/StateReporting.h>

NSString *domain = @"com.mygame.level";
SRStateReporter *reporter = [SRStateReporter reporterForDomain:domain];

[reporter reportTransitionToStateLabel:@"Level 1"
                        stableMetadata:nil
                      volatileMetadata:nil];

[reporter reportTransitionToStateLabel:@"Level 1"
                        stableMetadata:@{ @"id": @1001 }
                      volatileMetadata:nil];

[reporter reportVolatileMetadataUpdate:@{ @"health": @100 }];
```

Create a domain (reverse-DNS string), ask for a **reporter** (an instance of the state
machine), then call `reportTransition…` with a label (+ optional stable metadata), and
`reportVolatileMetadataUpdate:` to change volatile values without transitioning.

### Integration across the tools

- **Metal Performance HUD** — domains appear in the metrics configuration tab; enable
  them and the overlay shows label + stable + volatile metadata. Best way to verify
  adoption. Demo: a level state with `biome`/`id` as stable metadata and player position
  as volatile, reported once per second.
- **`metalperftrace`** — the overview lists domains, transition counts, and last known
  state. Add `--include-state-transitions` for the full list with timestamps and metadata:

  ```bash
  metalperftrace overview /Data/MyGameTrace.atrc --include-state-transitions
  # com.mygame.graphics
  #   High (30.59%, 14.996s)  raytracing: 1  shadow: ultra
  #   Medium (69.38%, 34.012s)  raytracing: 0  shadow: medium
  ```

  And `--aggregate` reports metrics as a function of state — across all domains, one
  domain, or a specific label:

  ```bash
  metalperftrace overview /Data/MyGameTrace.atrc --aggregate \
    --domain com.mygame.graphics --state-label "High"
  ```

  The payoff in the demo: **average FPS is only ~24 when graphics is set to High**, with
  a list of overlapping states from other domains.
- **Instruments** — opening a trace with state transitions creates a track per domain
  under the **Points of Interest** instrument; each track graphs transitions and volatile
  updates. Select a state to inspect its stable/volatile metadata in the sidebar.
  Combined with Metal metrics, this confirmed the frame rate started dropping as the
  graphics setting changed to High.

### Going deeper on a confirmed issue

Once you have enough context to reproduce, capture detailed CPU/GPU scheduling with the
**Metal system trace** template in Instruments, or capture and profile frames with the
**Metal debugger** in Xcode. (See "Metal Developer Tools" docs and "Discover new Metal
profiling tools for M3 and A17 Pro".)

### StateReporting best practices

- **Design domains and states up front.** Keep each domain conceptually orthogonal;
  don't cram many dimensions into one domain.
- **Keep transitions infrequent.** StateReporting is for long-period context, *not*
  high-frequency changes. Limit transitions to the cadence of user actions or slower —
  the system **throttles** if the rate is too high and you lose information until it
  recovers.
- **Verify correctness** with the HUD and Instruments; check transitions happen when you
  expect. Edge cases easily make the data wrong and hard to read.

## 5. Field data with MetricKit

After shipping, keep monitoring on real player devices. **MetricKit** provides two kinds
of data — **metrics** and **diagnostics** — with direct in-process access via daily
reports.

- In **macOS and iOS 27**, MetricKit exposes **Metal frame-rate** information alongside
  many other performance/power metrics.
- It also provides Metal frame rate **as a function of your StateReporting states** —
  frame rate aggregated and grouped by state. The sample report shows overall average
  frame rate (with time and frame count) plus a breakdown by states in the *level* domain.
- Reports are delivered to your game process for local analysis, and can be packaged for
  off-device processing and aggregation.
- **Diagnostics** help pinpoint which code path caused a problem — e.g. memory
  exceptions when the game is terminated for exceeding its memory limit.

(Deep-dive: "Meet the new MetricKit".)

## Recap & next steps

In iOS/macOS 27, Metal performance metrics are always recorded by the system; you can
look back and collect traces spanning hours/days, analyze with `metalperftrace` and
Instruments, add context with `StateReporting`, and post-launch collect MetricKit
metrics + diagnostics from player devices.

Recommended path: adopt **StateReporting** (design the domains that matter — levels,
graphics, network — and start reporting), play test and collect long-session traces,
and wire up **MetricKit** for daily field reports.
