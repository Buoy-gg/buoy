# @buoy-gg/assets

[![npm version](https://img.shields.io/npm/v/@buoy-gg/assets?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/assets) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/assets?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/assets)

Inspect assets visible to the runtime registry and, in development, the Metro dependency graph. Compare sizes and find possible duplicates.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

Use the inventory to investigate large registered assets and possible duplication. Development builds can also query Metro; release builds have less source information.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/assets
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

The ASSETS tool appears in the menu. Open it and browse discovered assets, largest first.

Buoy's own package assets are excluded from the inventory and its totals. App assets and assets from other libraries remain visible.

SVG thumbnails and detail previews use `expo-image` when it is installed and available in the native app. It is an optional peer; `react-native-svg` is not required. Without a compatible renderer, the tool shows a preview-unavailable label. Desktop and web use browser image rendering. Some SVG paths may not render correctly with the iOS system decoder.

Registered assets appear immediately. A spinner and a "found so far" count stay visible while the tool queries the development server for more assets. After eight seconds, a message explains that discovery is taking longer than usual. Size checks run afterward. If discovery fails or exceeds 90 seconds, the existing list stays available with a Retry button.

## Check the integration

Find a known bundled asset and compare its source and dimensions. “Not observed” means not seen in React Native’s asset registry during this app run; it does not prove an asset is unused or safe to delete. Runtime coverage is not a complete inventory of native resources.

## What you get

- **Inventory, largest first** — discovered assets with a thumbnail, dimensions, `@1x/@2x/@3x` scale coverage, and its size. Filter by kind, search by name, sort by size/name/type
- **Size information** — development builds can measure available variants through Metro. Release decoded-memory estimates are not encoded file sizes
- **Unobserved assets** — compare the available development bundle graph with assets registered during the session. Review candidates before deleting them
- **Duplicate content** — identical bytes shipped under different names, flagged by content hash (one of the cheapest app-size wins there is)
- **Audit findings** — images that decode larger than the device screen, heavy GIFs (every frame decodes into memory), unsubset icon fonts, WebP conversion candidates, missing/broken scale variants
- **Loaded font families** — build-time embedded and runtime loaded (via the expo-font native module when present)
- **Markdown report** — the full inventory with findings, ready to paste into an issue or PR

## How it relates to @buoy-gg/images

[`@buoy-gg/images`](https://www.npmjs.com/package/@buoy-gg/images) shows what your app **renders** at runtime — per-load cache verdicts, timings, failures. Assets inspects the available asset inventory. Use both views to investigate loading behavior and bundle size.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

Use **Duplicates** at the top of the asset list to show every asset that shares a content hash with another asset. The count includes all copies. Selecting it clears the search; select **All** or tap **Duplicates** again to return to the full list.

## Web support (unreleased)

Browser capture observes loaded resources. Add the Vite asset manifest or register a manifest from your bundler to include files before they load. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
