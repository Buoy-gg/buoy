# @buoy-gg/highlight-updates

[![npm version](https://img.shields.io/npm/v/@buoy-gg/highlight-updates?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/highlight-updates) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/highlight-updates?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/highlight-updates)

**See exactly WHY your React Native components re-render — every render gets an overlay tagged with its cause: mount, state, props, or parent.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/highlight-updates
```

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

That's it — the Render Highlighter appears in the floating menu. It's a standalone implementation: no React DevTools connection, no Chrome tab, no Flipper required.

Prefer to drive it yourself?

```tsx
import { HighlightUpdatesController } from "@buoy-gg/highlight-updates";

HighlightUpdatesController.toggle();
```

## What you get

- **Render overlays with the cause** — every render flashes a bounding box tagged mount / state change / prop change / parent re-render. No more guessing why a component updated.
- **Hook value tracking** — when state caused the render, see the before → after values of the exact `useState` or `useReducer` that changed.
- **Overlay mode** — lightweight real-time highlights while you interact with the app; perfect for spotting unnecessary re-renders.
- **Modal mode** — full inspector with render history, filtering, and detailed per-render cause breakdowns for deep debugging sessions.
- **Powers Bench's render capture** — with [`@buoy-gg/perf-monitor`](https://www.npmjs.com/package/@buoy-gg/perf-monitor) installed, benchmark recordings capture per-component render counts and durations.
- **Filters out the framework noise** — you see your components, not `View`/`Text` internals.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/highlight-updates) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
