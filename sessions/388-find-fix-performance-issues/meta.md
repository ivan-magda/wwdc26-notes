# Session 388 — Find and fix performance issues in your Metal games

- **URL:** https://developer.apple.com/videos/play/wwdc2026/388/
- **Duration:** 21m
- **Speakers:** Ruiwei (Metal Tools Team)

## Description

Maintaining a smooth, consistent frame rate across an entire play session is one
of the most important things you can deliver to players. This session walks through
new tools and workflows for collecting, analyzing, and contextualizing Metal
performance data over long game sessions — from desk testing through to field data
after the game ships. It covers the always-on system recording of Metal performance
metrics, the new `metalperftrace` command-line tool (macOS 27), the `StateReporting`
API for adding game-state context to traces, and MetricKit's new Metal frame-rate
metrics (iOS/macOS 27).

## Key topics

- Metal performance metrics overview — FPS, GPU time, frame interval, layer sizes, composition mode, MetalFX metrics
- Metal Performance HUD — configurable overlay of live metrics; good for quick reads, not for saving long durations
- Trace collection — Game Performance Overview template in Instruments (desk testing) + always-on system-level "look-back" collection saved for days, on macOS and iOS
- `metalperftrace` CLI (macOS 27) — `collect` (with `--last` / `--start`/`--end`) and `overview` (with `--json`, predicate filtering)
- Analyzing traces in Instruments — timeline plotting, auto-highlighting of deviating stats, time-range aggregation
- `StateReporting` API (Swift + Objective-C) — domains (finite state machines), states (labels), stable metadata, volatile metadata
- StateReporting integration across Metal Performance HUD, `metalperftrace` (`--include-state-transitions`, `--aggregate`), and Instruments (Points of Interest tracks)
- Best practices — orthogonal domains, low transition cadence (system throttles), verify correctness via HUD/Instruments
- Deep-dive profiling — Metal system trace template, Metal debugger in Xcode
- MetricKit field data (iOS/macOS 27) — Metal frame rate metrics, per-StateReporting-state breakdowns, memory-exception diagnostics

## Related sessions to fetch (referenced in this talk)

- [ ] Discover new Metal profiling tools for M3 and A17 Pro
- [ ] Meet the new MetricKit

## Chapter summary (Summary tab)

- **0:00 Introduction** — The challenge of maintaining consistent frame rates across long play sessions (thermal shifts, settings changes, level/fight transitions); preview of new tools for collecting, analyzing, and contextualizing Metal performance data.
- **1:51 Metal performance metrics** — Tour of metrics available via tools like the Metal Performance HUD: FPS, GPU time, frame interval, layer sizes, composition mode, MetalFX-related metrics.
- **3:32 Trace collection** — Game Performance Overview template in Instruments for desk testing; new always-on system-level data collection on iOS and macOS that stores Metal performance metrics for days.
- **6:38 Analyze performance traces** — Using the `metalperftrace` CLI on macOS (printing overviews, exporting JSON for scripting / AI agents); opening and visualizing traces in Instruments to identify FPS drops and anomalies.
- **10:08 Contextualize with StateReporting** — The `StateReporting` API: describe game behavior/state over time via domains, states, and metadata; how transitions appear in the HUD, `metalperftrace` output, and Instruments tracks.
- **17:48 Collect field data with MetricKit** — Using MetricKit in macOS and iOS 27 to collect Metal frame-rate data and other performance metrics in the field after shipping, including per-state breakdowns via StateReporting domains.
- **19:41 Next steps** — Adopt StateReporting, try the new trace-collection tools and `metalperftrace` CLI, explore related Metal developer-tools documentation and MetricKit.

## Code

See `code.md` — 5 snippets extracted from the Code tab.
