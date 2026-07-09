# @buoy-gg/zustand

[![npm version](https://img.shields.io/npm/v/@buoy-gg/zustand?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/zustand) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/zustand?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/zustand)

**Zustand devtools on the device — state diffs, jump-to-state, and one-tap store reset with no middleware.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/zustand
```

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

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/zustand) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
