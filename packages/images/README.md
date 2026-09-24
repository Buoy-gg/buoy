# @buoy-gg/images

[![npm version](https://img.shields.io/npm/v/@buoy-gg/images?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/images) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/images?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/images)

Inspect image loads observed by Buoy’s supported image hooks, including timing, dimensions, and estimated decoded memory.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

Native image loading can bypass JavaScript network capture. This package instruments supported React Native `Image` and `expo-image` paths to provide image-specific details.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/images
```

Then add the register import as the **first line** of your app entry file (`index.js` / `index.ts`):

```ts
import "@buoy-gg/images/register";
```

> Why first? RN core's `<Image>` instrumentation uses React Native's official component-decorator hook, which must be installed before the Image module first evaluates. expo-image capture has no such constraint — it works no matter when the tool loads. If you skip the import, the tool tells you what it's missing instead of sitting silently empty.

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

The IMAGES tool appears in the menu. Open it and browse image loads observed during the session.

## Check the integration

Load a supported image after the capture hook is installed and inspect the entry. Treat cache results according to the image library’s available signals and decoded memory as an estimate. Native image paths outside those hooks may not appear.

## What you get

- **Live image registry** — observed RN `<Image>` and expo-image loads: thumbnail, source, status, load time, decoded dimensions
- **Cache verdicts** — memory / disk / network per load (expo-image reports it directly; RN core is classified via `Image.queryCache`)
- **Oversize auditing** — decoded size vs displayed size × device pixel ratio, Lighthouse-style: green within 10%, red beyond 50%, with estimated wasted decoded memory and the exact size you should serve
- **Memory accounting** — estimated decoded-bitmap bytes per image and totals across the session (catch ballooning before the OOM)
- **Failure log** — captured `onError` events with the message, and on iOS the HTTP status code + response headers
- **Cache actions** — clear expo-image memory/disk caches from the detail view
- **Upscale detection** — tiny sources stretched into big boxes (blurry images) get flagged too

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

## Web support (unreleased)

Browser capture observes DOM images and supports shared overrides and size analysis. Savings previews use Canvas; cross-origin reads require permission from the image server. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
