# @buoy-gg/core

[![npm version](https://img.shields.io/npm/v/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/core)
[![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/core)

Buoy adds a floating developer-tools menu to your React Native app. Install the tools you need to inspect captured requests, app state, storage, and performance.

## Install and quick start

Install from your app’s directory, then sign in:

```bash
npm install @buoy-gg/core @buoy-gg/network
npx --package=@buoy-gg/core buoy login
```

In an Expo Router project, keep your existing navigator and providers. For example:

```tsx
import { Stack } from "expo-router";
import { Buoy, FloatingDevTools } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });

export default function RootLayout() {
  return (
    <>
      <Stack />
      <FloatingDevTools />
    </>
  );
}
```

The login command writes the Expo key to `.env.local`. Keep the navigator and menu inside your existing providers. Without Expo Router, keep your app’s root content in place of `Stack`. For React Native CLI, load the key through your app’s environment configuration and pass it to `Buoy.init`; `.env.local` is not loaded automatically.

Restart the development server after installation. Open Buoy, complete any account prompt, and select Network. Trigger a new HTTP request in your app and check its URL and status. If no request appears, check whether the app used cached data or an unsupported transport.

See the [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) for the full setup and [Installation](https://buoy.gg/buoy/latest/docs/installation) for requirements. Other tools may need store registration, provider context, or native dependencies.

The dial shows the most recently opened tool first. Tools you haven’t opened keep their registration order.

## Tools

| Tool | Package | What it does |
| --- | --- | --- |
| [Network](https://buoy.gg/buoy/latest/docs/tools/network) | `@buoy-gg/network` | Inspect HTTP requests captured through your React Native app’s global `fetch` and `XMLHttpRequest` hooks. Open a request to see its URL, status, headers, timing, and captured body. |
| [Storage](https://buoy.gg/buoy/latest/docs/tools/storage) | `@buoy-gg/storage` | Browse and edit supported AsyncStorage, registered MMKV instances, and registered SecureStore keys inside your app. |
| [Time Machine](https://buoy.gg/buoy/latest/docs/tools/time-machine) | `@buoy-gg/time-machine` | Save and restore supported client state; external side effects are not reversed. |
| [Ask Buoy](https://buoy.gg/buoy/latest/docs/tools/ask-buoy) | `@buoy-gg/ask-buoy` | Use an in-app assistant with your model endpoint and configured tools. |
| [Env](https://buoy.gg/buoy/latest/docs/tools/env) | `@buoy-gg/env` | Inspect runtime environment values and validate required variables. |
| [Query](https://buoy.gg/buoy/latest/docs/tools/react-query) | `@buoy-gg/react-query` | Inspect the TanStack Query cache used by your app. Refetch or invalidate a query and simulate states while checking the screen that consumes it. |
| [Routes](https://buoy.gg/buoy/latest/docs/tools/routes) | `@buoy-gg/route-events` | Inspect recorded navigation events and supported routes. |
| [Debug Borders](https://buoy.gg/buoy/latest/docs/tools/debug-borders) | `@buoy-gg/debug-borders` | Show layout borders and component labels over your React Native app. Tap a label to inspect the component’s available properties. |
| [Highlight Updates](https://buoy.gg/buoy/latest/docs/tools/highlight-updates) | `@buoy-gg/highlight-updates` | Highlight observed React component updates in development builds and inspect the available render details. |
| [Bench](https://buoy.gg/buoy/latest/docs/tools/perf-monitor) | `@buoy-gg/perf-monitor` | Measure available frame-rate, CPU, and memory metrics on a device. Record comparable runs to investigate performance changes. |
| [JS Top](https://buoy.gg/buoy/latest/docs/tools/js-top) | `@buoy-gg/js-top` | Task Manager for the JS thread — which timers, Promise chains & callbacks eat your JS FPS |
| [Images](https://buoy.gg/buoy/latest/docs/tools/images) | `@buoy-gg/images` | Inspect image loads observed by Buoy’s supported image hooks, including timing, dimensions, and estimated decoded memory. |
| [Assets](https://buoy.gg/buoy/latest/docs/tools/assets) | `@buoy-gg/assets` | Inspect assets visible to the runtime registry and, in development, the Metro dependency graph. Compare sizes and find possible duplicates. |
| [Events](https://buoy.gg/buoy/latest/docs/tools/events) | `@buoy-gg/events` | Review events from configured Buoy tools in one timeline. Filter by source, inspect an event, and export a selection for debugging. |
| [Console](https://buoy.gg/buoy/latest/docs/tools/console) | `@buoy-gg/console` | Read captured JavaScript console logs in your React Native app. Filter messages by level, search their contents, and inspect logged objects. |
| [Sentry](https://buoy.gg/buoy/latest/docs/tools/sentry) | `@buoy-gg/sentry` | Inspect captured Sentry envelopes and diagnostic estimates. |
| [Redux](https://buoy.gg/buoy/latest/docs/tools/redux) | `@buoy-gg/redux` | Inspect your connected Redux store, review captured actions and state changes, and dispatch actions from your device. |
| [Zustand](https://buoy.gg/buoy/latest/docs/tools/zustand) | `@buoy-gg/zustand` | Inspect the Zustand stores you register with Buoy. Review changes, edit state, and restore retained snapshots. |
| [Jotai](https://buoy.gg/buoy/latest/docs/tools/jotai) | `@buoy-gg/jotai` | Inspect registered Jotai atoms and their captured changes, with live values and per-atom history. |
| [Impersonate](https://buoy.gg/buoy/latest/docs/tools/impersonate) | `@buoy-gg/impersonate` | Connect your app’s user-switching flow to Buoy so testers can switch test accounts, roles, or flags without rebuilding. |
| [Overlay](https://buoy.gg/buoy/latest/docs/tools/image-overlay) | `@buoy-gg/image-overlay` | Place a reference image over your app to compare spacing, alignment, and sizing on the device. |

TV tools and the iOS Simulator camera have separate [platform requirements](https://buoy.gg/buoy/latest/docs/overview).

## Desktop and AI

Inspect configured tools in the floating menu on your device. You can also connect them to:

- [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop), for desktop panels and remote actions. React Native apps need `@buoy-gg/external-sync`; follow the [connection guide](https://buoy.gg/buoy/latest/docs/desktop) and sign in to Desktop separately.
- [MCP](https://buoy.gg/buoy/latest/docs/mcp), for an AI editor to inspect and act on the app. Configure both process and device accounts; data and action tools require Pro.
- [Ask Buoy](https://buoy.gg/buoy/latest/docs/tools/ask-buoy), for an in-app assistant. It requires Pro, a model endpoint you configure, and the tool integrations its tasks depend on.

Supported features vary by platform, build, and installed tools. [Flutter](https://buoy.gg/buoy/latest/docs/flutter/quick-start) has a separate debug-build setup.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

## Production and connections

Start with a development build. Before enabling Buoy in a shipped app, restrict access using your app’s authorization checks and review the [component reference](https://buoy.gg/buoy/latest/docs/floating-devtools). Production access requires Pro, and development-only capabilities remain unavailable.

Hiding the menu is not a substitute for backend authorization. Headless mode hides the on-device UI; it still requires account admission and sync configuration. Release sync is disabled by default and must be explicitly enabled.

Device sessions can travel over your LAN to the configured broker. Buoy also makes account and license requests; development telemetry is described in [Telemetry](https://buoy.gg/buoy/latest/docs/telemetry). Ask Buoy sends model requests to the endpoint you configure.

## License

Proprietary software. © Buoy LLC. All rights reserved. See the [Terms of Service](https://buoy.gg/terms).

### Custom floating controls

The DevTools bubble uses the shared `FloatingTool` shell. Custom tools can use the same shell with `openFloatingTool`; `FloatingDevTools` already mounts the host. See the [integration guide](https://buoy.gg/buoy/latest/docs/custom-tools/floating-tools).

## Web support (unreleased)

Import `FloatingDevTools` from `@buoy-gg/core/web` and pass the tool namespaces through `modules`. Settings, account status, and modal persistence share their implementation with native. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.

In a Vite app, run `pnpm exec buoy login` from the app directory. The CLI writes `VITE_BUOY_KEY` to `.env.development.local` for Free accounts or `.env.local` for paid accounts. Pass `import.meta.env.VITE_BUOY_KEY` to the host's `licenseKey` prop.

The browser dial supports Tab, arrow keys between tools, Enter to activate controls, and Escape to close. Its center button opens settings. Drag panel backgrounds or handles to move them; tabs and inputs keep their normal mouse behavior. Minimized tools stay above the floating bar and scroll when needed. They open below only when there is not enough room above for one row.

The web dial follows the shared Background selection in Settings, including changes made while the dial is open.
