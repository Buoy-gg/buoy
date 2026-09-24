# @buoy-gg/route-events

[![npm version](https://img.shields.io/npm/v/@buoy-gg/route-events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/route-events) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/route-events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/route-events)

Inspect observed navigation events, browse supported routes, and navigate using the parameters your app requires.

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

![Route Inspector demo](https://github.com/user-attachments/assets/90e55dc7-f8ab-423a-9770-84b9ff9c8446)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/route-events
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
// app/_layout.tsx (Expo Router)
import { Slot } from "expo-router";
import { FloatingDevTools } from "@buoy-gg/core";

export default function RootLayout() {
  return (
    <>
      <Slot />
      <FloatingDevTools />
    </>
  );
}
```

After account setup, open Routes and navigate between two known screens. Check their paths and parameters. Keep your existing navigator and providers; supported data differs between Expo Router and React Navigation. The event duration is time since the previous navigation, not screen render time.

## What you get

- **Sitemap** — browse discovered routes, searchable, with route types (static, dynamic, layout) labeled
- **Navigate** — select a supported route and supply its required parameters. App navigation guards still apply
- **Live navigation stack** — watch the stack push, pop, and replace as you move through the app
- **Navigation event timeline** — every navigation logged with path, params, timestamp, and time since the previous one
- **Filtering & search** — filter events by pathname pattern, search across routes
- **Tracker integration** — the core menu mounts the package’s route tracker
- **Hooks for custom UIs** — `useRouteEvents`, `useRouteSitemap`, and `useNavigationStack` if you want the data without the modal

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/routes) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Browser History API and hash changes feed the shared route history. Register known paths and the framework router adapter for route selection and navigation. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
