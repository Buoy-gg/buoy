# @buoy-gg/redux

[![npm version](https://img.shields.io/npm/v/@buoy-gg/redux?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/redux) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/redux?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/redux)

**Redux DevTools on the device — action log, state diffs, and time travel with zero store changes.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/redux
```

## Quick start

Zero config. Your existing store works as-is — no middleware, no wrapper. Buoy hooks the store at creation via the official Redux DevTools integration point (which Redux Toolkit enables by default), so actions are captured from your app's very first dispatch — including everything dispatched inside thunks and RTK Query.

```tsx
import { configureStore } from '@reduxjs/toolkit';
import { Provider } from 'react-redux';
import { FloatingDevTools } from '@buoy-gg/core';

const store = configureStore({ reducer: rootReducer }); // unchanged

export default function App() {
  return (
    <Provider store={store}>
      <YourApp />
      <FloatingDevTools />
    </Provider>
  );
}
```

### How capture works (and how to guarantee full capture)

Buoy binds to your store the earliest way available, in this order:

1. **Store-creation hook (default, full capture)** — importing `@buoy-gg/redux` claims the Redux DevTools global that RTK checks by default. If the package loads before your store module, Buoy is inside the store from creation: every action (thunk-internal, RTK Query) is captured and time travel is real. To **guarantee** this ordering, make it the first import of your app entry:

   ```tsx
   // index.js — first line
   import '@buoy-gg/redux';
   ```

2. **Middleware (explicit full capture)** — one line if you prefer being explicit:

   ```tsx
   middleware: (getDefault) => getDefault().concat(buoyReduxMiddleware),
   ```

3. **Startup fallback** — if your store was created before Buoy loaded, `FloatingDevTools` binds it automatically at app mount. Top-level dispatches are captured from startup; actions dispatched *inside* thunks/middleware aren't visible to this mode (the tool tells you when it's in it).

### Optional: full time travel

Time travel works out of the box on path 1. On the middleware path, wrap your reducer:

```tsx
import { withBuoyDevTools } from '@buoy-gg/redux';

const store = configureStore({
  reducer: withBuoyDevTools(rootReducer),
});
```

## What you get

- **Every action, captured** — type, payload, timestamp, and a diff summary (`+added -removed ~modified`) per dispatch
- **Frame-budget warnings** — actions taking longer than 16ms are flagged so slow reducers can't hide
- **RTK async thunk linking** — pending → fulfilled/rejected automatically connected by Request ID, with a visual timeline and per-request durations
- **Time travel** — jump to the state after any action, or replay an action to re-test your reducers
- **Three-tab detail view** — full action payload, full state tree, and a side-by-side diff (tree or split view)
- **Search & filter** — find actions by type, or show only actions that actually changed state
- **Export & copy** — dump action history as JSON for bug reports or test fixtures
- **Recording toggle** — pause capture when you need to focus, resume when ready

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/redux) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
