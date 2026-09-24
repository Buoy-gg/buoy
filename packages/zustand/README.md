# @buoy-gg/zustand

[![npm version](https://img.shields.io/npm/v/@buoy-gg/zustand?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/zustand) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/zustand?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/zustand)

Inspect the Zustand stores you register with Buoy. Review changes, edit state, and restore retained snapshots.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/zustand
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

## Quick start

Pass your stores to `FloatingDevTools` via the `zustandStores` prop — a named map of your store hooks. No middleware, no store changes; observation is subscribe-only, so your stores stay untouched.

```tsx
import { FloatingDevTools } from '@buoy-gg/core';
import { useCounterStore } from './stores/counter';
import { useAuthStore } from './stores/auth';
import { useCartStore } from './stores/cart';

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools
        zustandStores={{
          counterStore: useCounterStore,
          authStore: useAuthStore,
          cartStore: useCartStore,
        }}
      />
    </>
  );
}
```

Prefer to wire it outside React? `watchStores({ counterStore: useCounterStore })` from `@buoy-gg/zustand` does the same thing at module scope.

## Check the integration

Change a value in a registered store through your app and find its diff in Buoy. Edits and resets change the real store; test with disposable data. Register the same store instances that your screens use.

## What you get

- **Every state change, captured** — store name, update type (`setState`, `replace`, `persist`, `initial`), changed keys, and a diff summary
- **Side-by-side diffs** — additions, removals, and modifications highlighted, in tree or split view
- **Jump to state** — restore any store to a previously captured state instantly
- **One-tap reset** — send any store back to its initial state without restarting the app
- **Persist awareness** — stores using Zustand's `persist` middleware are auto-detected and tagged
- **Store color coding** — each store keeps a consistent color across the UI for easy tracking
- **Search & filter** — find changes by store name or changed keys, or show only updates that modified state
- **Optional `buoyDevTools()` middleware** — wrap individual stores for partial-state capture and per-update timing (flags updates over the 16ms frame budget)

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/zustand) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Register live stores with watchStores. The shared browser panel edits the same store objects used by the app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
