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

A few tools take **one extra line** to opt a widget, provider, or your entry point in — noted in each tool's section below.

## Devices

- **iOS Simulator / Android Emulator** — connects to Buoy Desktop automatically (`localhost` / `10.0.2.2`).
- **Physical devices** — pass your computer's LAN IP via the widget: `BuoyDevTools(socketUrl: 'http://192.168.1.x:42831', …)`. iOS will show the Local Network permission prompt on first connect — tap Allow.

## Tools

### Network

Every HTTP request in one hook — no per-client setup. Captures `package:http`, dio, `Image.network`/`NetworkImage`, `cached_network_image`, and GraphQL clients, with headers, bodies, timing, status, and errors.

Everything riding `dart:io`'s `HttpClient` is captured automatically:

| Captured | Notes |
| --- | --- |
| `package:http` | default `IOClient` |
| dio | attributed as `dio` in the panel |
| `Image.network` / `NetworkImage` | Flutter's own image loading |
| `cached_network_image` | cache misses / revalidations |
| graphql_flutter / ferry | tag with `X-Request-Client: graphql` for operation names |

Known gaps (documented): `cupertino_http` / `cronet_http` native clients, gRPC (raw sockets), secondary isolates, Flutter web.

### Storage

Browse and edit every key your app persists (shared_preferences and friends) live, with an event stream of every write and diffs of what changed.

### Console

Every `print`, `debugPrint`, and `log` call in a filterable DevTools-style panel, on the device, in any build.

> **Flutter note:** to catch logs emitted before your first frame, wrap your entry point — run `runApp` inside the Buoy console zone in `main()` so capture starts before any log fires.

### Env

The config values your app actually resolved, validated against the types and shapes you expect, with a health score.

### Routes

Your [go_router](https://pub.dev/packages/go_router) sitemap and live navigation stack with params — tap any route to jump straight to it.

> **Flutter note:** register your router once so Buoy can read the sitemap and drive navigation: `registerBuoyRoutes(router: yourRouter)`.

### Images

Every image load with cache verdicts and oversize audits, so slow and stale images stop hiding.

> **Flutter note:** wrap the widgets you want tracked in `BuoyImage(...)` (a thin wrapper over your image widget).

### Impersonate

Inject impersonation headers into every outgoing request to test as any user — no logout, no rebuild. Your backend decides what the headers mean.

### Image Overlay

Pin a design mockup over the running app to nail pixel-perfect UI — free placement, or locked to a specific component.

> **Flutter note:** wrap a widget in `BuoyImageTarget(...)` to make it selectable for component-match mode.

### Events Timeline

Network, storage, state, and navigation activity merged into one chronological, LLM-ready stream — export it and hand an AI the full story of what happened.

### Riverpod Inspector

Watch every provider's live value and each state change over time — the Flutter analog of the React Native Jotai atom inspector.

> **Flutter note:** add the Buoy observer to your `ProviderScope`: `ProviderScope(observers: [BuoyProviderObserver()], child: ...)`.

### Perf Monitor

A live on-device HUD showing UI/JS FPS, CPU, and memory while you use the app, streamed to the desktop.

> Benchmarking and batch reports (recorded, comparable runs) are **coming soon** on Flutter — today the perf tool is a live HUD with live metrics.

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
