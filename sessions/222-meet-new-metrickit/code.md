# Code samples — Session 222

Extracted from the Code tab. Timestamps map to the transcript where identifiable.

## Receive metric reports

```swift
import MetricKit

let manager = MetricManager()

for await report in manager.metricReports {
    processReport(report)
}
```

## Send metric reports to a server

```swift
import MetricKit

for await report in manager.metricReports {
    let jsonData = try JSONEncoder().encode(report)
    sendToServer(jsonData)
}
```

## Inspect a metric report (filter by group, switch on value)

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

## Receive diagnostic reports

```swift
import MetricKit

let manager = MetricManager()

for await report in manager.diagnosticReports {
    processReport(report)
}
```

## Send diagnostic data to a server

```swift
import MetricKit

for await report in manager.diagnosticReports {
    let jsonData = try JSONEncoder().encode(report)
    sendToServer(jsonData)
}
```

## Inspect diagnostics (switch on result; extract crash backtrace/reason/category)

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

## Enable state reporting and report a transition

```swift
import MetricKit
import StateReporting

let domain = StateReportingDomain("com.metrickitsample.tabs")
let manager = MetricManager(enabledStateReportingDomains: [domain])


// Report transitions throughout the app

let reporter = StateReporter.reporter(for: domain.rawValue)
reporter.reportTransition(to: "Reports")
```

## Custom structured state metadata with @ReportableMetadata

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

## Encode metric reports grouped by state reporting domain

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

---

## Useful API facts surfaced by the code

- Entry point: `MetricManager()`; reports arrive via `metricReports` and `diagnosticReports` async streams (`for await`). Keep the manager alive; subscribe at launch on a detached task / dedicated service class.
- Metric report shape: `report.intervalEntries` (an `intervalEntries.fullDayEntry` plus optional smaller windows) → `entry.values` → filter by `metric.metricGroup` (`.memory`, `.cpu`, `.display`, `.gpu`) → switch over metric cases (e.g. `.peakMemory`).
- Diagnostic report shape: `report.result` switched over `.crash` / `.hang` / … . Crash exposes `callStackTree`, `terminationReason`, and the new `terminationCategory`.
- State reporting lives in a separate `StateReporting` module: `StateReportingDomain`, `StateReporter.reporter(for:)`, `reporter.reportTransition(to:)`. Register domains via `MetricManager(enabledStateReportingDomains:)`.
- Custom metadata: `@ReportableMetadata` macro on a struct; pass the type as `stableMetadata:` to the reporter and an instance to `reportTransition(to:stableMetadata:)`.
- Encoding by state: `encoder.userInfo[MetricReport.encodingFormatKey] = MetricReport.EncodingFormat.byStateReportingDomain`.
- Reports are `Codable` (JSON via `JSONEncoder`).
