# @buoy-gg/react-query

[![npm version](https://img.shields.io/npm/v/@buoy-gg/react-query?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/react-query) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/react-query?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/react-query)

Inspect the TanStack Query cache used by your app. Refetch or invalidate a query and simulate states while checking the screen that consumes it.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

![React Query DevTools demo](https://github.com/user-attachments/assets/258e892d-3eaf-41f8-9fae-d7d2dcd6c39d)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/react-query
```

Requires `@tanstack/react-query` as a peer — you already have it if you use React Query.

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

## Check the integration

Open a known query, refetch it, and check the screen that uses it. Restore any simulated state after testing. The offline toggle changes TanStack Query’s online state; query behavior still depends on its `networkMode` and configuration.

## What you get

- **Cache inspection** — browse every query, drill into cached data with an interactive JSON tree, and edit values in place
- **Refetch & invalidate** — trigger refetches or invalidate queries straight from the UI
- **Simulate loading & error states** — test spinners and error boundaries without waiting on real network conditions
- **One-tap offline toggle** — the WiFi switch drives React Query's `onlineManager` to pause all queries, perfect for offline-first testing
- **Live mutation tracking** — status, variables, response or error, and timing for every mutation as it runs
- **Query states at a glance** — fresh, stale, fetching, paused, and inactive across your whole cache
- **Filter & search** — narrow by status or search by query key

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/react-query) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Use the app’s existing QueryClientProvider. The browser host mounts the shared tracker and cache adapter. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
