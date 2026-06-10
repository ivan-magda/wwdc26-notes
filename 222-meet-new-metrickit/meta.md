# Session 222 — Meet the new MetricKit

- **URL:** https://developer.apple.com/videos/play/wwdc2026/222/
- **Duration:** 17m
- **Speakers:** Yonni (engineer, MetricKit team)

## Description

MetricKit is the framework that provides metrics and diagnostics for monitoring
real-world app performance on real devices. In iOS 27, the framework has been rebuilt
from the ground up with a new, contextually rich, Swift-first API. The session walks
through receiving your first metric report, your first diagnostic report, and then
contextualizing both with the new State Reporting framework. New in iOS 27: Metal
frame rate metrics, memory exception diagnostics, a crash termination category, and
per-state metric aggregation.

## Key topics

- New Swift-first API — `MetricManager` entry point; `metricReports` / `diagnosticReports` async streams; all new features are exclusive to this API (old `MXMetricManager` deprecated path)
- Metrics structure — daily report → `intervalEntries` (one full-day entry + smaller multi-hour breakdowns) → metric groups (`.cpu`, `.memory`, `.display`, `.gpu`) → individual metrics
- New metrics — Metal frame rate metric (for game render performance)
- Diagnostics — crash/hang reports delivered immediately; symbolicated `callStackTree` backtrace, `terminationReason`, exception type
- New diagnostics — memory exception diagnostics (memory-limit terminations); new `terminationCategory` on crash diagnostics
- State Reporting framework — report app states (domains + named states) so MetricKit aggregates metrics/diagnostics per state instead of blending; `@ReportableMetadata` macro for custom structured per-state metadata; `stateEntries` / `StateEntry` on the report
- Server pipeline — reports are `Codable`; `JSONEncoder` with `MetricReport.encodingFormatKey = .byStateReportingDomain` to group output by domain
- Best practices — narrow domains, stable phases (not transient UI), upper limits on state count, validate with the Points of Interest instrument

## Related sessions to fetch (referenced in this talk)

- [ ] Find and fix performance issues in your Metal game

## Chapter summary (Summary tab)

- **0:01 Introduction** — MetricKit provides metrics + diagnostics for monitoring real-world app performance. In iOS 27 it is rebuilt with a new Swift-first API and new features: Metal frame rate metrics, memory exception diagnostics, and granular data with state reporting.
- **4:07 Metrics** — Daily reports contain performance metrics (launch time, hangs, CPU, memory) organized into interval entries and metric groups. Retrieve as Codable reports for server aggregation, or inspect directly by filtering for specific groups and values.
- **7:13 Diagnostics** — On crash, hang, or other failure, MetricKit captures and immediately delivers a diagnostic report with a symbolicated backtrace plus metadata (exception type, termination reason). iOS 27 adds memory exception diagnostics and a new termination category field on crash diagnostics.
- **10:03 Context** — The State Reporting framework lets apps report their active configuration or user flow as named domains and states, so MetricKit aggregates data separately for each state. Custom structured metadata attaches via `@ReportableMetadata`; per-state metrics surface as `StateEntry` values.

## Code

See `code.md` — 10 snippets extracted from the Code tab.
