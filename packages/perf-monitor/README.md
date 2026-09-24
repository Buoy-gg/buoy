# @buoy-gg/perf-monitor

[![npm version](https://img.shields.io/npm/v/@buoy-gg/perf-monitor?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/perf-monitor) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/perf-monitor?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/perf-monitor)

Measure available frame-rate, CPU, and memory metrics on a device. Record comparable runs to investigate performance changes.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules
```

Bench relies on native peers:

- **Required:** `react-native-reanimated` (>= 4) and `react-native-worklets` drive the UI-thread HUD counters. Add `react-native-worklets/plugin` to your Babel config.
- **Optional but recommended:** `react-native-performance-toolkit` + `react-native-nitro-modules` unlock true native UI-thread FPS, real CPU %, and process memory. These ship native code — rebuild with a **dev build** (`npx expo prebuild` then `npx expo run:ios` / `run:android`, plus `pod install` on bare iOS), not Expo Go.
- **Without the toolkit**, Bench still runs in JS-fallback mode (rAF-based JS FPS, approximated UI FPS, JS heap; no CPU) and shows a one-time notice explaining what's missing.

Requires React Native >= 0.76.

## Before you start

Use a development build with `@buoy-gg/core` and a Free or Pro Buoy account key. From your app’s directory, sign in:

```bash
npx --package=@buoy-gg/core buoy login
```

For Expo, initialize Buoy before rendering the menu:

```tsx
import { Buoy } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });
```

The login command writes the Expo key to `.env.local`. For React Native CLI, pass the key from your app’s environment configuration; React Native does not load `.env.local` automatically. Mount `FloatingDevTools` inside the same providers as your screens and restart the development server after installation. The [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) shows the complete root component setup.

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

## Check the integration

Repeat the same screen interaction on the same device and build, then compare runs. Available metrics depend on platform and installed native modules. Render capture requires the development-only React hooks; do not compare it as if it were available in release builds.

## What you get

- **Live HUD on a real device** — UI FPS, JS FPS, CPU, memory, and jank updating in real time as you use the app; the HUD reads UI-thread shared values, so it keeps moving even when JS is fully blocked.
- **Record and compare runs** — capture a session, save it, then select saved runs to compare side-by-side (duration, memory, JS FPS, CPU) and prove a change made things faster.
- **Batch benchmarks** — run the same flow across several implementation variants and get a ranked report with per-metric leaders and at-risk flags.
- **Render capture** — with [`@buoy-gg/highlight-updates`](https://www.npmjs.com/package/@buoy-gg/highlight-updates) installed, recordings capture per-component render counts and durations, so reports say "case B dropped to 41 JS FPS because `ProductList` rendered 47× costing 312ms" instead of just the number.
- **AI-driven optimization** — the MCP server's `run_benchmark_batch` tool and `buoy-optimize` wizard let your AI assistant measure on-device, apply a change, and re-measure in a loop.
- **Web mode** — rAF frame rate, a BUSY long-task % (the "why does it feel janky" number), JS heap, and a PAGES section that ranks your slowest routes by FPS.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/perf-monitor) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Browser measurements use frame timing, available JS heap data, and long tasks. Native CPU, RSS, and thermal measurements remain device-specific. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
