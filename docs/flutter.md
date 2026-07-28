---
title: Flutter (Beta)
id: flutter
description: "Buoy for Flutter — a full in-app devtools suite (network, storage, console, env, routes, images, Riverpod, events, impersonate, image overlay, perf HUD) streaming live to Buoy Desktop and your AI editor. One install: flutter pub add buoy."
---

# Buoy for Flutter (Beta)

Buoy is now a **full devtools suite for Flutter**. Eleven tools ship today — each device-verified — with the same three surfaces you get on React Native:

1. **In your app** — a floating devtools bubble with every installed tool.
2. **On your desktop** — live in the [Buoy Desktop](https://buoy.gg) dashboard, side by side with your React Native devices.
3. **In your AI editor** — the Buoy MCP server reads and drives your Flutter app from Claude Code or Cursor.

It's the same broker, the same protocol, and the same MCP tools as React Native — a Flutter device just shows up alongside your RN devices.

## Install

```sh
flutter pub add buoy
```

The `buoy` umbrella package pulls in the whole suite. Prefer to add tools à la carte? Every tool is its own package (see the [package table](#packages) below).

## Wire it up

One widget — wrap your app via `MaterialApp.builder`:

```dart
import 'package:buoy/buoy.dart';

MaterialApp(
  builder: (context, child) => BuoyDevTools(
    deviceName: 'My App',
    child: child ?? const SizedBox.shrink(),
  ),
)
```

That's the whole setup. Every installed Buoy tool self-registers on mount: capture, the floating in-app menu, the live desktop connection, and the MCP server. Optional props: `licenseKey` for Pro, `socketUrl` for physical devices, `deviceId` to pin the device identity, and `tools` for your own custom tools.

A few tools take **one extra line** to opt a widget, provider, or your entry point in — noted on each tool's page.

## Devices

- **iOS Simulator / Android Emulator** — connects to Buoy Desktop automatically (`localhost` / `10.0.2.2`).
- **Physical devices** — pass your computer's LAN IP via the widget: `BuoyDevTools(socketUrl: 'http://192.168.1.x:42831', …)`. iOS will show the Local Network permission prompt on first connect — tap Allow.

## Tools

Eleven tools ship for Flutter today, each device-verified and each with its own page — same layout as the React Native docs:

| Tool | What it does |
| --- | --- |
| [Network Monitor](./flutter/tools/network) | Every HTTP request — `package:http`, dio, image loads, GraphQL — with headers, bodies, timing, and errors |
| [Storage Explorer](./flutter/tools/storage) | Browse and edit every persisted key live, with an event stream of every write |
| [Console](./flutter/tools/console) | Every `print`, `debugPrint`, and `log` call in a DevTools-style panel, in any build |
| [Environment Inspector](./flutter/tools/env) | The config values your app actually resolved, validated with a health score |
| [Route Inspector](./flutter/tools/routes) | Your go_router sitemap and live navigation stack — tap any route to jump there |
| [Images](./flutter/tools/images) | Every image load with cache verdicts, oversize audits, and failure diagnosis |
| [Impersonate Tool](./flutter/tools/impersonate) | Inject impersonation headers to test as any user — no logout, no rebuild |
| [Image Overlay](./flutter/tools/image-overlay) | Pin a design mockup over the running app for pixel-perfect UI |
| [Events Timeline](./flutter/tools/events) | Network, storage, state, and navigation merged into one LLM-ready stream |
| [Riverpod Inspector](./flutter/tools/riverpod) | Every provider's live value and each state change, with diffs |
| [Perf Monitor](./flutter/tools/perf-monitor) | A live on-device HUD with FPS, jank, CPU, and memory |

## Desktop & MCP

Flutter devices speak the **same protocol** as React Native, so nothing is Flutter-specific here:

- **Buoy Desktop** shows your Flutter device next to your RN devices with the same panels and dashboards.
- **The MCP server** (`@buoy-gg/mcp`) reads and drives your Flutter app with the same tools — `list_devices`, `get_events` / `get_snapshot`, `navigate`, `describe_screen`, and more.

## Packages

All packages are `0.3.0` on [pub.dev](https://pub.dev/packages/buoy). The `buoy` umbrella pulls in the rest.

| Package | What it is |
| --- | --- |
| `buoy` | Umbrella — one install pulls in the whole suite |
| `buoy_core` | The floating menu + broker/desktop/MCP wiring |
| `buoy_shared_ui` | Shared UI primitives used by the tools |
| `buoy_network` | Network inspector |
| `buoy_storage` | Storage explorer |
| `buoy_console` | Console |
| `buoy_env` | Environment inspector |
| `buoy_routes` | Routes (go_router) |
| `buoy_images` | Images |
| `buoy_impersonate` | Impersonate |
| `buoy_image_overlay` | Image overlay |
| `buoy_events` | Events timeline |
| `buoy_riverpod` | Riverpod state inspector |
| `buoy_perf_monitor` | Perf monitor (live HUD) |

## Not on Flutter (yet)

To be exact about coverage: the React Query devtools, Redux and Zustand inspectors, render highlighting, debug borders, and JS Top are **React Native only** and don't ship for Flutter. React Native remains Buoy's primary, most complete platform.

## Beta notes

- Package versions are `0.x` — APIs may shift between minor versions.
- Source is visible on [GitHub](https://github.com/Buoy-gg/Buoy-Flutter). Proprietary license — free in development, [Pro](https://buoy.gg/pricing) unlocks production and MCP, same as React Native.
- Coming soon: benchmarking/batch reports for the perf monitor, and Dart Top (a thread-time profiler, the Flutter analog of JS Top). [Vote on the roadmap](https://buoy.gg/roadmap).
