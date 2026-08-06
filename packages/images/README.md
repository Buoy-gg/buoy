# @buoy-gg/images

[![npm version](https://img.shields.io/npm/v/@buoy-gg/images?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/images) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/images?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/images)

**See every image your app loads — where it came from, how long it took, what it cost, and why it failed.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

Image HTTP requests never touch the JS network stack — network devtools are permanently blind to them. This tool is the visibility layer: a live registry of every RN `<Image>` and [`expo-image`](https://docs.expo.dev/versions/latest/sdk/image/) load, with cache verdicts, timings, and size auditing. Pure JS — works in Expo Go and release builds.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/images
```

Then add the register import as the **first line** of your app entry file (`index.js` / `index.ts`):

```ts
import "@buoy-gg/images/register";
```

> Why first? RN core's `<Image>` instrumentation uses React Native's official component-decorator hook, which must be installed before the Image module first evaluates. expo-image capture has no such constraint — it works no matter when the tool loads. If you skip the import, the tool tells you what it's missing instead of sitting silently empty.

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

The IMAGES tool appears in the menu. Open it and browse every image the session has loaded.

## What you get

- **Live image registry** — every RN `<Image>` and expo-image load: thumbnail, source, status, load time, decoded dimensions
- **Cache verdicts** — memory / disk / network per load (expo-image reports it directly; RN core is classified via `Image.queryCache`)
- **Oversize auditing** — decoded size vs displayed size × device pixel ratio, Lighthouse-style: green within 10%, red beyond 50%, with estimated wasted decoded memory and the exact size you should serve
- **Memory accounting** — estimated decoded-bitmap bytes per image and totals across the session (catch ballooning before the OOM)
- **Failure log** — every `onError` with the message, and on iOS the HTTP status code + response headers
- **Cache actions** — clear expo-image memory/disk caches from the detail view
- **Upscale detection** — tiny sources stretched into big boxes (blurry images) get flagged too

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.
