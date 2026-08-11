---
title: Perf Monitor
seoTitle: "Perf Monitor — setup, config & API"
id: flutter-tools-perf-monitor
description: "Watch your Flutter app's performance on a real device — a live HUD with FPS, jank, CPU, and memory, streaming to the Buoy Desktop dashboard. Pure Dart, no native code."
---

Watch performance on a real device. The Perf Monitor is a live on-device HUD showing FPS, jank, CPU, and memory while you use the app — streamed to the [Buoy Desktop](../../desktop) dashboard so you can watch it full-size while you drive the phone.

Don't take our word for it — this is the real tool UI, running here on mock HUD + batch data. Walk the guided tour, or skip it and start tapping:

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

*Looking for an overview with screenshots and FAQs? See the [Bench — Performance Monitor page on buoy.gg](https://buoy.gg/tools/perf-monitor).*
