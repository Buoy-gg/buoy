---
title: Redux DevTools
id: tools-redux
---

<!-- ::platform-badge platform="both" -->

Full Redux Toolkit inspection for React Native. Monitor actions, explore state changes, view diffs, and debug your Redux store in real-time.

## Installation

<!-- ::PM npm="npm install @buoy-gg/redux" yarn="yarn add @buoy-gg/redux" pnpm="pnpm add @buoy-gg/redux" bun="bun add @buoy-gg/redux" -->

## Setup

Add the middleware to your Redux store:

```tsx
import { configureStore } from '@reduxjs/toolkit';
import { buoyReduxMiddleware } from '@buoy-gg/redux';

const store = configureStore({
  reducer: rootReducer,
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(buoyReduxMiddleware),
});
```

That's it. The Redux DevTools auto-detects your store and appears in your FloatingDevTools menu.

---

## Action List

Every dispatched action is captured with rich metadata:

- **Action Type** — Full action name with slice detection
- **Category Badges** — Automatic detection of pending, fulfilled, rejected states
- **Duration** — How long the action took (with slow action warnings)
- **Diff Summary** — Quick overview of state changes (+added -removed ~modified)
- **Timestamp** — When the action was dispatched

---

## Detail View

Tap any action to see three detailed tabs:

### Action Tab
View the complete action payload, meta information, and error details for failed actions.

### State Tab
Explore the full state tree after this action with an interactive JSON viewer.

### Diff Tab
Side-by-side comparison showing exactly what changed in your state — additions, removals, and modifications highlighted.

---

## RTK Async Thunks

Full support for Redux Toolkit async thunks:

- **Request ID Tracking** — Link related pending/fulfilled/rejected actions
- **Status Detection** — Visual indicators for async operation states
- **Duration Calculation** — Total time from pending to completion
- **Original Arguments** — See what was passed to the thunk

---

## Features

- **Search & Filter** — Find actions by type or filter by category
- **Slow Action Detection** — Actions taking >16ms are flagged
- **State Size Tracking** — Monitor your state tree size
- **Copy to Clipboard** — Export action history for debugging

---

## What's Next

- [React Query DevTools](./react-query) — TanStack Query inspection
- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV
