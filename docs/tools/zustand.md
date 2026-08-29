---
title: Zustand DevTools
seoTitle: "Zustand DevTools for React Native — stores, diffs & time travel"
id: tools-zustand
description: "Zustand devtools for React Native — watch store state changes, explore diffs, jump back to any previous state, and reset stores live on your device."
---

<!-- ::platform-badge platform="both" -->

Zustand's appeal is that a store is just a function — which is also why nothing tells you when one changes. There is no action log to read, so a value that goes wrong halfway through a flow leaves you sprinkling `subscribe` calls and rebuilding to see them.

Buoy writes the update stream you do not have: which store changed, which keys, the diff, and whether an update fired without changing anything. It tells rehydration apart from your own writes, and jumps a store back without restarting the app.

Walk a live checkout session: stores update, a payment fails, DIFF names the keys, JUMP rolls checkoutStore back, RESET empties cartStore — then free play.

<!-- ::zustand-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/zustand" yarn="yarn add @buoy-gg/zustand" pnpm="pnpm add @buoy-gg/zustand" bun="bun add @buoy-gg/zustand" -->

---

## Setup

Pass your stores directly to `FloatingDevTools` via the `zustandStores` prop — no separate setup call needed.

```tsx
import { FloatingDevTools } from '@buoy-gg/core';
import { useCounterStore } from './stores/counter';
import { useAuthStore } from './stores/auth';
import { useCartStore } from './stores/cart';

const stores = {
  counterStore: useCounterStore,
  authStore: useAuthStore,
  cartStore: useCartStore,
};

return <FloatingDevTools zustandStores={stores} />;
```

---

## State Change List

Every state update is captured with rich metadata:

- **Store Name** — Which store changed, color-coded for quick identification
- **Category Badge** — `setState`, `replace`, `persist`, or `initial` — instant recognition of update type
- **Changed Keys** — Top-level keys that changed in this update
- **Diff Summary** — Quick overview of additions, removals, and modifications
- **Timestamp** — When the change occurred with relative time
- **Duration** — How long the update took (middleware mode only, flags >16ms)

---

## Detail View

Tap any state change to see three detailed tabs:

### State Tab
Explore the full state tree after this change with a collapsible JSON viewer. Navigate deeply nested state with ease.

### Diff Tab
Side-by-side comparison showing exactly what changed — additions (green), removals (red), and modifications (yellow) clearly highlighted. Choose between tree view or split view.

### Store Tab
Browse the complete current state of the store, including all keys and their current values.

---

## Advanced: `buoyDevTools()` middleware

For precise partial state capture and timing data, wrap individual stores:

```tsx
import { create } from 'zustand';
import { buoyDevTools } from '@buoy-gg/zustand';

const useCounterStore = create(
  buoyDevTools(
    (set) => ({
      count: 0,
      increment: () => set((s) => ({ count: s.count + 1 })),
    }),
    { name: 'counterStore' }
  )
);
```

Works with middleware chaining (persist, immer, etc.):

```tsx
import { persist } from 'zustand/middleware';

const useAuthStore = create(
  buoyDevTools(
    persist(
      (set) => ({ user: null, login: (u) => set({ user: u }) }),
      { name: 'auth-storage' }
    ),
    { name: 'authStore' }
  )
);
```

---

## Performance Monitoring

Catch performance issues before they impact users (middleware mode):

- **Update Timing** — Millisecond-precision duration for every state update
- **Slow Update Detection** — Updates taking >16ms (frame budget) are flagged with warnings
- **State Change Indicators** — Quickly identify updates that actually modified state

---

## Editing a Store

Expand a store's card on the Stores tab and tap **Edit state** — the same full-screen tree editor the Storage tool uses: tap a row to select it, double-tap to type a new value, restructure with the docked actions, then write everything in one save. Works for in-memory and persisted stores alike (a persisted store's `persist` middleware writes the disk copy itself on save — no storage detour).

Saves are **merge-only**. However deep the edit, it is written as `setState({ changedTopLevelKeys }, false)`, which by construction cannot delete a top-level key and cannot touch the action functions your store keeps in state. Two things follow from that:

- **A top-level key can be changed but not removed or renamed.** A zustand merge can't delete, so Remove is disabled on top-level rows, and a raw-JSON edit that drops a key is refused at save with the key named. (For a persisted store, delete the key from its saved copy in the Storage tool and tap "re-read saved value" instead.)
- **Your actions survive, and untouched keys keep the app's value.** The save carries only what you changed — a key the app wrote while you were editing, and you didn't touch, stays the app's.

A store whose state isn't a plain object, or that keeps a function nested inside a data key, says so on the card instead of offering the button. Each save lands in the change log as one ordinary recorded change.

## Features

### Search & Filter
Find state changes by store name or changed keys, or filter to show only updates that modified state.

### Jump to State
Restore any store to a previously captured state — instantly see how your app looked at any point in history.

### Reset Store
Reset any store back to its initial state in one tap — no need to restart the app.

### Rehydration, Told Apart
A store behind zustand's `persist` middleware changes on its own at startup,
when its value comes back off disk. Buoy labels that update **HYDRATE** rather
than drawing it as an ordinary `setState` — so the one change none of your code
made is the one you can pick out of the log, and every later write to the same
store is still marked **PERSISTED**.

### Store Color Coding
Each store gets a consistent color across the UI for easy visual tracking when monitoring multiple stores.

### Persist Awareness
Auto-detects stores using Zustand's `persist` middleware and tags changes accordingly.

### Copy to Clipboard
Export state data or diffs for debugging, bug reports, or test fixtures (Pro).

### Recording Toggle
Pause state capture when you need to focus, resume when ready.

---

## What It Can't Do

**Stores are not auto-discovered.** A Zustand store is a plain function with no registry to enumerate, so Buoy cannot find yours the way it finds a Redux store. Pass them to `FloatingDevTools` via `zustandStores`, or wrap a store with the `buoyDevTools` middleware. If nothing is registered, the tool says so rather than showing an empty list.

**Action names and timing need the middleware.** The `zustandStores` route watches state and can tell you *what* changed. `buoyDevTools` sits inside the setter, so it also knows *which call* changed it. Without it you get the diff, not the label.

## What's Next

- [Redux DevTools](./redux) — Redux action monitor with state diffing and time-travel
- [React Query DevTools](./react-query) — TanStack Query inspection
- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV

---

## FAQ

### How do I debug Zustand state in React Native?

Install `@buoy-gg/zustand` and register your stores — they appear in the on-device browser with live state, an update stream, and diffs. No Redux DevTools bridge or web debugger needed.

### Can I see which store update caused a bug?

Yes — the event stream timestamps every update with the store name and changed key, and each event has a before/after diff.
