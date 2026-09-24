---
title: JS Top
seoTitle: "React Native JS Thread Profiler — what's eating JS FPS"
id: tools-js-top
description: "A live Task Manager for the React Native JS thread — see which timers, Promise chains, and callbacks are eating your JS FPS, ranked in real time. Works in Expo Go and release builds."
---

<!-- ::platform-badge platform="both" -->

Rank the callback origins JS Top can observe by execution time and call count. Compare timers, animation callbacks, and Promise reactions while reproducing a slow interaction. Work outside the wrapped paths appears as unattributed.

The tool uses JavaScript instrumentation and can run in Expo Go. Production access requires Pro.

<!-- ::js-top-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/js-top" yarn="yarn add @buoy-gg/js-top" pnpm="pnpm add @buoy-gg/js-top" bun="bun add @buoy-gg/js-top" -->

With auto-discovery, installing the package is all you need — the JS TOP tool and its toggleable busy-pill HUD appear in your floating menu automatically. Or register it explicitly:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { jsTopPreset, jsTopModalPreset } from "@buoy-gg/js-top";

<FloatingDevTools apps={[jsTopModalPreset, jsTopPreset]} />
```

---

## How it works

You can't sample a blocked JS thread from JavaScript — so JS Top doesn't sample. Instead it wraps every entry point work can take onto the thread (`setTimeout`, `setInterval`, `setImmediate`, `requestAnimationFrame`, `queueMicrotask`, Promise reactions, and legacy-bridge call-ins) and measures each callback precisely, attributing the time to *where the callback was scheduled from*.

Alongside that, a calibrated high-frequency probe measures **estimated thread occupancy** from timer-gap inflation, and React Native's built-in `longtask` observer flags every 50ms+ block. Anything the wrappers can't see shows up honestly as an **unattributed** row — the tool never pretends to a coverage it doesn't have.

- **Inactive when closed.** The engine only runs while the tool is open (or a desktop dashboard is watching).
- **Exclusive-time accounting.** Nested callbacks never double-count; totals always add up.
- **Blocking-task attribution.** Each 50ms+ stall is matched to the callback that overlapped it.

## What You Can Do

- **Rank live JS-thread cost** — Sort by recent ms, calls, average, max, or total.
- **Catch the runaway interval** — A polling loop someone forgot shows up in seconds, named after the function that scheduled it.
- **See thread busy%** — A live meter plus 30s history sparkline, honest even through multi-second stalls.
- **Read blocking tasks** — Every 50ms+ freeze, with the worst-offending origin named.
- **Watch from the desktop** — The Buoy Desktop panel mirrors the device table live, and adds a dev-only per-function Hermes sampler for function-level flame data.

---

## Ask your AI what's eating the thread

With the [MCP server](../mcp), an agent can call `get_js_thread_top` — the device samples for a few seconds and returns the ranked table, so "why is JS FPS low?" becomes a one-tool-call answer.

---

## Coverage notes

- On the New Architecture (bridgeless), touch handlers and React commit work enter the thread through paths pure JS can't wrap — that time appears as **unattributed** (the banner in the tool explains this). Timers, rAF, microtasks, and Promise chains are attributed when they use the wrapped paths.
- Hermes runs `async/await` continuations through an internal path that bypasses `.then` — async function bodies also land in unattributed.

## What's Next

- [Performance Monitor](./perf-monitor) — Benchmarks, FPS/CPU/memory recording, automation
- [Highlight Updates](./highlight-updates) — See which components re-render
- [AI / MCP Server](../mcp) — Let an agent profile the thread for you

---

## FAQ

### How do I find what's blocking the JS thread in React Native?

Install `@buoy-gg/js-top` and open JS TOP — it ranks every task origin (`setInterval ← startPolling`, `Promise.then ← api.ts`, `requestAnimationFrame ← rafSpinLoop`) by the time it consumed, so the runaway timer or callback appears in the ranking when captured.

### Does it need a native module, dev client, or attached debugger?

No. JS Top is pure JavaScript — it wraps the entry points work takes onto the thread instead of sampling, so it runs in Expo Go, dev builds, and release builds with no debugger attached.

### Why is some time reported as "unattributed"?

On the New Architecture, touch handlers and React commit work enter the JS thread through paths pure JavaScript can't wrap. That time is reported honestly as unattributed rather than being blamed on the wrong origin. Timers, rAF, microtasks, and Promise chains are attributed when they use the wrapped paths.

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
