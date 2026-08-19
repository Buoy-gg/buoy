---
title: Assets
seoTitle: "React Native Asset Inventory — sizes, duplicates & unused"
id: tools-assets
description: "See every asset shipped in your app — bundled images, fonts, video and audio with real byte sizes, scale-variant coverage, duplicate detection, and shipped-but-never-loaded auditing. Zero configuration."
---

<!-- ::platform-badge platform="both" -->

How many megabytes of images are you shipping? Which ones does nobody ever see? Is that hero PNG in the bundle twice? React Native gives you **no** inventory of your bundled assets — the usual answer is unzipping a release build and spelunking through APK Analyzer after you've already shipped the bloat.

The Assets tool is that inventory, live in your app: every bundled asset — images, fonts, video, audio — with dimensions, `@1x/@2x/@3x` scale coverage, real byte sizes, duplicate-content detection, and the one thing no build-time tool can do: which assets are **shipped but never loaded** at runtime. Pure JavaScript, zero configuration.

Watch the inventory fill largest-first, catch the unused megabytes, filter what no code loads, then baseline the size regression — the real tool on mock data:

<!-- ::assets-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/assets" yarn="yarn add @buoy-gg/assets" pnpm="pnpm add @buoy-gg/assets" bun="bun add @buoy-gg/assets" -->

That's it — no register import, no config, no registration. Auto-discovery finds the installed package and the ASSETS tool appears in your floating menu:

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

> **Why zero-config?** Asset ids in React Native's runtime registry are contiguous, so the tool enumerates everything registered since app start the moment it opens — nothing needs to load early. Live updates stream in as lazily-required assets register later.

---

## What You Can Do

- **See the whole inventory, largest first** — every bundled asset with a thumbnail, dimensions, scale variants, and its size. Filter by kind (images / fonts / video / audio / data) or show only never-loaded assets.
- **Get real byte sizes** — in dev, every scale variant is measured from the Metro server, per variant and totaled. In release builds you still get decoded-memory estimates.
- **Find assets you ship but never use** — the tool diffs the full bundle graph (dev server) against what actually registered at runtime. Static grep tools can't know what's *loaded*; this does.
- **Catch duplicate content** — identical bytes shipped under different names or paths, flagged by content hash. One of the most common (and cheapest to fix) app-size wins.
- **Audit scale coverage** — images whose variants can't serve the current device's pixel ratio (blurry on 3x) are flagged per record.
- **Spot WebP wins** — large PNG/JPEG assets that would typically shrink 20–40% as WebP.
- **See every loaded font family** — build-time embedded and runtime loaded (via the expo-font native module when present).
- **Copy a markdown report** — the full inventory with findings, ready for an issue or PR description.

---

## How It Works

Three layers, each degrading gracefully:

1. **Runtime registry (everywhere, incl. release):** enumerates `@react-native/assets-registry` — every asset whose module has been evaluated — and patches `registerAsset` for live updates. Works in Expo Go, dev clients, bare RN, and release builds.
2. **Metro graph (dev):** fetches the dev server's full asset graph for bundle-wide coverage, source paths, and byte measurement — this is what powers never-loaded detection and real sizes.
3. **Expo enrichment (when present):** loaded font families and the expo-updates embedded-asset map, read through guarded globals — no extra dependencies for bare RN apps.

**Honest limits:** `.json` files (including Lottie) compile into the JS bundle as source modules, so they never reach the asset registry — they're visible in dev via the bundle graph only. Native-only resources (app icons, splash screens) live outside the JS bundle entirely and aren't listed.

---

## FAQ

### How do I find out how big my React Native app assets are?

Open the tool in a dev build — every scale variant of every bundled asset is measured from the Metro server and summed per asset, with kind totals in the header. Release builds still get decoded-memory estimates.

### Can it find assets I ship but never use?

Yes — in dev it fetches the full Metro bundle graph and diffs it against what actually registered at runtime, so anything bundled but never required shows up under UNUSED. Runtime knowledge is what static grep scripts are missing.

### How is this different from the Images tool?

Images shows what your app renders at runtime — per-load cache verdicts, timings, failures. Assets shows what your app ships in the bundle. The slow load is an Images problem; the megabytes are an Assets problem.

### How do I stop asset bloat from creeping back in?

Save a baseline before you start, then re-open the tool after any change — it reports added, removed and grown assets with the net byte delta, persisted across app restarts.
