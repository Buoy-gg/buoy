---
title: Redux DevTools
seoTitle: "Redux DevTools for React Native — actions, state & time travel"
id: tools-redux
description: "Redux DevTools for React Native — monitor dispatched actions, inspect state changes, and time-travel debug your store live on the device, no Flipper needed."
---

<!-- ::platform-badge platform="both" -->

Inspect captured Redux actions, their payloads, state, and diffs. Import Buoy before store creation for enhancer-based capture, or use the explicit middleware setup below. State jumps need the enhancer or reducer wrapper.

<!-- ::redux-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/redux" yarn="yarn add @buoy-gg/redux" pnpm="pnpm add @buoy-gg/redux" bun="bun add @buoy-gg/redux" -->

Complete core setup and ensure the package loads before your store. Dispatch a test action, verify its payload and state diff, then check whether Jump is enabled.

> Capture through the Redux DevTools integration point requires that integration to be enabled and Buoy to load before store creation. If either condition is missing, use the explicit middleware path below.

> **Guarantee full capture** — the store-creation hook needs `@buoy-gg/redux` to load before your store module. That's usually automatic; to make it a guarantee, put `import '@buoy-gg/redux';` as the **first import of your app entry**. If Buoy loads too late, it still binds your store automatically at app mount (top-level dispatches only — the tool tells you when it's in that mode).

---

## BUOY vs Chrome Redux DevTools

Buoy provides an in-app action list, state inspection, diffs, and supported state jumps. A browser extension is a separate debugging surface; check its documentation for its current capabilities.

Production use requires Pro and deliberate app access controls. Available capture and jump behavior depends on your store integration, as described above.

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

> **Note:** Jumping to a past state needs a reducer that can serve it, which is a separate piece of wiring from action capture — middleware sits above your reducer and cannot replace what it returns. You get it automatically when `@buoy-gg/redux` is imported before your store module (Buoy becomes the store enhancer); otherwise add the reducer wrapper from [Advanced Configuration](#advanced-configuration). **The JUMP button tells you which you have**: it is disabled and labelled "Time travel not wired" when the store cannot serve a jump, rather than doing nothing when pressed.

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

Use explicit configuration when you need middleware options or your store was created before Buoy loaded.

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

> **When to use manual middleware:** If you need to ignore specific actions, increase history size, or you simply prefer explicit wiring — the middleware path is also a guaranteed-full-capture alternative to the import-order note above. Everything (action log, desktop sync, MCP `get_redux_state`/`redux_dispatch`) works the same on either path.

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

## What It Can't Do

**JUMP only reaches the 25 most recent actions.** Every retained action pins its own copy of the state tree, and on an app that replaces large slices wholesale — a store switch, a rehydration — a few dozen of those are enough to exhaust memory. Older actions keep their row, their diff summary and their payload; they just no longer have a tree to restore, so JUMP is disabled on them.

**It reads the store, it doesn't replay it.** Jumping sets state directly. It does not re-run your reducers, re-fire thunks, or reissue the network calls an action originally triggered — so a jump puts the *data* back, not the side effects.

## What's Next

- [React Query DevTools](./react-query) — TanStack Query inspection
- [Network Monitor](./network) — Inspect supported HTTP requests
- [Storage Explorer](./storage) — Browse and edit AsyncStorage & MMKV

---

## FAQ

### How do I use Redux DevTools in React Native without Flipper?

Install `@buoy-gg/redux` — the action stream, state diffs, and time-travel controls run inside the app on the device. Flipper is not required.

### Does time travel work on the device?

Yes — JUMP restores the store to the state after any recorded action, and REPLAY re-dispatches an action, directly from the in-app panel.

REPLAY works on every setup. JUMP needs a reducer that handles the jump, which you get either by importing `@buoy-gg/redux` before your store module (Buoy becomes the store enhancer) or by wrapping your root reducer with `withBuoyDevTools`. If neither applies, the JUMP button is disabled and says so — it never silently does nothing.

JUMP is also disabled on older actions whose raw state has been released. Buoy keeps the before/after state trees of the 25 most recent actions only: every retained action pins its own copy of the tree, and on an app that replaces large slices wholesale (a store switch, a rehydration) a few dozen of those are enough to exhaust memory. Older actions keep their row, their diff summary and their payload — just not a tree to restore.

## Web support (unreleased)

Use the app’s existing Redux provider. The browser host mounts capture and exposes the shared state and action panels. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
