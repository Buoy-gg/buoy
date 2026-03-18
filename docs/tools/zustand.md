---
title: Zustand DevTools
id: tools-zustand
---

<!-- ::platform-badge platform="both" -->

Full Zustand store inspection for React Native. Monitor state changes, explore diffs, jump to any previous state, and reset stores in real-time — directly on your device.

<!-- ::zustand-demo -->

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

## Features

### Search & Filter
Find state changes by store name or changed keys, or filter to show only updates that modified state.

### Jump to State
Restore any store to a previously captured state — instantly see how your app looked at any point in history.

### Reset Store
Reset any store back to its initial state in one tap — no need to restart the app.

### Store Color Coding
Each store gets a consistent color across the UI for easy visual tracking when monitoring multiple stores.

### Persist Awareness
Auto-detects stores using Zustand's `persist` middleware and tags changes accordingly.

### Copy to Clipboard
Export state data or diffs for debugging, bug reports, or test fixtures (Pro).

### Recording Toggle
Pause state capture when you need to focus, resume when ready.

---

## What's Next

- [Redux DevTools](./redux) — Redux action monitor with state diffing and time-travel
- [React Query DevTools](./react-query) — TanStack Query inspection
- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV
