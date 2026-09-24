# @buoy-gg/debug-borders

[![npm version](https://img.shields.io/npm/v/@buoy-gg/debug-borders?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/debug-borders) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/debug-borders?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/debug-borders)

Show layout borders and component labels over your React Native app. Tap a label to inspect the component’s available properties.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

![Debug Borders demo](https://github.com/user-attachments/assets/945fdb5d-2546-442d-98e7-ef73231abbba)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/debug-borders
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

Tap the BORDERS icon in the floating menu to cycle Off → Borders → Labels.

Want your own naming or colors? Build the tool with `createDebugBordersTool({ name: "LAYOUT", bordersColor: "#ec4899", ... })` and pass it to `FloatingDevTools` via the `apps` array.

## Check the integration

Enable borders on a known screen, then tap a component label and inspect its properties. Border drawing and label interaction differ: the labels are intentionally tappable.

## What you get

- **Depth-colored borders** — rainbow borders on every component, colored by depth in the tree, fully touch-through so they never block interaction.
- **Labels mode** — filters down to components with a `testID` or `accessibilityLabel`: green = testID, pink = accessibilityLabel. Labels sit above their boxes and stack automatically when they overlap.
- **Tap to inspect** — tap any label for the component's `testID`, `accessibilityLabel`, `nativeID`, key, accessibility props, full computed styles, and exact position and size.
- **Answers real questions on-device** — "what testID does QA need for this button?" or "which component owns this padding?" without leaving the phone.
- **Stays out of the way** — borders auto-hide on inactive screens in stack navigators and whenever a DevTools modal is open, and they track layout changes automatically.
- **Works everywhere** — Expo and React Native CLI, Fabric (New Architecture) and Paper.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/debug-borders) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Import @buoy-gg/core/web/register before React DOM. The shared overlay measures DOM elements and draws their borders and labels. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
