<div align="center">
  <h1>@buoy-gg/js-top</h1>
  <p><b>A live Task Manager for the React Native JS thread.</b></p>
  <p>See which timers, Promise chains, and callbacks are eating your JS FPS — ranked in real time, like Activity Monitor for your app's JS thread.</p>
  <p>
    <a href="https://buoy.gg/buoy/latest/docs/tools/js-top">Docs</a> ·
    <a href="https://buoy.gg">buoy.gg</a>
  </p>
</div>

---

JS Top ranks measured callback time. For example, a task table might look like this (illustrative values):

```
#  TASK ORIGIN                        5S MS   %BUSY  CALLS  AVG   MAX
1  setInterval ← startPolling         1240    41.2   50     24.8  31.0
2  Promise.then ← processTodos         610    20.3   10     61.0  75.2
3  requestAnimationFrame ← rafLoop     402    13.4   300    1.3   4.1
4  unattributed (events / React)       255     8.5   –      –     –
```

- **Pure JavaScript.** No native module, no debugger, no dev client — works in **Expo Go** and even release builds.
- **Capture lifecycle.** The engine only runs while the tool is open (or a Buoy Desktop dashboard is watching).
- **Honest accounting.** Exclusive-time bookkeeping (nested callbacks never double-count), a calibrated probe that estimates thread occupancy, and an explicit *unattributed* row for work no JS wrapper can see.
- **Blocking-task attribution.** Every 50ms+ freeze (via React Native's `longtask` observer) is matched to the callback that overlapped it.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/js-top
```

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

After setup, open JS Top, run a known timer-driven interaction, and inspect the task table. Explicit registration:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { jsTopPreset, jsTopModalPreset } from "@buoy-gg/js-top";

<FloatingDevTools apps={[jsTopModalPreset, jsTopPreset]} />
```

## How it works

You can't sample a blocked JS thread from JS — so JS Top doesn't sample. It wraps supported scheduling entry points (`setTimeout` / `setInterval` / `setImmediate` / `requestAnimationFrame` / `queueMicrotask` / Promise reactions / legacy-bridge call-ins) and deterministically times each callback, attributing cost to **where the callback was scheduled from** — with lazy stack capture so hot origins get named `setInterval ← startPolling` while limiting stack-capture work.

On the New Architecture, touch/React-commit work enters through paths pure JS can't wrap; that time shows as *unattributed* (the in-tool banner explains). Work outside wrapped paths, including some async/await continuations and native entry points, can remain unattributed.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Remote capabilities

- The desktop dashboard mirrors the device's task table live, and adds a dev-only **per-function Hermes sampler** (attaches to Metro's inspector).
- Via the Buoy MCP server, your AI agent can call `get_js_thread_top` — "why is JS FPS low?" becomes one tool call.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

## License

Part of [React Buoy](https://buoy.gg). See buoy.gg for licensing.

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
