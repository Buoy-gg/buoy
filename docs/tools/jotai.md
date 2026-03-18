---
title: Jotai DevTools
id: tools-jotai
---

<!-- ::platform-badge platform="both" -->

Full Jotai atom inspection for React Native. Monitor atom state changes, explore value diffs, and browse live atom values in real-time — directly on your device.

<!-- ::jotai-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/jotai" yarn="yarn add @buoy-gg/jotai" pnpm="pnpm add @buoy-gg/jotai" bun="bun add @buoy-gg/jotai" -->

---

## Setup

Call `watchAtoms` once at module scope — pass your Jotai store and a named map of your atoms. No wrappers, no middleware, no modifications to existing atoms.

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

That's it. Registered atoms automatically appear in the Jotai tool inside your FloatingDevTools menu.

> **Zero config required** — Your existing atoms work as-is. No wrappers, no `atomWithDevTools`, nothing to change.

---

## Atoms Tab

Browse all registered atoms and their **live current value**:

- **Atom Name** — Color-coded for easy identification across both tabs
- **Value Type** — `number`, `boolean`, `object`, `array · N`, `null`, etc. at a glance
- **Live Value** — Tap any atom to expand and see the full value tree, updated in real-time
- **Change Count** — How many times this atom has changed this session
- **View History** — Jump straight to the filtered event history for a single atom

---

## Events Tab

Every atom change is captured with rich metadata:

- **Atom Name** — Which atom changed, color-coded for quick identification
- **Value Transition** — `prev → next` at a glance (e.g. `0 → 5`, `null → {name, email}`, `[2 items] → [3 items]`)
- **Category Badge** — `INIT` (initial registration) or `WRITE` (subsequent update)
- **No Change** — Flags writes that fired but didn't actually change the value
- **Timestamp** — When the change occurred with relative time

---

## Detail View

Tap any event to see three detailed tabs:

### Change Tab
Atom name, timestamp, category badge, and a diff summary showing which object keys changed.

### Value Tab
The full atom value after this change — collapsible JSON tree for objects, raw value for primitives.

### Diff Tab
Side-by-side comparison of before and after — additions (green), removals (red), modifications (yellow). Choose between tree view or split view.

---

## Using a Custom Store

If your app uses a `<Provider>` with a custom store, pass it directly:

```tsx
import { createStore, Provider } from 'jotai';
import { watchAtoms } from '@buoy-gg/jotai';
import { countAtom, authAtom } from './atoms';

const myStore = createStore();

watchAtoms(myStore, { countAtom, authAtom });

export function App() {
  return (
    <Provider store={myStore}>
      <YourApp />
    </Provider>
  );
}
```

---

## Features

### Atom Color Coding
Each atom gets a consistent color across the Atoms tab, Events tab, and detail views. Colors are assigned automatically based on atom name and persist for the session.

### Value Transition at a Glance
Every event row shows `prev → next` so you immediately know what changed — no need to tap in for simple updates.

### Atom History
Tap "view history" on any atom in the Atoms tab to see all events scoped to just that atom — useful for tracking a specific piece of state through a flow.

### Search & Filter
Find events by atom name or value content. Add filter patterns to hide noisy atoms from both the Atoms and Events tabs simultaneously.

### Copy to Clipboard
Export the full atoms snapshot or event history as JSON for bug reports, test fixtures, or sharing with teammates (Pro).

### Recording Toggle
Pause atom capture when you need to focus, resume when ready.

---

## What's Next

- [Zustand DevTools](./zustand) — Zustand store monitor with state diffing and jump-to-state
- [Redux DevTools](./redux) — Redux action monitor with state diffing and time-travel
- [React Query DevTools](./react-query) — TanStack Query inspection
- [Network Monitor](./network) — See every API call your app makes
