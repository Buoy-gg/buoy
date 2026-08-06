<div align="center">
  <h1>@buoy-gg/js-top</h1>
  <p><b>A live Task Manager for the React Native JS thread.</b></p>
  <p>See which timers, Promise chains, and callbacks are eating your JS FPS — ranked in real time, like Activity Monitor for your app's JS thread.</p>
  <p>
    <a href="https://buoy.gg/tools/js-top">Docs</a> ·
    <a href="https://buoy.gg">buoy.gg</a>
  </p>
</div>

---

Every React Native developer has watched JS FPS tank and asked: **what is using it all?** JS Top answers live:

```
#  TASK ORIGIN                        5S MS   %BUSY  CALLS  AVG   MAX
1  setInterval ← startPolling         1240    41.2   50     24.8  31.0
2  Promise.then ← processTodos         610    20.3   10     61.0  75.2
3  requestAnimationFrame ← rafLoop     402    13.4   300    1.3   4.1
4  unattributed (events / React)       255     8.5   –      –     –
```

- **Pure JavaScript.** No native module, no debugger, no dev client — works in **Expo Go** and even release builds.
- **Zero overhead when closed.** The engine only runs while the tool is open (or a Buoy Desktop dashboard is watching).
- **Honest accounting.** Exclusive-time bookkeeping (nested callbacks never double-count), a calibrated busy probe for true thread occupancy, and an explicit *unattributed* row for work no JS wrapper can see.
- **Blocking-task attribution.** Every 50ms+ freeze (via React Native's `longtask` observer) is matched to the callback that overlapped it.

## Install

```bash
npm install @buoy-gg/js-top
```

With Buoy auto-discovery, that's it — JS TOP appears in your floating menu. Explicit registration:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { jsTopPreset, jsTopModalPreset } from "@buoy-gg/js-top";

<FloatingDevTools apps={[jsTopModalPreset, jsTopPreset]} />
```

## How it works

You can't sample a blocked JS thread from JS — so JS Top doesn't sample. It wraps every entry point work can take onto the thread (`setTimeout` / `setInterval` / `setImmediate` / `requestAnimationFrame` / `queueMicrotask` / Promise reactions / legacy-bridge call-ins) and deterministically times each callback, attributing cost to **where the callback was scheduled from** — with lazy stack capture so hot origins get named `setInterval ← startPolling` without per-call overhead.

On the New Architecture, touch/React-commit work enters through paths pure JS can't wrap; that time shows as *unattributed* (the in-tool banner explains). Timers, rAF, microtasks and Promise chains are always fully attributed.

## Buoy Desktop + AI

- The desktop dashboard mirrors the device's task table live, and adds a dev-only **per-function Hermes sampler** (attaches to Metro's inspector).
- Via the Buoy MCP server, your AI agent can call `get_js_thread_top` — "why is JS FPS low?" becomes one tool call.

## License

Part of [React Buoy](https://buoy.gg). See buoy.gg for licensing.
