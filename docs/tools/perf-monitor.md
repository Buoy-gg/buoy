---
title: Bench
id: tools-perf-monitor
description: "Benchmark React Native performance on a real device — track UI/JS FPS, CPU, memory, and jank, record runs, and compare variants to prove what's faster."
---

<!-- ::platform-badge platform="both" -->

Measure and prove performance on a real device. Bench tracks UI FPS, JS FPS, CPU, memory, and jank in real time, records runs you can save and compare, and can benchmark a matrix of variants to tell you which implementation is actually faster.

<!-- ::perf-monitor-demo -->

## Installation

Bench relies on native peers. Install the package with **Reanimated** and **Worklets** (required), plus the **performance toolkit** and **Nitro modules** that power true native UI-thread metrics:

<!-- ::PM npm="npm install @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" yarn="yarn add @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" pnpm="pnpm add @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" bun="bun add @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" -->

These ship native code, so rebuild the app with a custom **dev build** (`expo prebuild` then `npx expo run:ios` / `run:android`) — **not Expo Go** — and run `pod install` on iOS. See [Reanimated's setup](https://docs.swmansion.com/react-native-reanimated/) for its Babel plugin. Without `react-native-performance-toolkit`, Bench still runs in JS-only mode; adding it unlocks native UI FPS and CPU.

Once installed, Bench appears in the floating menu. On [Buoy Desktop](../desktop) it also renders as a live HUD you can watch while you use the app.

### Web

Bench also runs in the browser — Expo web, Electron, or any React DOM app — with **no native modules and no dev build**. On web the HUD samples browser APIs instead:

- **FPS** — main-thread frame rate (`requestAnimationFrame`). On web there's a single thread, so JS FPS and UI FPS read the same value.
- **BUSY** — replaces the CPU row: the % of recent time the main thread was blocked by [long tasks](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceLongTaskTiming) (>50ms). This is the "why does it feel janky" number.
- **JS heap** — `performance.memory` (Chromium-based browsers; the row hides elsewhere).
- **PAGES** — the full HUD ranks your slowest routes by average FPS while each was active (browser-history route tracking — works with React Router, Next, hash routers, or plain `pushState`). Watch it while you click around to see exactly which pages are slow.

React Native Web apps get this automatically. Plain React apps need the standard one-line bundler alias (`react-native` → `react-native-web`) and can render the HUD directly:

```tsx
import { PerfMonitorOverlay, PerfMonitorController } from "@buoy-gg/perf-monitor";

// anywhere in your tree
<PerfMonitorOverlay />
// toggle it
PerfMonitorController.toggle();
```

---

## What You Can Do

- **Live HUD** — Watch UI FPS, JS FPS, CPU, and memory update in real time as you navigate.
- **Record runs** — Capture a session, save it, and keep a library of recordings.
- **Compare** — Long-press to select two or more saved runs and see them side-by-side in the same bar-chart report batches get (duration, memory, JS FPS, CPU) to prove a change made things faster.
- **Batch benchmarks** — Run the same flow across several variants and get a ranked report with per-metric leaders and at-risk flags.
- **Render capture** — See *why* a run was slow: every recording also captures per-component render counts and durations (React-profiler-style), so reports show the top re-rendering components next to the FPS numbers.

### Stall reconstruction

The sampler runs on the JS thread, so it physically can't take samples *while* the JS thread is blocked. Instead of silently skipping those moments (which would make stalls invisible in recordings and on the desktop dashboard's live HUD), the monitor detects the gap when sampling resumes and backfills it with reconstructed `0 JS FPS` samples — so history sparklines, live sync, and saved reports all show the stall. Reconstructed samples are marked `synthetic` in report JSON, counted in the report's stats, and never fabricated across app backgrounding. One consequence worth knowing: recordings made before this behavior existed under-report JS stalls, so a stall-heavy run recorded today will (honestly) score worse than the same behavior recorded on an older version.

---

## Render Capture

With [`@buoy-gg/highlight-updates`](./highlight-updates) installed, every recording — manual or batch — also captures **which components rendered, how many times, and how long they took** (self time, React DevTools profiler convention). Batch reports gain a *Top re-renderers* section per case, single-run reports get a *Render commits* block, and the MCP `run_benchmark_batch` ranking includes the heaviest components per case — so instead of "case B dropped to 41 JS FPS" you get "case B dropped to 41 JS FPS **because `ProductList` rendered 47× costing 312ms**".

A few things to know:

- Requires a **dev build** (the React DevTools hook and profiling timers aren't present in release builds). Without them the run simply has no render data — nothing breaks.
- Capture walks committed fibers on the JS thread, adding a small overhead per commit. It's applied **uniformly to every case** in a batch, so relative comparisons stay fair — but for absolute FPS measurements you can turn it off: the **Capture render commits** toggle in the tool's Settings covers manual recordings, the **Capture renders** toggle in Automate settings covers batches, and `captureRenders: false` works on `run_benchmark_batch`.
- Buoy's own devtools UI (the HUD, floating menu, etc.) is excluded from results automatically, and React Native framework wrappers (`View`, `Text`, `Animated(View)`, Touchable internals, VirtualizedList cells, …) are folded into the run totals instead of cluttering the component list — the rows you see are your components.
- Want to *watch* the renders as they happen? Turn on **Show live render highlights** in the tool's Settings (off by default) — while a recording is running, the [Render Highlighter](./highlight-updates)'s bounding boxes flash in real time, then the overlay returns to whatever state it was in. The boxes themselves cost real frame time, so leave this off when you care about the numbers.

---

## Optimize with your AI

Bench powers Buoy's AI performance workflow. The [MCP server](../mcp) ships a **`buoy-optimize` wizard skill** that turns the whole tune → measure → repeat loop into an almost hands-off process. Point your assistant at a new feature you're building — or an existing screen that's slow on device — and it will:

- Navigate to the screen and benchmark a matrix of implementation variants with `run_benchmark_batch`
- Read the ranked comparison (FPS, CPU, memory, jank) from the **real device**, pick the winner, and apply the next optimization
- Loop — measure, change, re-measure — until the numbers stop improving

Because it measures on-device instead of guessing, it collapses what is normally days or weeks of AI back-and-forth into minutes. In one real pass, a Skia LED display went from **28 lights stuttering** under advanced effects to **over 12,000 lights with no lag**.

The only steps that stay manual are the ones a human has to eyeball — mainly confirming the result still *renders correctly*. If you're on Skia or other GPU-drawn UI, plan to glance at the screen between passes: the wizard drives the metrics, you verify it still looks right. Say **"buoy optimize"** in your editor to start.

---

## What's Next

- [AI / MCP Server](../mcp) — Automate benchmarks with an AI agent
- [Render Highlighter](./highlight-updates) — Find the re-renders hurting performance
- [Events Timeline](./events) — See performance in context with everything else
