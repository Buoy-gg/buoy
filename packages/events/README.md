# @buoy-gg/events

[![npm version](https://img.shields.io/npm/v/@buoy-gg/events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/events) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/events)

**One chronological timeline across every tool — network, storage, Redux, React Query, routes — so you can watch a whole user flow unfold in order.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/events
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

That's it. The EVENTS tool appears in the menu and automatically captures from every Buoy tool you have installed — no per-source setup.

## What you get

- **One timeline, every source** — network requests, AsyncStorage/MMKV writes, Redux actions, React Query queries and mutations, Zustand, Jotai, and navigation events, interleaved chronologically
- **Auto-detection** — if a Buoy tool is installed, its events just show up
- **Correlation badges** — related events are linked: a React Query fetch start → success pair shows as "1/2" so you can trace the full lifecycle
- **LLM export presets** — copy the timeline as Markdown tuned for AI agents, a Bug Report with timestamps and error details, machine-readable JSON, or Errors Only
- **Smart formatting** — nested JSON parsed, verbose fields stripped, and Redux events show only the state that actually changed
- **Filter and search** — narrow by source, status, or text before you export
- **`useUnifiedEvents` hook** — consume the merged event stream in your own components

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/events) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
