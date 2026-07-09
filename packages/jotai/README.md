# @buoy-gg/jotai

[![npm version](https://img.shields.io/npm/v/@buoy-gg/jotai?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/jotai) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/jotai?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/jotai)

**Jotai devtools on the device — every atom write shows `prev → next`, with per-atom history and live values.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/jotai
```

## Quick start

One `watchAtoms(store, atoms)` call at module scope — pass your Jotai store and a named map of atoms. No wrappers, no `atomWithDevTools`, no changes to existing atoms; observation is subscribe-only.

```tsx
import { getDefaultStore } from 'jotai';
import { watchAtoms } from '@buoy-gg/jotai';
import { countAtom } from './atoms/count';
import { authAtom } from './atoms/auth';
import { cartAtom } from './atoms/cart';

watchAtoms(getDefaultStore(), {
  countAtom,
  authAtom,
  cartAtom,
});
```

Using a `<Provider>` with a custom store? Pass it instead:

```tsx
const myStore = createStore();
watchAtoms(myStore, { countAtom, authAtom });
```

Registered atoms appear in the Jotai tool inside the floating menu.

## What you get

- **`prev → next` on every event** — see `0 → 5` or `null → {name, email}` at a glance, no tapping in for simple updates
- **Live Atoms tab** — every registered atom with its current value, value type, and change count, updated in real time
- **Per-atom history** — jump from any atom straight to its filtered event timeline to trace one piece of state through a flow
- **No-change flags** — writes that fired but didn't actually change the value are called out
- **Three-tab detail view** — change metadata, full value tree, and a side-by-side before/after diff
- **Atom color coding** — each atom keeps a consistent color across tabs and detail views
- **Search & filter** — find events by atom name or value content; hide noisy atoms everywhere at once
- **Recording toggle** — pause atom capture when you need to focus, resume when ready

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/jotai) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
