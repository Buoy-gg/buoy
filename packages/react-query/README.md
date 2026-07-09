# @buoy-gg/react-query

[![npm version](https://img.shields.io/npm/v/@buoy-gg/react-query?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/react-query) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/react-query?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/react-query)

**Full TanStack Query devtools on the device — inspect the cache, simulate loading and error states, and flip your app offline with one tap.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

![React Query DevTools demo](https://github.com/user-attachments/assets/258e892d-3eaf-41f8-9fae-d7d2dcd6c39d)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/react-query
```

Requires `@tanstack/react-query` as a peer — you already have it if you use React Query.

## Quick start

Render `FloatingDevTools` inside your `QueryClientProvider`. The tool picks up your `QueryClient` from context and connects automatically — no extra call needed.

```tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { FloatingDevTools } from '@buoy-gg/core';

const queryClient = new QueryClient();

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <YourApp />
      <FloatingDevTools />
    </QueryClientProvider>
  );
}
```

That's it. The React Query tool (and a WiFi toggle) appear in the floating menu.

## What you get

- **Cache inspection** — browse every query, drill into cached data with an interactive JSON tree, and edit values in place
- **Refetch & invalidate** — trigger refetches or invalidate queries straight from the UI
- **Simulate loading & error states** — test spinners and error boundaries without waiting on real network conditions
- **One-tap offline toggle** — the WiFi switch drives React Query's `onlineManager` to pause all queries, perfect for offline-first testing
- **Live mutation tracking** — status, variables, response or error, and timing for every mutation as it runs
- **Query states at a glance** — fresh, stale, fetching, paused, and inactive across your whole cache
- **Filter & search** — narrow by status or search by query key

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/react-query) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
