# @buoy-gg/route-events

[![npm version](https://img.shields.io/npm/v/@buoy-gg/route-events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/route-events) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/route-events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/route-events)

**See every route in your app, watch navigation happen in real time, and jump to any screen from a full sitemap.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

![Route Inspector demo](https://github.com/user-attachments/assets/90e55dc7-f8ab-423a-9770-84b9ff9c8446)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/route-events
```

## Quick start

```tsx
// app/_layout.tsx (Expo Router)
import { Slot } from "expo-router";
import { FloatingDevTools } from "@buoy-gg/core";

export default function RootLayout() {
  return (
    <>
      <Slot />
      <FloatingDevTools />
    </>
  );
}
```

That's it. `FloatingDevTools` detects the package, mounts the route tracker for you, and the ROUTES tool shows up in the menu. It auto-detects whether you're on Expo Router or React Navigation and adapts.

## What you get

- **Full sitemap** — browse every route in your app, searchable, with route types (static, dynamic, layout) labeled
- **Jump to any screen** — tap a route in the sitemap and navigate straight to it, params and all
- **Live navigation stack** — watch the stack push, pop, and replace as you move through the app
- **Navigation event timeline** — every navigation logged with path, params, timestamp, and time since the previous one
- **Filtering & search** — filter events by pathname pattern, search across routes
- **Zero setup** — no `<RouteTracker />` to place, no config; the core menu wires it automatically
- **Hooks for custom UIs** — `useRouteEvents`, `useRouteSitemap`, and `useNavigationStack` if you want the data without the modal

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/routes) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
