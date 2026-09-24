---
title: Web support
description: Run Buoy's shared developer tools in React web apps.
---

Browser support is available in this source checkout. These changes have not been published to npm yet. Build or pack this checkout to try them.

The browser uses the same tool panels, stores, filters, actions, snapshot providers, and sync protocol as React Native. React Native Web renders the panels. Small platform modules handle browser storage, routing, DOM inspection, clipboard access, images, and performance measurements. Changes to a shared tool implementation feed both builds. Settings, account status, modal persistence, and the settings event bus are also shared.

## Setup

Install matching Buoy package builds with React, React DOM, and React Native Web 0.21. A browser app does not need React Native or Expo. Keep the framework dependencies that your selected tools inspect: Zustand, Jotai, React Redux, or TanStack Query, for example.

Import `/web` entries explicitly. This selects the browser API in TypeScript and server bundlers. Browser bundlers also select these builds through the packages' `browser` export condition. Native applications keep using the existing root imports.

For render, layout, focus, and element inspection, put this import **before React DOM** in the application entry point:

```ts
import '@buoy-gg/core/web/register';
```

It installs the React DevTools backend before React mounts, preserves Vite Fast Refresh callbacks, and leaves an existing complete DevTools hook in place. Its server entry is inert. It does not open a desktop connection. Ordinary production builds do not initialize the backend; an authorized internal release can call `registerBrowserDevTools({ enableInRelease: true })` before mounting React.

Register the modules you use. The host discovers their presets, mounts capture roots and overlays, and registers their existing action adapters:

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

Mount the host inside the app's existing providers and development-only boundary. Keep `modules` stable outside the component. Supply the existing Free or Pro account key through the app's environment configuration. The Vite example uses `import.meta.env.VITE_BUOY_KEY`. Account checks, feature limits, and development-only restrictions still apply.

For Vite, run `pnpm exec buoy login` from the app directory and complete browser sign-in. The CLI writes `VITE_BUOY_KEY` to `.env.development.local` for Free accounts or `.env.local` for paid accounts. Reload the app afterward. From this repository's root, use `pnpm --filter example-web exec buoy login` to configure the example.

Use namespace keys matching package names, such as `'react-query'`, `'route-events'`, and `'time-machine'`. Pass configured presets through `apps={[createWebTool(preset)]}` when a tool needs app callbacks. A configured preset replaces the module's default preset with the same ID. Custom tools retain their modal metadata and sync adapter.

## Tool integrations

| Tools | Browser source and setup |
| --- | --- |
| Network | Host-managed `fetch` and XHR capture, request details, GraphQL metadata, response overrides, conditions, pins, and saved requests. A standalone collector can use `startNetworkCapture()` and its cleanup function. |
| Storage | `localStorage` and `sessionStorage`, with key editing, captured writes, undo, and snapshot restore. Local storage uses the shared async backend; session storage uses the shared synchronous backend. |
| Zustand | Register live stores with `watchStores`. Actions remain callable after edits and restores. |
| Jotai | Register atoms with `watchAtoms(store, atoms)` and use the app's store. |
| React Query | Mount below the existing `QueryClientProvider`. Cache observation, edits, and snapshots use that client. |
| Redux | Mount below the existing Redux provider. Capture starts with the host. Keep the native early-import integration when inspecting native store creation. |
| Routes | Captures History API and hash navigation. Register known paths with `registerBrowserRoutes(paths)` to populate the route list. Connect framework navigation with `registerBrowserRouter(adapter)`. |
| Env | Pass explicit public values through `setRemoteEnv(values)` and required keys through the host's `requiredEnvVars`. Build-time replacements cannot be enumerated automatically. |
| Console, Events | Capture browser JavaScript logs and aggregate events from the registered tools. |
| Sentry | Pass `sentryGetClient={Sentry.getClient}` from the app's existing browser SDK. Buoy observes outgoing envelopes and supported diagnostic hooks. |
| Time Machine, Scenarios | Use the same snapshot providers, restore actions, recording, and scenario runner as native. Only registered sources participate. |
| Ask Buoy | Uses the shared agent and registered tool actions. Configure the existing model endpoint and account access. |
| Impersonate | Use `createImpersonateTool` with the app's user-search callback. Browser fetch and XHR receive the configured header. |
| Performance, JS Top | Browser frame rate, available JS heap and long-task measurements, and measured JavaScript callbacks. Native CPU, RSS, and thermal measurements have no browser equivalent. |
| Highlight Updates, Debug Borders | React render tracking and DOM measurement through the early DevTools registration. |
| Image Overlay | Matches `data-testid="image-target:Name"` elements or positions a free overlay. Clipboard access follows browser permissions. |
| Images | Observes DOM images, their dimensions and accessible load timing; supports source, loading/error, blank, size, and cache-bypass simulations. WebP savings previews use Canvas. |
| Assets | Observes loaded resources and accepts a build manifest for files that have not loaded. See below. |
| TV Remote, Focus Inspector | Observe browser keyboard and DOM focus events. They retain their existing remote action interfaces. Browser input does not emulate a TV's native focus engine. |

