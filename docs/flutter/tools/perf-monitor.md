---
title: Perf Monitor
seoTitle: "Flutter Performance Monitor — FPS, jank, CPU & memory"
id: flutter-tools-perf-monitor
description: "Watch your Flutter app's performance on a real device — a live HUD with FPS, jank, CPU, and memory, streaming to the Buoy Desktop dashboard. Pure Dart, no native code."
---

Watch performance on a real device. The Perf Monitor is a live on-device HUD showing FPS, jank, CPU, and memory while you use the app — streamed to the [Buoy Desktop](../../desktop) dashboard so you can watch it full-size while you drive the phone.

The React Native build of this tool, running here on mock data. The Flutter port ships the same panels — walk the tour, or skip it and start tapping.

<!-- ::perf-monitor-live-demo -->

## Installation

<!-- ::pub package="buoy_perf_monitor" -->

Pure Dart — no native libraries, no FFI, no platform channels, no rebuild. Using the [`buoy` umbrella](../installation)? It's already registered. Standalone:

```dart
import 'package:buoy_perf_monitor/buoy_perf_monitor.dart';

void main() {
  if (kDebugMode) registerBuoyPerfMonitor();
  runApp(const MyApp());
}
```

Open the **PERF** tool from the floating dial to see live metrics and toggle the HUD.

---

## What It Measures

- **FPS + jank** — from `SchedulerBinding.addTimingsCallback`: build (Dart UI thread) and raster (GPU thread) frame timings. FPS is activity-gated: an idle Flutter app renders no frames, so the HUD shows `—` at rest — honest, not a fake 60.
- **Memory (RSS)** — from `dart:io`'s `ProcessInfo.currentRss`, sampled continuously even while the UI is still.
- **CPU** — from `/proc/self/stat` on Android (no pure-Dart source exists on iOS yet).

---

## Coming Soon

**Benchmarking and batch reports** — recorded, comparable runs with ranked reports, like the React Native [Bench](../../tools/perf-monitor) tool — plus **Dart Top**, a "what's eating the thread" profiler. [Vote on the roadmap](https://buoy.gg/roadmap).

---

## What's Next

- [Buoy Desktop](../../desktop) — Watch the live HUD full-size on your desktop
- [Events Timeline](./events) — Performance in context with everything else
- [Network Monitor](./network) — Find the slow requests behind the jank

---

## FAQ

### How do I measure FPS and jank in a Flutter app on a real device?

Add `buoy_perf_monitor` and call `registerBuoyPerfMonitor()` — a live on-device HUD reports build (Dart UI thread) and raster (GPU thread) frame timings from `SchedulerBinding.addTimingsCallback`, plus memory (RSS) and, on Android, CPU. It streams to Buoy Desktop so you can watch it full-size while you drive the phone.

### Why does FPS show a dash when the app is idle?

FPS is activity-gated. An idle Flutter app renders no frames, so the HUD shows `—` at rest rather than a fake 60.

### Does it need native code?

No — it's pure Dart. No native libraries, no FFI, no platform channels, and no rebuild.
