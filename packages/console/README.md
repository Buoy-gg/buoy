# @buoy-gg/console

[![npm version](https://img.shields.io/npm/v/@buoy-gg/console?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/console) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/console?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/console)

**Capture every `console.log`, `warn`, and `error` from your React Native app in a Chrome-DevTools-style panel — filterable, on-device, no cable or Flipper needed.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/console
```

## Quick start

Pass the preset to the floating menu, and mount `ConsoleRoot` once near the app root so capture starts at startup — logs emitted before you open the tool are retained:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { consoleToolPreset, ConsoleRoot } from "@buoy-gg/console";

export default function App() {
  return (
    <>
      {/* your app */}
      <ConsoleRoot />
      <FloatingDevTools apps={[consoleToolPreset]} />
    </>
  );
}
```

That's the whole setup — it patches `console.*` directly, so no logger integration or Babel plugin is required.

## What you get

- **Everything logged, everywhere** — patches `console.*` directly, so logs from your own code *and* your dependencies are captured with no extra setup.
- **Live, color-coded stream** — `log`, `info`, `warn`, and `error` stream in as they happen, color-coded by level.
- **Chrome-DevTools-style panel** — a 1:1 port of the Chrome console view: objects and arrays are formatted and expandable, just like the browser.
- **Filter by level** — focus on just errors and warnings when you're chasing a bug.
- **Search** — filter messages by substring to find the exact log you care about.
- **Preserve log** — an optional setting keeps the buffer across reloads, honored from the very first log.
- **Read production logs** — Buoy runs on-device in any build, so you can read console output from a release build with no cable and no Metro connection.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp) — its `get_console` tool lets an agent read the console tail, filtered by minimum level or message substring.

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/console) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
