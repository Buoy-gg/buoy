# @buoy-gg/jotai

[![npm version](https://img.shields.io/npm/v/@buoy-gg/jotai?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/jotai) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/jotai?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/jotai)

Inspect registered Jotai atoms and their captured changes, with live values and per-atom history.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/jotai
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
import { createStore, Provider } from "jotai";

const myStore = createStore();
watchAtoms(myStore, { countAtom, authAtom });

// Use this same store in the provider around your app and Buoy menu.
<Provider store={myStore}>{/* Existing app and FloatingDevTools */}</Provider>;
```

Registered atoms appear in the Jotai tool inside the floating menu.

## Check the integration

Change a registered atom through your app and inspect its history. Use the same store as your Jotai provider. Whether an atom can be edited depends on whether it is writable; a derived atom can also be writable.

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

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/jotai) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Register the app’s atoms and store with watchAtoms. The shared panel and snapshot provider use those registrations. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
