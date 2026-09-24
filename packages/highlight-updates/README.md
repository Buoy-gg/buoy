# @buoy-gg/highlight-updates

[![npm version](https://img.shields.io/npm/v/@buoy-gg/highlight-updates?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/highlight-updates) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/highlight-updates?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/highlight-updates)

Highlight observed React component updates in development builds and inspect the available render details.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/highlight-updates
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

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools />
    </>
  );
}
```

That's it — the Render Highlighter appears in the floating menu. It's a standalone implementation: no React DevTools connection, no Chrome tab, no Flipper required.

Prefer to drive it yourself?

```tsx
import { HighlightUpdatesController } from "@buoy-gg/highlight-updates";

HighlightUpdatesController.toggle();
```

## Check the integration

In a development build, open the tool and update a visible component’s state. Confirm that an update is highlighted. Capture depends on React’s development hooks; Pro does not make those hooks available in a release build.

## What you get

- **Render overlays with the cause** — observed updates flash a bounding box with the available mount, state, props, or parent-update classification.
- **Hook value tracking** — when state caused the render, see the before → after values of the exact `useState` or `useReducer` that changed.
- **Overlay mode** — lightweight real-time highlights while you interact with the app; perfect for spotting unnecessary re-renders.
- **Modal mode** — full inspector with render history, filtering, and detailed per-render cause breakdowns for deep debugging sessions.
- **Powers Bench's render capture** — with [`@buoy-gg/perf-monitor`](https://www.npmjs.com/package/@buoy-gg/perf-monitor) installed, benchmark recordings capture per-component render counts and durations.
- **Filters out the framework noise** — you see your components, not `View`/`Text` internals.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/highlight-updates) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Import @buoy-gg/core/web/register before React DOM to capture roots and renders. The shared inspector measures DOM elements. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
