---
title: Redux DevTools
id: tools-redux
description: "Redux DevTools for React Native — monitor dispatched actions, inspect state changes, and time-travel debug your store live on the device, no Flipper needed."
---

<!-- ::platform-badge platform="both" -->

Full Redux Toolkit inspection for React Native. Monitor actions, explore state changes, time-travel debug, and inspect your Redux store in real-time — directly on your device.

<!-- ::redux-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/redux" yarn="yarn add @buoy-gg/redux" pnpm="pnpm add @buoy-gg/redux" bun="bun add @buoy-gg/redux" -->

That's it. The Redux DevTools auto-detects your store and appears in your FloatingDevTools menu.

> **Zero config required** — Just install the package. Your existing Redux store works as-is with no middleware or wrapper needed.

---

## BUOY vs Chrome Redux DevTools

BUOY brings the power of Redux DevTools to mobile — with features designed for React Native workflows.

| Feature | BUOY | Chrome Extension |
|:--------|:----:|:----------------:|
| **On-device debugging** | ✅ | ❌ |
| **Works in production** | ✅ | ❌ |
| **QA/support can use it** | ✅ | ❌ |
| **No desktop app required** | ✅ | ❌ |
| **Zero configuration** | ✅ | ❌ |
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

> **Note:** For full time-travel support (jumping to past states), add the optional reducer wrapper. See [Advanced Configuration](#advanced-configuration) below.

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

## Advanced Configuration

For most apps, zero-config is all you need. But if you want more control:

### Enable Full Time-Travel

To enable jumping to past states (not just viewing them), wrap your reducer:

```tsx
import { configureStore } from '@reduxjs/toolkit';
import { withBuoyDevTools } from '@buoy-gg/redux';

const store = configureStore({
  reducer: withBuoyDevTools(rootReducer),
});
```

### Custom Middleware Options

For fine-grained control over what gets captured:

```tsx
import { createBuoyReduxMiddleware, withBuoyDevTools } from '@buoy-gg/redux';

const customMiddleware = createBuoyReduxMiddleware({
  maxActions: 500,           // History size (default: 200)
  ignoreActions: [           // Actions to skip
    'persist/PERSIST',
    'persist/REHYDRATE',
  ],
});

const store = configureStore({
  reducer: withBuoyDevTools(rootReducer),
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(customMiddleware),
});
```

> **When to use manual middleware:** Only if you need to ignore specific actions or increase history size. The auto-instrumentation handles everything else.

> **No conflicts:** If you configure middleware manually, the auto-instrumentation automatically detects this and defers to your configuration. You'll never get duplicate action entries.

---

## API Reference

### Exports

```tsx
import {
  // Auto-instrumentation (used internally, rarely needed)
  instrumentStore,
  isStoreInstrumented,

  // Manual middleware (optional, for advanced config)
  buoyReduxMiddleware,
  createBuoyReduxMiddleware,

  // Time-travel (optional)
  withBuoyDevTools,
  jumpToState,
  replayAction,

  // Hooks
  useReduxActions,
  useAutoInstrumentRedux,

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
