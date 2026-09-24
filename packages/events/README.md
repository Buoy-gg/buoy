# @buoy-gg/events

[![npm version](https://img.shields.io/npm/v/@buoy-gg/events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/events) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/events?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/events)

Review events from configured Buoy tools in one timeline. Filter by source, inspect an event, and export a selection for debugging.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/events
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

That's it. The EVENTS tool appears in the menu and automatically captures from every Buoy tool you have installed — no per-source setup.

## Check the integration

Enable a configured source, trigger a known event in your app, and filter the timeline to that source. Capture settings and history limits determine what the timeline and exports contain.

## What you get

- **One timeline, every source** — network requests, AsyncStorage/MMKV writes, Redux actions, React Query queries and mutations, Zustand, Jotai, and navigation events, interleaved chronologically
- **Auto-detection** — if a Buoy tool is installed, its events just show up
- **Correlation badges** — related events are linked: a React Query fetch start → success pair shows as "1/2" so you can trace the full lifecycle
- **LLM export presets** — copy the timeline as Markdown tuned for AI agents, a Bug Report with timestamps and error details, machine-readable JSON, or Errors Only
- **Smart formatting** — nested JSON parsed, verbose fields stripped, and Redux events show only the state that actually changed
- **Filter and search** — source badges narrow the timeline, and header search filters it live as you type (matches titles, subtitles, and full network URLs — they stack, so filter to Network then search the failing endpoint)
- **`useUnifiedEvents` hook** — consume the merged event stream in your own components

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/events) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
