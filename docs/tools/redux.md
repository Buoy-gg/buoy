---
title: Redux DevTools
id: tools-redux
---

<!-- ::platform-badge platform="both" -->

Full Redux Toolkit inspection for React Native. Monitor actions, explore state changes, time-travel debug, and inspect your Redux store in real-time — directly on your device.

<!-- ::redux-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/redux" yarn="yarn add @buoy-gg/redux" pnpm="pnpm add @buoy-gg/redux" bun="bun add @buoy-gg/redux" -->

## Setup

Add the middleware and reducer wrapper to your Redux store:

```tsx
import { configureStore, combineReducers } from '@reduxjs/toolkit';
import { buoyReduxMiddleware, withBuoyDevTools } from '@buoy-gg/redux';

const rootReducer = combineReducers({
  // your reducers
});

const store = configureStore({
  reducer: withBuoyDevTools(rootReducer), // Enable time-travel
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(buoyReduxMiddleware),
});
```

That's it. The Redux DevTools auto-detects your store and appears in your FloatingDevTools menu.

---

## BUOY vs Chrome Redux DevTools

BUOY brings the power of Redux DevTools to mobile — with features designed for React Native workflows.

| Feature | BUOY | Chrome Extension |
|:--------|:----:|:----------------:|
| **On-device debugging** | ✅ | ❌ |
| **Works in production** | ✅ | ❌ |
| **QA/support can use it** | ✅ | ❌ |
| **No desktop app required** | ✅ | ❌ |
| Action logging | ✅ | ✅ |
| State inspection | ✅ | ✅ |
| State diff view | ✅ | ✅ |
| Time-travel (Jump to state) | ✅ | ✅ |
| Action replay | ✅ | ✅ |
| Action filtering & search | ✅ | ✅ |
| Performance timing | ✅ | ✅ |
| Async thunk linking | ✅ | ✅ |
| Export history | ✅ | ✅ |
| RTK Query support | ✅ | ✅ |
| Skip/toggle actions | 🔜 | ✅ |
| Dispatch custom actions | 🔜 | ✅ |
| Import state | 🔜 | ✅ |
| Persist across reloads | 🔜 | ✅ |
| Stack traces | 🔜 | ✅ |

> **Why on-device matters:** Debug Redux on real devices, in TestFlight, or in production. No USB cable, no desktop app, no "it works on the simulator" moments.

---

## Action List

Every dispatched action is captured with rich metadata:

- **Action Type** — Full action name with automatic slice detection
- **Category Badges** — Instant recognition of pending, fulfilled, rejected states
- **Duration** — How long the action took (with slow action warnings >16ms)
- **Diff Summary** — Quick overview of state changes (+added -removed ~modified)
- **Timestamp** — When the action was dispatched with relative time

---

## Detail View

Tap any action to see three detailed tabs:

### Action Tab
View the complete action payload, meta information, and error details for failed actions. Interactive JSON tree for exploring nested data.

### State Tab
Explore the full state tree after this action with a collapsible data viewer. Navigate deeply nested state with ease.

### Diff Tab
Side-by-side comparison showing exactly what changed — additions (green), removals (red), and modifications (yellow) clearly highlighted. Choose between tree view or split view.

---

## Time-Travel Debugging

Jump to any point in your app's history:

- **Jump to State** — Instantly restore your app to the state after any action
- **Replay Action** — Re-dispatch any action to test how your reducers respond
- **Async Timeline** — Visual timeline showing the full lifecycle of async operations

```tsx
// Time-travel is enabled automatically when you use withBuoyDevTools
import { withBuoyDevTools } from '@buoy-gg/redux';

const store = configureStore({
  reducer: withBuoyDevTools(rootReducer),
  // ...
});
```

---

## RTK Async Thunks

Full support for Redux Toolkit async thunks with intelligent linking:

- **Request ID Tracking** — Automatically links pending → fulfilled/rejected actions
- **Visual Timeline** — See the full async flow in a connected timeline
- **Concurrent Request Handling** — Color-coded badges distinguish parallel requests (#1, #2, etc.)
- **Duration Calculation** — Total time from pending to completion
- **Original Arguments** — See exactly what was passed to the thunk

---

## Performance Monitoring

Catch performance issues before they impact users:

- **Action Timing** — Millisecond-precision duration for every action
- **Slow Action Detection** — Actions taking >16ms (frame budget) are flagged with warnings
- **Average Duration** — Track performance trends across your session
- **State Change Indicators** — Quickly identify actions that actually modified state

---

## Features

### Search & Filter
Find actions instantly by type, or filter to show only actions that changed state.

### Copy to Clipboard
Export action data or payloads for debugging, bug reports, or test fixtures.

### Recording Toggle
Pause action capture when you need to focus, resume when ready.

### Export History
Download your complete action history as JSON for sharing with teammates or creating test data.

---

## Configuration

Customize the middleware for your needs:

```tsx
import { createBuoyReduxMiddleware } from '@buoy-gg/redux';

const customMiddleware = createBuoyReduxMiddleware({
  maxActions: 500,           // History size (default: 200)
  ignoreActions: [           // Actions to skip
    '@@redux/INIT',
    'persist/PERSIST',
  ],
});

const store = configureStore({
  reducer: withBuoyDevTools(rootReducer),
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(customMiddleware),
});
```

---

## API Reference

### Exports

```tsx
import {
  // Middleware
  buoyReduxMiddleware,        // Default middleware
  createBuoyReduxMiddleware,  // Configurable middleware

  // Time-travel
  withBuoyDevTools,           // Reducer wrapper for time-travel
  jumpToState,                // Programmatic state restoration
  replayAction,               // Programmatic action replay

  // Store access
  reduxActionStore,           // Direct access to action history

  // History adapter (for custom integrations)
  reduxHistoryAdapter,
  createReduxHistoryAdapter,
} from '@buoy-gg/redux';
```

---

## What's Next

- [React Query DevTools](./react-query) — TanStack Query inspection
- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV
