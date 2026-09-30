---
title: Installation
seoTitle: "Install React Web DevTools — Buoy setup for React web apps"
id: web-installation
description: "Full install guide for Buoy in a React web app: packages, the early DevTools hook, module registration, per-tool setup, routers, assets, Desktop and MCP."
---

This is the complete version of [Quick Start](./quick-start). The browser uses the same tool panels, stores, filters, actions, snapshot providers and sync protocol as React Native. React Native Web renders the panels, and small browser modules handle storage, routing, DOM inspection, the clipboard, images and performance timing. Settings, account status and modal persistence are shared too.

## Requirements

| | |
|---|---|
| React | React and React DOM |
| Renderer | `react-native-web` 0.21 |
| Buoy packages | `7.0.41` or later, imported from their `/web` entries |
| Not needed | React Native, Expo |

Keep the libraries your tools inspect, such as Zustand, Jotai, React Redux or TanStack Query.

## Packages

<!-- ::PM npm="npm install @buoy-gg/core react-native-web" yarn="yarn add @buoy-gg/core react-native-web" pnpm="pnpm add @buoy-gg/core react-native-web" bun="bun add @buoy-gg/core react-native-web" -->

For plain Markdown readers, the command is:

```bash
npm install @buoy-gg/core react-native-web
```

Then install the tools you want, for example `@buoy-gg/network` or `@buoy-gg/zustand`. Import the `/web` entries explicitly. That selects the browser API in TypeScript and in server bundlers. Browser bundlers also pick these builds through the packages' `browser` export condition. Native apps keep using the root imports.

## The early DevTools hook

Put this import at the top of your entry file, **before React DOM**. Render, layout, focus and element inspection need it, and so does Network if you want the requests made while the page loads:

```ts
import '@buoy-gg/core/web/register';
```

