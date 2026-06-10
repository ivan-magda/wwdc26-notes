---
title: "Meet the new MetricKit — Full Digest"
session: WWDC26 · 222
url: https://developer.apple.com/videos/play/wwdc2026/222/
duration: 17m
speakers: Yonni (MetricKit team)
sources: transcript.md, code.md, meta.md
compiled: 2026-06-10
---

# WWDC26 · 222 — Meet the new MetricKit

## TL;DR

In iOS 27 MetricKit is **rebuilt from the ground up** with a modern, Swift-first API.
The entry point is no longer `MXMetricManager` but a new **`MetricManager`** that hands
you two async streams: `metricReports` and `diagnosticReports`. Reports are `Codable`,
strongly typed, and structured for both server aggregation and direct inspection. Three
big additions:

1. **Metal frame rate metric** — render performance for games.
2. **Memory exception diagnostics + a crash `terminationCategory`** — more insight when
   the system kills your app, and a way to tie abnormal-termination trends to individual
   crash reports.
3. **State Reporting** — a companion framework that lets you label *what your app was
   doing* (active tab, configuration, experiment branch) so MetricKit aggregates metrics
   and diagnostics **per state** instead of blending everything into one number.

All new features are **exclusive to the new API**. If you're on `MXMetricManager`, the
closing call to action is: migrate to `MetricManager`.

The framing throughout is a performance workflow loop: **collect → analyze → triage →
fix → monitor**. MetricKit owns the "collect" step; metrics drive analysis, diagnostics
drive triage.

---

## 1. Overview — metrics vs. diagnostics

MetricKit provides two kinds of data:

- **Metrics** — your app's ongoing health signal. Tell you whether an area of performance
  is improving or worsening overall (launch time, hangs, animation/hitches, CPU, GPU,
  disk writes, network transfers).
- **Diagnostics** — tell you *which code path* caused a problem so you can fix it.

Example metric shapes mentioned:

- **Time to first draw** launch metric is delivered as a **histogram** of launch counts
  bucketed by time range (e.g. most launches between 510–540 ms over a day).
- You can derive your own KPIs from raw data: a reported 3 s total hang time over 30 min
  of use → a derived **6 s/hour hang rate**, which becomes a trend signal once aggregated
  across devices.

New in iOS 27, every metric can be provided **as a function of app state** (e.g. hang time
intersected with active tab 1 / 2 / 3) — see section 4.

## 2. Metrics — receiving and reading reports

As people use the app, MetricKit collects metrics throughout the day and delivers a
**daily report**. Report structure:

- `intervalEntries` — a **full-day** aggregated entry plus smaller **multi-hour breakdown
  windows** (only present when there are metrics for them).
- Inside each interval, metrics are grouped into **metric groups**: `.cpu`, `.memory`,
  `.display`, `.gpu`.
- Inside a group are the individual metrics.

### Receive reports

`MetricManager` is the entry point. Await its `metricReports`. Do this **at app startup**
to avoid losing data from a delayed subscription, and keep the manager alive so streams
keep delivering.

```swift
import MetricKit

let manager = MetricManager()

for await report in manager.metricReports {
    processReport(report)
}
```

### Send to a server (reports are Codable)

```swift
import MetricKit

for await report in manager.metricReports {
    let jsonData = try JSONEncoder().encode(report)
    sendToServer(jsonData)
}
```

### Inspect a specific group / value

Iterate `intervalEntries`, filter to a group, switch over the metric cases:

```swift
import MetricKit

for await report in manager.metricReports {
    let intervalEntries = report.intervalEntries
    let fullDayEntry = intervalEntries.fullDayEntry

    for entry in intervalEntries {
        let memoryMetrics = entry.values.filter { $0.metricGroup == .memory }

        for metric in memoryMetrics {
            switch metric {
            case .peakMemory(let peak):
                processPeakMemory(peak)
            default: break
            }
        }
    }
}
```

Do this work in a **detached task or dedicated service class** at launch.

### Analysis is a data-science problem

Aggregating metrics across all devices is on you: set up a server to ingest reports,
aggregate along the dimensions you care about, pick the right statistical analysis,
establish a baseline, then monitor for regressions/improvements.

## 3. Diagnostics — triaging crashes and hangs

When something goes wrong (crash, hang, etc.) the system captures a diagnostic **on
device** and delivers it **immediately** through MetricKit. Diagnostics carry the detail
you need to triage — including **backtraces** showing the exact call stack at the time of
the event.

**Crash diagnostics** specifically provide:

- a **symbolicated backtrace** (`callStackTree`) — read it top-down from system
  `thread start` into your app's code until execution stops at the crash site (the talk's
  example bottoms out in the app's `submitReport()`),
