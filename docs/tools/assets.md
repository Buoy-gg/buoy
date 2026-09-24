---
title: Assets
seoTitle: "React Native Asset Inventory — sizes, duplicates & runtime registration"
id: tools-assets
description: "Inspect React Native assets with available sizes, scale variants, duplicate checks, and development-only bundle coverage. Identify assets not loaded during a test session."
---

<!-- ::platform-badge platform="both" -->

Inspect assets registered in the running app, including dimensions, scale variants, and available size data. In development, the Metro graph adds bundle-wide information for duplicate and not-yet-loaded checks.

An asset not loaded during this session may still be used by another screen or flow. Exercise representative flows before removing it.

Buoy's own package assets are excluded from the inventory, size totals, and reports. The exclusion uses package source paths, so app assets with the same filenames remain visible.

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

- **See the whole inventory, largest first** — assets visible through the available inventory sources with a thumbnail, dimensions, scale variants, and its size. Filter by kind (images / fonts / video / audio / data) or show only assets not observed in the runtime registry.
- **Get real byte sizes** — in dev, every scale variant is measured from the Metro server, per variant and totaled. In release builds you still get decoded-memory estimates.
- **Find assets not observed in the runtime registry** — compare the bundle graph with React Native asset registrations during this app run. Other loading paths may not be captured.
- **Catch duplicate content** — identical bytes shipped under different names or paths, flagged by content hash. One of the most common (and cheapest to fix) app-size wins.
- **Audit scale coverage** — images whose variants can't serve the current device's pixel ratio (blurry on 3x) are flagged per record.
- **Spot WebP wins** — large PNG/JPEG candidates for conversion; measure the converted files before claiming savings.
- **See every loaded font family** — build-time embedded and runtime loaded (via the expo-font native module when present).
- **Copy a markdown report** — the full inventory with findings, ready for an issue or PR description.

---

## How It Works

Three layers, each degrading gracefully:

1. **Runtime registry (everywhere, incl. release):** enumerates `@react-native/assets-registry` — every asset whose module has been evaluated — and patches `registerAsset` for live updates. Works in Expo Go, dev clients, bare RN, and release builds.
2. **Metro graph (dev):** fetches the dev server's full asset graph for bundle-wide coverage, source paths, and byte measurement — this is what powers runtime registration comparisons and real sizes.
3. **Expo enrichment (when present):** loaded font families and the expo-updates embedded-asset map, read through guarded globals — no extra dependencies for bare RN apps.

**Limits:** `.json` files (including Lottie) compile into the JS bundle as source modules, so they never reach the asset registry — they're visible in dev via the bundle graph only. Native-only resources (app icons, splash screens) live outside the JS bundle entirely and aren't listed.

---

## FAQ

### Can I preview SVG assets?

On native apps, SVG thumbnails and detail previews use `expo-image` if it is installed and its native module is available. It is optional, and Assets does not require `react-native-svg`. When a preview cannot load, the tool shows a placeholder. Desktop and web use browser image rendering. The iOS system SVG decoder has limitations with some path commands.

### Why does the asset count grow after I open the tool?

The tool shows registered assets first, then asks the development server for the larger bundle inventory. A spinner and a "found so far" count show that discovery is still running. After eight seconds, a message explains the wait. You can keep browsing the assets already listed.

Size checks run after discovery. If discovery fails or exceeds 90 seconds, the tool keeps the assets it already found and offers Retry. Without a development server, it shows runtime-registered assets only.

### How do I find out how big my React Native app assets are?

Open the tool in a dev build — every scale variant of assets visible through the available inventory sources is measured from the Metro server and summed per asset, with kind totals in the header. Release builds still get decoded-memory estimates.

### Can it find assets I ship but never use?

In development, it compares the Metro bundle graph with React Native’s runtime asset registry. **Not observed** shows assets missing from that registry during this app run, across screens. Other loading paths may not register there, so this is not proof that an asset is unused.

### How is this different from the Images tool?

Images shows what your app renders at runtime — per-load cache verdicts, timings, failures. Assets shows what your app ships in the bundle. The slow load is an Images problem; the megabytes are an Assets problem.

Use **Duplicates** at the top of the asset list to show every asset that shares a content hash with another asset. The count includes all copies. Selecting it clears the search; select **All** or tap **Duplicates** again to return to the full list.

## Web support (unreleased)

Browser capture observes loaded resources. Add the Vite asset manifest or register a manifest from your bundler to include files before they load. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