It installs the React DevTools backend before React mounts, keeps Vite Fast Refresh working, and leaves an existing complete DevTools hook alone. It also records the page's `fetch` and XHR calls until Network starts, at most the latest 100, and Network then lists them with the rest. Its server entry does nothing, and it doesn't open a Desktop connection. In a production build it runs only in browsers where `FloatingDevTools` has rendered in the past week, so visitors who never see Buoy are unaffected. [Production builds](./frameworks#production-builds) has the details.

The right place for the import depends on the framework. [Frameworks](./frameworks) covers Next.js, React Router and TanStack Start.

## Mounting

Register the modules you use. The host finds their presets, mounts their capture and overlays, and registers their actions:

```tsx
'use client';

import { useEffect } from 'react';
import { FloatingDevTools } from '@buoy-gg/core/web';
import * as network from '@buoy-gg/network/web';
import * as storage from '@buoy-gg/storage/web';
import * as zustand from '@buoy-gg/zustand/web';
import { useCounterStore } from './stores';

const modules = { network, storage, zustand };

export function DevTools({ licenseKey }: { licenseKey: string }) {
  useEffect(() => zustand.watchStores({ counter: useCounterStore }), []);
  return <FloatingDevTools modules={modules} licenseKey={licenseKey} />;
}
```

Mount the host inside the app's providers, behind a development check or, in production, a check for the users who should see it. Keep `modules` outside the component so it stays stable. Use keys that match the package names, such as `'react-query'`, `'route-events'` and `'time-machine'`.

A tool that needs callbacks from your app is configured as a preset and passed through `apps={[createWebTool(preset)]}`. It replaces the module's default preset with the same ID. Custom tools keep their modal metadata and sync adapter.

## License key

A Free or Pro account key is required. For Vite, run this from the app directory and finish the browser sign-in:

```bash
npx --package=@buoy-gg/core buoy login
```

The CLI writes `VITE_BUOY_KEY` to `.env.development.local` for a Free account or `.env.local` for a paid one. Restart the dev server, then pass `licenseKey={import.meta.env.VITE_BUOY_KEY}`. Other bundlers supply the key through their own environment configuration. Account checks, feature limits and development-only restrictions still apply.

## Tool setup

Each tool's own page is shared with React Native. This is what changes in the browser.

| Tool | Browser source and setup |
| --- | --- |
| [Network](../tools/network) | The page's `fetch` and XHR calls, with request details, GraphQL metadata, response overrides, conditions, pins and saved requests. A standalone collector can call `startNetworkCapture()` and its cleanup function. |
| [Storage](../tools/storage) | `localStorage` and `sessionStorage`, with key editing, captured writes, undo and snapshot restore. |
| [Zustand](../tools/zustand) | Register live stores with `watchStores`. Actions stay callable after edits and restores. |
| [Jotai](../tools/jotai) | Register atoms with `watchAtoms(store, atoms)`, using the app's store. |
| [React Query](../tools/react-query) | Mount inside the existing `QueryClientProvider`. Cache observation, edits and snapshots use that client. |
| [Redux](../tools/redux) | Mount inside the existing Redux provider. Capture starts with the host. To let Time Machine restore the store, add `buoyDevtoolsEnhancer` from `@buoy-gg/redux/web` to its enhancers. |
| [Routes](../tools/routes) | History API and hash navigation. List known paths with `registerBrowserRoutes(paths)` and connect your router with `registerBrowserRouter(adapter)`, shown below. |
| [Env](../tools/env) | Pass public values with `setRemoteEnv(values)` and required keys through the host's `requiredEnvVars`. Buoy can't list values a bundler replaced at build time. |
| [Console](../tools/console), [Events](../tools/events) | Browser JavaScript logs, and events from the registered tools. |
| [Sentry](../tools/sentry) | Pass `sentryGetClient={Sentry.getClient}` from the app's browser SDK. Buoy observes outgoing envelopes and supported diagnostic hooks. The Sentry package isn't on npm yet. |
| [Time Machine](../tools/time-machine) | The same snapshots, restore actions and recording as native. Only registered sources take part. |
| [Ask Buoy](../tools/ask-buoy) | The shared agent, using the registered tools' actions. Configure the model endpoint and account access as on native. |
| [Impersonate](../tools/impersonate) | Use `createImpersonateTool` with the app's user-search callback. The page's `fetch` and XHR calls get the configured header. |
| [Perf Monitor](../tools/perf-monitor), [JS Top](../tools/js-top) | Frame rate, JS heap where the browser exposes it, long tasks and measured JavaScript callbacks. CPU, memory and thermal readings have no browser equivalent. |
| [Highlight Updates](../tools/highlight-updates) | React render tracking and DOM measurement, through the early DevTools hook. |
| [Image Overlay](../tools/image-overlay) | Matches `data-testid="image-target:Name"` elements or places a free overlay. Clipboard access follows browser permissions. |
| [Assets](../tools/assets) | Resources the page loaded, plus a build manifest for files that haven't loaded. See below. |

Debug Borders and Images also have browser builds; their pages cover the web setup. TV Remote and Focus Inspector observe browser keyboard and DOM focus events, which is not the same as a TV's native focus engine.

## Routers

For React Router, Next.js or another router, give Buoy its real navigation methods:

```ts
import { registerBrowserRouter } from '@buoy-gg/shared-ui/web/utils';

const cleanup = registerBrowserRouter({
  push: path => router.push(path),
  replace: path => router.replace(path),
  back: () => router.back(),
});
```

Register the adapter for the router's lifetime and call `cleanup` when it goes away. Without an adapter, Buoy changes same-origin browser history directly. History capture starts when Buoy mounts, because browsers don't expose the history stack from before that.

## Assets that haven't loaded

The Vite plugin builds an inventory without downloading each asset in the browser:

```ts
// vite.config.ts
import { buoyAssets } from '@buoy-gg/assets/vite';
export default { plugins: [buoyAssets()] };
```

Load it from your development setup:

```ts
import { loadBrowserAssetManifest } from '@buoy-gg/assets/web';
await loadBrowserAssetManifest('/buoy-assets.json');
```

In production builds the manifest includes emitted assets and media in `public`. During Vite development it lists public media, and source assets show up as the page requests them. Respect your app's base path when loading the manifest. Other bundlers can generate an array of `{ url, bytes?, hash?, width?, height? }` and pass it to `registerBrowserAssetManifest`.

## Desktop and MCP

Install `@buoy-gg/external-sync` and add `'external-sync': externalSync` to `modules`, importing the namespace from `@buoy-gg/external-sync/web`. The browser publishes the same tool capabilities and handles the same actions as a phone. Desktop and MCP keep their own account requirements.

In development the default broker is the page's hostname on port 42831, which is your machine even when you open the page from a phone on the same Wi-Fi. For another host, pass `externalSync={{ socketURL: 'http://192.168.1.20:42831' }}`. `externalSync={false}` turns the connection off. `headless` keeps capture and remote actions running without the floating menu.

Production builds connect only with a real Pro license and `externalSync.enableInRelease`, and default to `http://localhost:42831`, the Buoy Desktop on the admin's own machine. The first time a page from your production site connects, Buoy Desktop asks whether to allow that site. Production builds refuse plain `http://` to another machine unless you also set `allowInsecureNetwork`, and they refuse Impersonate's user search and start actions unless you pass `releaseActions: ['impersonate']`. See [Release builds](../desktop#release-builds).

## Browser boundaries

- Network capture covers the current page's `fetch` and XHR calls, starting when the early hook loads, or when the host mounts if you skip the hook. Other frames, workers, WebSockets and browser-internal traffic need separate collectors.
- CORS decides which headers and bodies are readable. Cross-origin resource sizes and timings may need `Timing-Allow-Origin`, and Buoy reports them as unavailable rather than guessing. See [MDN's Resource Timing guide](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API/Resource_timing).
- Storage capture sees method calls and browser storage events. Direct property assignments, IndexedDB, cookies and native secure storage are separate APIs.
- Images sees DOM image elements. CSS backgrounds and Canvas drawings don't have the same lifecycle. Cache bypass changes the image URL, since page JavaScript can't clear the browser's HTTP cache.
- Keep device testing for native rendering, gestures, native modules, secure storage and hardware measurements.
- Load the dev tools separately from your public bundle, and import only the tools you use. The full suite includes a lot of UI and images.