- a **termination reason** (why the app was terminated),
- an **exception type** (what kind of failure),
- new in iOS 27, a **`terminationCategory`** that indicates *how each crash was accounted
  for in metrics* — so if abnormal terminations trend up, you can correlate the trend with
  individual diagnostics.

Also new in iOS 27: **memory exception diagnostics**, delivered when your app or extension
is terminated for exceeding its memory limit.

### Receive diagnostics

```swift
import MetricKit

let manager = MetricManager()

for await report in manager.diagnosticReports {
    processReport(report)
}
```

### Send to a server (also Codable)

```swift
import MetricKit

for await report in manager.diagnosticReports {
    let jsonData = try JSONEncoder().encode(report)
    sendToServer(jsonData)
}
```

### Pick apart by type

`report.result` switches over diagnostic cases; crash exposes backtrace, reason, category:

```swift
import MetricKit

for await report in manager.diagnosticReports {
    switch report.result {
    case .crash(let crash):
        let backtrace = crash.callStackTree
        let reason = crash.terminationReason
        let category = crash.terminationCategory
        processCrash(backtrace: backtrace, reason: reason, category: category)
    case .hang(let hang):
        processHangDiagnostic(hang)
    default: break
    }
}
```

## 4. Context — the State Reporting framework

The motivating problem: a single **blended** metric hides where the problem is. The
expense-app demo reports a scroll **hitch rate of 15 ms/s** averaged over all usage — but
that number mixes the Reports tab and the Spending tab. Split by state and the story
flips: Spending scrolls at **1 ms/s** (smooth), Reports at **71 ms/s** (critical). Now you
know exactly where to optimize.

**States** are information *you* define describing your app's configuration or behavior,
so MetricKit can aggregate metrics (and diagnostics) **as a function of** those
characteristics.

### Domains and the transition model

- Each state is scoped to a **domain** (a function/area of the app, typically a reverse-DNS
  string).
- A domain has **exactly one active state at a time**. Use **separate domains** to track
  multiple independent dimensions concurrently — e.g. one domain for the active tab, a
  separate domain for an experiment's batch-size setting; MetricKit then delivers metrics
  split by each tab *and* each batch size.
- States use a **transition model**: you report the state you're *moving to*, and MetricKit
  tracks how long you stay there. There are **no start/end pairs** — you just report the
  current condition.

### Reporting transitions

```swift
import MetricKit
import StateReporting

let domain = StateReportingDomain("com.metrickitsample.tabs")
let manager = MetricManager(enabledStateReportingDomains: [domain])

let reporter = StateReporter.reporter(for: domain.rawValue)
reporter.reportTransition(to: "Reports")
```

### Custom structured metadata with `@ReportableMetadata`

Attach extra structured detail per state by defining a struct with the
`@ReportableMetadata` macro, creating a reporter bound to that type, and passing an
instance on each transition:

```swift
import StateReporting

@ReportableMetadata
struct ViewConfiguration {
    let listSize: String
    let isSorted: Bool
}

let reporter = StateReporter.reporter(
    for: domain.rawValue,
    stableMetadata: ViewConfiguration.self
)

reporter.reportTransition(
    to: "Reports",
    stableMetadata: ViewConfiguration(listSize: "large", isSorted: false)
)
```

### Reading per-state metrics

- Before you report any states, the report carries broad metrics; the new `stateEntries`
  property is **empty**.
- Once you report states, `MetricReport` gains **`StateEntry`** values — each state gets a
  `StateEntry` whose metric values are aggregated over the time spent in that state.

### Encoding grouped by domain

When shipping to your analytics server you can group output by domain. Set
`encodingFormatKey` on the encoder's `userInfo` to `.byStateReportingDomain`:

```swift
import MetricKit

for await report in manager.metricReports {
    let encoder = JSONEncoder()

    let formatKey = MetricReport.encodingFormatKey
    encoder.userInfo[formatKey] = MetricReport.EncodingFormat.byStateReportingDomain

    let jsonData = try encoder.encode(report)
    sendToServer(jsonData)
}
```

Both `stateEntries` and `intervalEntries` then come out grouped by each domain and state.

### Best practices for states

- **Narrowly scope domains** so each app area owns its state.
- States should be **stable, meaningful phases — not transient UI events.**
- Design each state so that if a regression appears, the state alone gives you enough to
  target the fix.
- **Plan the number** of transitions: too many states → data too granular to interpret,
  and there are **upper limits** to minimize overhead.
- Validate with the **Points of Interest instrument** that the states you report match
  expectations **before you ship.**

## 5. Wrap-up / migration

- Use `MetricManager` to start collecting metrics and monitoring health.
- Analyze diagnostics to find specific fixes.
- Contextualize with State Reporting.
- Explore the new data types: **memory diagnostics** and the **Metal frame rate** metric.
- If you're on **`MXMetricManager`**, **migrate to `MetricManager`** — all the new
  capabilities are exclusive to the new API.
