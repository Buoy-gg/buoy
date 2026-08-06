# @buoy-gg/assets

[![npm version](https://img.shields.io/npm/v/@buoy-gg/assets?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/assets) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/assets?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/assets)

**See everything your app ships — every bundled image, font, video and audio file, with sizes, duplicate detection, and the ones nobody ever loads.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

How many megabytes of images are you shipping? Is that hero PNG in the bundle twice? React Native gives you no inventory of your bundled assets — the usual answer is unzipping a release build and spelunking through APK Analyzer after the bloat already shipped. This tool is that inventory, live in your app. Pure JS, zero configuration — no register import, no config.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/assets
```

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

The ASSETS tool appears in the menu. Open it and browse everything the app ships, largest first.

## What you get

- **The whole inventory, largest first** — every bundled asset with a thumbnail, dimensions, `@1x/@2x/@3x` scale coverage, and its size. Filter by kind, search by name, sort by size/name/type
- **Real byte sizes** — in dev, every scale variant is measured from the Metro server; release builds still get decoded-memory estimates
- **Shipped-but-never-loaded detection** — the tool diffs the full bundle graph against what actually registered at runtime. Static grep tools can't know what's *loaded*; this does
- **Duplicate content** — identical bytes shipped under different names, flagged by content hash (one of the cheapest app-size wins there is)
- **Baseline diff** — save a baseline, make your change, and see exactly what it added: new assets, removed assets, grown assets, net bytes. App-size regression prevention without CI tooling
- **Audit findings** — images that decode larger than the device screen, heavy GIFs (every frame decodes into memory), unsubset icon fonts, WebP conversion candidates, missing/broken scale variants
- **Loaded font families** — build-time embedded and runtime loaded (via the expo-font native module when present)
- **Markdown report** — the full inventory with findings, ready to paste into an issue or PR

## How it relates to @buoy-gg/images

[`@buoy-gg/images`](https://www.npmjs.com/package/@buoy-gg/images) shows what your app **renders** at runtime — per-load cache verdicts, timings, failures. This tool shows what your app **ships** in the bundle. They complement each other: images finds the slow load; assets finds the megabytes you never needed to ship.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp) — `get_assets` for the inventory and findings, `asset_action` for rescans and the baseline workflow.

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.
