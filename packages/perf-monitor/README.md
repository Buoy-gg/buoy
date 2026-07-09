# @buoy-gg/perf-monitor

[![npm version](https://img.shields.io/npm/v/@buoy-gg/perf-monitor?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/perf-monitor) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/perf-monitor?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/perf-monitor)

**Bench — benchmark React Native performance on a real device: UI/JS FPS, CPU, memory, and jank, with recorded runs you can compare to prove what's actually faster.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules
```

Bench relies on native peers:

- **Required:** `react-native-reanimated` (>= 4) and `react-native-worklets` drive the UI-thread HUD counters. Add `react-native-worklets/plugin` to your Babel config.
- **Optional but recommended:** `react-native-performance-toolkit` + `react-native-nitro-modules` unlock true native UI-thread FPS, real CPU %, and process memory. These ship native code — rebuild with a **dev build** (`npx expo prebuild` then `npx expo run:ios` / `run:android`, plus `pod install` on bare iOS), not Expo Go.
- **Without the toolkit**, Bench still runs in JS-fallback mode (rAF-based JS FPS, approximated UI FPS, JS heap; no CPU) and shows a one-time notice explaining what's missing.

Requires React Native >= 0.76.

## Quick start

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools />
    </>
  );
}
```

Bench appears in the floating menu: one entry toggles the live HUD, another opens the recorder/report modal. On web (Expo web, Electron, any React DOM app) no native modules are needed — render the HUD directly:

```tsx
import { PerfMonitorOverlay, PerfMonitorController } from "@buoy-gg/perf-monitor";

<PerfMonitorOverlay />;          // anywhere in your tree
PerfMonitorController.toggle();  // show/hide
```

## What you get

- **Live HUD on a real device** — UI FPS, JS FPS, CPU, memory, and jank updating in real time as you use the app; the HUD reads UI-thread shared values, so it keeps moving even when JS is fully blocked.
- **Record and compare runs** — capture a session, save it, then select saved runs to compare side-by-side (duration, memory, JS FPS, CPU) and prove a change made things faster.
- **Batch benchmarks** — run the same flow across several implementation variants and get a ranked report with per-metric leaders and at-risk flags.
- **Render capture** — with [`@buoy-gg/highlight-updates`](https://www.npmjs.com/package/@buoy-gg/highlight-updates) installed, recordings capture per-component render counts and durations, so reports say "case B dropped to 41 JS FPS because `ProductList` rendered 47× costing 312ms" instead of just the number.
- **AI-driven optimization** — the MCP server's `run_benchmark_batch` tool and `buoy-optimize` wizard let your AI assistant measure on-device, apply a change, and re-measure in a loop.
- **Web mode** — rAF frame rate, a BUSY long-task % (the "why does it feel janky" number), JS heap, and a PAGES section that ranks your slowest routes by FPS.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) — including a live HUD with Start/Stop recording — and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/perf-monitor) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
