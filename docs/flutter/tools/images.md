---
title: Images
id: flutter-tools-images
description: "Debug every image in your Flutter app — a live registry of loads with cache verdicts (memory/disk/network), timings, oversize + wasted-memory auditing, and a failure log."
---

Images not loading? Loading slow? Stale avatars that never update? Memory ballooning? Image HTTP traffic in Flutter is fetched by `dart:io` image loaders and never surfaces the layout size or cache origin a network inspector needs — this tool is the visibility layer.

Every image loaded through `BuoyImage` appears in a live registry with where it came from (memory cache / disk cache / network), how long it took, how big it decoded versus how big it displayed, and exactly why it failed.

## Installation

```sh
flutter pub add buoy_images
```

Flutter has no app-wide `Image` decorator hook, so capture is opt-in at the widget level — use `BuoyImage` in place of `Image` / `CachedNetworkImage`:

```dart
import 'package:buoy_images/buoy_images.dart';

BuoyImage(
  provider: CachedNetworkImageProvider(url), // or NetworkImage(url), AssetImage(...)
  width: 120,
  height: 120,
)
```

It wraps your `ImageProvider`, measures the rendered box for the oversize audit, and owns the props so reload/retry and simulations work.

---

## What You Can Do

- **See every image load, live** — thumbnail, source URL, load time, decoded dimensions, status. Tap for the full detail view.
- **Get cache verdicts per load** — memory, disk, or network.
- **Catch oversized sources** — decoded pixels compared against the laid-out size × device pixel ratio, Lighthouse-style, with the estimated wasted decoded memory and the exact dimensions you should serve instead.
- **Track decoded memory** — estimated decoded-bitmap bytes per image and totaled, so you catch ballooning before the OOM crash.
- **Diagnose failures** — every failure captured with the error message and the HTTP status where available.
- **Reproduce image bugs** — per-image hard reload / retry, plus simulation overrides: force error / loading / blank / URL swap, and app-wide offline / cold-start / blank-images modes.

---

## Desktop & AI

The same live registry streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) — the full tool (list, detail, simulations, mass actions) on a big screen. And with the [MCP server](../../mcp), an agent can list every load with `get_images`, reload or retry with `image_action`, and flip failure simulations with `set_image_simulation`.

---

## What's Next

- [Network Monitor](./network) — The rest of your HTTP traffic
- [Image Overlay](./image-overlay) — Pin a mockup over the app for pixel-perfect UI
- [AI / MCP Server](../../mcp) — Let an agent audit a screen's images