For React Router, Next.js, or another router, supply its real navigation methods:

```ts
import { registerBrowserRouter } from '@buoy-gg/shared-ui/web/utils';

const cleanup = registerBrowserRouter({
  push: path => router.push(path),
  replace: path => router.replace(path),
  back: () => router.back(),
});
```

Register and clean up the adapter with the router's lifetime. The default adapter changes same-origin browser history. Captured history starts when Buoy mounts; browsers do not expose the full pre-existing history stack.

## Assets that have not loaded

The Vite plugin generates an inventory without downloading each asset in the browser:

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

In production builds, the manifest includes emitted assets and media in `public`. During Vite development, it lists public media; requested source assets also appear through runtime observation. Honor your application's base path when loading the manifest. Other bundlers can generate an array of `{ url, bytes?, hash?, width?, height? }` and pass it to `registerBrowserAssetManifest`.

## Desktop and MCP

Add `'external-sync': externalSync` to `modules`, importing the namespace from `@buoy-gg/external-sync/web`. The browser publishes the same tool capabilities and handles the same actions as mobile. Desktop and MCP retain their own account requirements.

The default broker is the page hostname on port 42831. For another host, pass `externalSync={{ socketURL: 'https://your-broker.example' }}`. An HTTPS app needs a browser-permitted broker connection. Use `externalSync={false}` to disable the connection. `headless` keeps capture and remote actions active without the floating menu. Release connections require both a real Pro license and `externalSync.enableInRelease`.

## Browser boundaries

- Network hooks cover the current page's fetch and XHR calls. Other frames, workers, WebSockets, and browser-internal traffic need separate collectors.
- CORS controls readable headers and bodies. Cross-origin resource sizes and timings may need `Timing-Allow-Origin`. Buoy reports unavailable measurements instead of inventing them. See [MDN's Resource Timing guide](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API/Resource_timing).
- Storage capture observes method calls and browser storage events. Direct property assignments, IndexedDB, cookies, and native secure storage are separate APIs.
- Images observes DOM image elements. CSS backgrounds and Canvas drawings do not expose the same image lifecycle. Cache bypass changes the image URL; page JavaScript cannot clear the browser's HTTP cache.
- Browser tests exercise shared behavior. Keep device tests for native rendering, gestures, native modules, secure storage, and hardware measurements.
- Load development tools separately from the public app bundle. Import only the tools you use; the complete suite includes substantial UI and visual assets.

## Verification

From the repository root:

```bash
pnpm verify:web --next
pnpm test:web
pnpm test:web:e2e
pnpm test:web:browsers
pnpm --filter example-web run build
```

`verify:web` builds and packs all 32 browser packages, installs them in a clean React consumer, and checks TypeScript, a production bundle, server imports/rendering, and shared store identity. It rejects installations that pull in React Native or Expo. The `--next` flag also builds and prerenders a Next.js App Router consumer with webpack.

The Cypress suite exercises every shared panel and the main tool actions, including real HTTP transport, state restoration, image handling, Sentry capture, and desktop broker actions. The Playwright suite checks common behavior in Chromium, Firefox, and WebKit. Install test browsers with `pnpm exec playwright install chromium firefox webkit`; install Cypress with `pnpm --filter example-web exec cypress install`.

Use `pnpm --filter example-web dev` for manual testing. After editing package source, run `pnpm build:web` and reload; the example consumes compiled package exports. See the checkout's `docs/WEB_PORT_PROGRESS.md` for recorded results and remaining release checks.
