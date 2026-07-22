---
title: Images
id: tools-images
description: "Debug every image in your app — a live registry of RN <Image> and expo-image loads with cache verdicts (memory/disk/network), timings, oversize + wasted-memory auditing, and a failure log. Works in Expo Go and release builds."
---

<!-- ::platform-badge platform="both" -->

Images not loading? Loading slow? Stale avatars that never update? Memory ballooning until the app dies? React Native gives you **zero** visibility into any of it — image HTTP requests never touch the JS network stack, so even network devtools are permanently blind to them.

The Images tool is the visibility layer: every image your app loads — React Native's `<Image>` and `expo-image` — appears in a live registry with where it came from (memory cache / disk cache / network), how long it took, how big it decoded, and exactly why it failed. Pure JavaScript — works in Expo Go and release builds.

## Installation

<!-- ::PM npm="npm install @buoy-gg/images" yarn="yarn add @buoy-gg/images" pnpm="pnpm add @buoy-gg/images" bun="bun add @buoy-gg/images" -->

Add the register import as the **first line** of your app entry file (`index.js` / `index.ts`):

```ts
import "@buoy-gg/images/register";
```

Then the IMAGES tool appears in your floating menu:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { imagesToolPreset } from "@buoy-gg/images";

<FloatingDevTools apps={[imagesToolPreset]} />
```

> **Why first?** RN core's `<Image>` instrumentation uses React Native's official component-decorator hook, which must be installed before the Image module first evaluates. expo-image capture has no timing constraint — it works whenever the tool loads. If the import is missing or too late, the tool tells you exactly what to fix instead of sitting silently empty.

---

## What You Can Do

- **See every image load, live** — thumbnail, source URL, load time, decoded dimensions, format, status. Tap for the full detail view.
- **Get cache verdicts per load** — memory, disk, or network. expo-image reports it directly on every load; RN core images are classified via `Image.queryCache` right after loading.
- **Catch oversized sources** — the tool compares decoded pixels against the laid-out size × device pixel ratio, Lighthouse-style: green within 10%, red beyond 50% — with the estimated wasted decoded memory and the exact dimensions you should serve instead.
- **Catch upscaled (blurry) sources too** — a 50px thumbnail stretched into a 300pt hero gets flagged the other way.
- **Track decoded memory** — estimated decoded-bitmap bytes per image and totaled across mounted images, so you catch ballooning before the OOM crash.
- **Diagnose failures** — every `onError` is captured with the error message; on iOS, RN core also gives you the HTTP status code and response headers.
- **Clear caches** — expo-image memory and disk caches, one tap from the detail view.

## How it works

React Native ships an official (if `unstable_`-prefixed) hook that lets a devtool wrap every `<Image>` in the app — the Images tool uses it to observe sources and attach load/progress/error handlers app-wide, with zero native code. expo-image's component is instrumented the same way through a render-level patch that's immune to import order. Both paths force-attach the load events the libraries already emit natively, so capture works identically in Expo Go, dev clients, and release builds.

Buoy's own UI (like the thumbnails inside the tool) is excluded from capture — the tool never appears in its own registry.

## Coverage notes

- Byte counts (`loaded/total`) come from image `onProgress` events: reliable on iOS RN core and expo-image; RN core on Android (Fresco) doesn't report real byte counts.
- Cache classification for RN core images uses `Image.queryCache`, which reflects the cache *after* the load — combined with whether network progress events fired, the verdict distinguishes a fresh download from a cache hit.
- Decoded-memory numbers are estimates (`width × height × 4` bytes) — the same math the platforms use for RGBA bitmaps.

## What's Next

Cache explorer (browse the actual disk cache directories with sizes and ages), per-screen waste reports, an X-ray overlay mode (badges on every on-screen image), desktop dashboard mirroring, and MCP tools so your AI agent can audit a screen's images in one call.
