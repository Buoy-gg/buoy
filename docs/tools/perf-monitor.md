---
title: Bench
id: tools-perf-monitor
---

<!-- ::platform-badge platform="both" -->

Measure and prove performance on a real device. Bench tracks UI FPS, JS FPS, CPU, memory, and jank in real time, records runs you can save and compare, and can benchmark a matrix of variants to tell you which implementation is actually faster.

<!-- ::perf-monitor-demo -->

## Installation

Bench relies on native peers. Install the package with **Reanimated** and **Worklets** (required), plus the **performance toolkit** and **Nitro modules** that power true native UI-thread metrics:

<!-- ::PM npm="npm install @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" yarn="yarn add @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" pnpm="pnpm add @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" bun="bun add @buoy-gg/perf-monitor react-native-reanimated react-native-worklets react-native-performance-toolkit react-native-nitro-modules" -->

These ship native code, so rebuild the app with a custom **dev build** (`expo prebuild` then `npx expo run:ios` / `run:android`) — **not Expo Go** — and run `pod install` on iOS. See [Reanimated's setup](https://docs.swmansion.com/react-native-reanimated/) for its Babel plugin. Without `react-native-performance-toolkit`, Bench still runs in JS-only mode; adding it unlocks native UI FPS and CPU.

Once installed, Bench appears in the floating menu. On [Buoy Desktop](../desktop) it also renders as a live HUD you can watch while you use the app.

---

## What You Can Do

- **Live HUD** — Watch UI FPS, JS FPS, CPU, and memory update in real time as you navigate.
- **Record runs** — Capture a session, save it, and keep a library of recordings.
- **Compare** — Long-press to select two or more saved runs and see them side-by-side in the same bar-chart report batches get (duration, memory, JS FPS, CPU) to prove a change made things faster.
- **Batch benchmarks** — Run the same flow across several variants and get a ranked report with per-metric leaders and at-risk flags.

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
