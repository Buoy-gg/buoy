---
title: Native Profiler
seoTitle: "Native Profiler for iOS and Android"
id: tools-native-profiler
description: "Trace native CPU work with MCP or Buoy Desktop. Find hot functions and long main-thread work."
---

<!-- ::platform-badge platform="ios" -->
<!-- ::platform-badge platform="android" -->

Find slow native work when JS looks fine. Take a short trace from MCP or Buoy Desktop. The report shows hot functions and long main-thread work.

Use an iOS Simulator or an Android app. Both need a connected Buoy Pro app. iOS needs macOS and Xcode. Android needs adb on Mac or Linux. The app needs no new package or native marks. Real iPhones are not supported yet.

Tested with Xcode 27.0 and iOS 26.3.1 on a sim. This sim uses CPU samples to find busy spans. Android tests use an API 36 emulator. It captured stacks, RSS, and frame rows.

## Take a trace

In Desktop, pick the app, then open Native Profiler. Press Record and use the app. The trace stops after ten seconds. Press Stop to end it sooner. Closing the panel stops its run too.

MCP has two tools:

```json
{"name":"native_profile","arguments":{"action":"start","deviceId":"your-device-id","durationMs":15000}}
```

Wait for the `recording` state. Run the flow you want to check. Then stop with the returned ID, even if the flow fails.

```json
{"name":"native_profile","arguments":{"action":"stop","profileId":"returned-profile-id"}}
```

Read it again with `get_native_profile` and that `profileId`. Use `action: "record"` for a timed run. It waits for the trace to finish. Arrange app use during that call.

Runs last one to 60 seconds. Timed runs default to ten seconds. Start defaults to a 60-second cap. Set `thresholdMs` to choose the busy-span limit. It defaults to 250 ms; the range is 100 to 5,000 ms.

## Read the report

Each busy span gives its start, length, and method:

- `os-hang`: Xcode found a hang.
- `sample-density`: dense main-thread CPU samples show sustained work.
- `sched-busy`: Android UI CPU spans show sustained work.

The sample and scheduler methods are estimates. At least 60% of each span must show CPU work. Gaps over ten ms split a span. It cannot find a sleeping or blocked thread. A trace mark alone never counts as a stall.

Hot functions rank by sampled self time. That counts the leaf frame, so stack frames are not counted twice. On iOS, main-thread busy percent is a sample estimate. Android uses scheduler CPU time for that percent. Sim results measure this Mac, not a phone's speed.

Android tries to take function samples at 100 Hz. If none are found, hot threads show CPU time. The report labels this source as `sched`. It does not pass thread names off as functions.

The report keeps ten hot functions and ten busy spans. Android also shows up to ten hot threads. Totals show how many were found. Missing CPU data stays unknown. Memory shows RSS at the start, peak, and end. Growth is end minus start, so it can be negative. iOS reads host RSS with `ps` four times per second. Its source is `host-ps-rss`; this is not heap size. Android reads RSS from `linux.process_stats` at the same rate. Its source is `perfetto-process-stats`.

Android frame counts come from `frame-timeline`. They count app frames once across layers. Jank and drop counts need at least 30 frames. Fewer rows show "too few frames to judge" and the count. Their `status` is `too-few-frames`; jank and drop counts are `null`. With 30 or more, `status` is `captured`. These counts describe this trace, not the whole app. No frame rows means unknown, not zero dropped frames. These rows need Android 12 or newer. iOS frame counts stay unknown. Use [Bench](./perf-monitor.md) for its supported metrics. Use [JS TOP](./js-top.md) to check JS work.

## Files and limits

Raw traces and reports stay on this host. The report gives their paths. XML is never sent to the agent. Reports stay under 16 KiB. Each raw trace has a 256 MiB cap.

One host run may trace a device at a time. Stop it in the host that started it. The host keeps up to ten finished runs for one day. Older files are pruned when a run ends.

Xcode gets one retry for a 45-second attach timeout. The report shows `attachRetries`, either zero or one. No other error gets a retry. A second timeout still fails. The error names the Xcode/CoreSimulator attach issue. It gives commands for the chosen app and sim. First relaunch that app and try again. If needed, reboot just that sim and retry. App data stays in place. If no CPU rows are found, the run fails too. Try a shorter trace while the app does some work. Do not read an empty trace as proof that the app is fast.

Android uses a pinned `trace_processor` binary, version `v52.0`. It is fetched once, before the trace starts. Each use checks its SHA-256 hash. It never fetches a moving "latest" build during a trace.

See the [Perfetto CPU guide](https://perfetto.dev/docs/getting-started/cpu-profiling) and [frame guide](https://perfetto.dev/docs/data-sources/frametimeline) for data source limits. The pinned hashes come from [Perfetto's fixed manifest](https://github.com/google/perfetto/blob/v53.0/tools/trace_processor).

Ask Buoy support is a follow-up. Use MCP or Desktop for now.
