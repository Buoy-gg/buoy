# @buoy-gg/network

[![npm version](https://img.shields.io/npm/v/@buoy-gg/network?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/network) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/network?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/network)

**See every HTTP request your React Native app makes — URL, headers, payloads, timing, and errors — live on the device, no Flipper or proxy required.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

![Network Monitor demo](https://github.com/user-attachments/assets/473ddf83-03cd-4bd1-8dc3-0f66eda9fa8a)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/network
```

## Quick start

Fully automatic. Render the floating menu once — the Network Monitor is auto-discovered as soon as this package is installed:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools />
    </>
  );
}
```

Want a different label or color? Build the tool with `createNetworkTool({ name, colorPreset })` and pass it via `apps={[...]}`.

## What you get

- **Every request, zero config** — intercepts `fetch` and `XMLHttpRequest`, so fetch, axios, GraphQL clients, and gRPC-web all show up automatically.
- **GraphQL gets special treatment** — operation names are extracted from queries, mutations, and subscriptions and shown with variables in arrow notation: `GetUser › 123`. No more guessing which `/graphql` request is which.
- **Full request detail** — request/response headers, payloads, HTTP status, and duration for every call, with status-colored rows.
- **Filter and search** — narrow by status code, HTTP method, or URL pattern; search captured events; pause/resume capture; clear history.
- **Ignore the noise** — a shared ignored-domains/URL filter (`useIgnoredPatterns`) hides endpoints you never want to see, shared with the Events tool.
- **Hooks for custom UIs** — `useNetworkEvents`, `useNetworkEventList`, and `useNetworkBadgeCounts` expose the capture stream with narrow per-consumer subscriptions.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

> 🎯 **Flutter (beta):** Buoy is now a full suite for Flutter — `flutter pub add buoy`. Network, storage, console, env, routes, images, Riverpod, events, and a live perf HUD, all streaming to the same desktop dashboard and MCP server. [Details →](https://buoy.gg/flutter)

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/network) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
