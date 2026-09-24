# @buoy-gg/redux

[![npm version](https://img.shields.io/npm/v/@buoy-gg/redux?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/redux) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/redux?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/redux)

Inspect your connected Redux store, review captured actions and state changes, and dispatch actions from your device.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/redux
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

Load the Buoy Redux integration before creating your store and keep Redux DevTools integration enabled. The example uses your existing `rootReducer` and app component; do not create a second store for Buoy.

```tsx
import '@buoy-gg/redux'; // Load before the module that creates your store.
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

### Capture modes

Buoy binds to your store the earliest way available, in this order:

1. **Store-creation hook (default, full capture)** — importing `@buoy-gg/redux` claims the Redux DevTools global that RTK checks by default. If the package loads before your store module, Buoy is inside the store from creation: every action (thunk-internal, RTK Query) is captured and time travel is real. To establish this ordering, make it the first import of your app entry:

   ```tsx
   // index.js — first line
   import '@buoy-gg/redux';
   ```

2. **Middleware (explicit full capture)** — one line if you prefer being explicit:

   ```tsx
   import { buoyReduxMiddleware } from "@buoy-gg/redux";

   const store = configureStore({
     reducer: rootReducer,
     middleware: (getDefault) => getDefault().concat(buoyReduxMiddleware),
   });
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

## Check the integration

Dispatch an existing app action and confirm that its action and state change appear. Keep the Redux devtools connection enabled and initialize Buoy’s Redux integration before creating the store. Jumping to a retained state does not reverse backend requests or other side effects.

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

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/redux) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Use the app’s existing Redux provider. The browser host mounts capture and exposes the shared state and action panels. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
