---
title: BuoyDevTools
seoTitle: "BuoyDevTools API — Flutter floating devtools menu, props & tool registration"
id: flutter-buoy-devtools
description: "API reference for BuoyDevTools, the draggable floating button that opens Buoy's in-app debugging menu in your Flutter app — the Flutter analog of React Native's FloatingDevTools."
---

The `BuoyDevTools` widget is the entry point for Buoy on Flutter — the analog of React Native's `FloatingDevTools`. It wraps your app and renders a draggable floating button that opens a menu containing all your registered debugging tools.

<!-- ::floating-menu-live-demo-flutter -->

## Basic Usage

```dart
import 'package:buoy/buoy.dart';

MaterialApp(
  builder: (context, child) => BuoyDevTools(
    licenseKey: 'YOUR_LICENSE_KEY',
    child: child ?? const SizedBox.shrink(),
  ),
)
```

That's it. With the [`buoy` umbrella](./installation), every first-party tool you've installed self-registers on mount and appears in the menu. Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

Mount it from `MaterialApp.builder` (or `MaterialApp.router`) so it sits above your Navigator and survives every route change.

## Props

| Prop | Type | Default | Notes |
|------|------|---------|-------|
| `child` | `Widget` | required | Your app (usually the `MaterialApp` builder child) |
| `licenseKey` | `String?` | `null` | Unlocks Pro (production + MCP) |
| `deviceName` | `String` | `'Flutter App'` | Label in Buoy Desktop / MCP |
| `deviceId` | `String?` | auto | Pin a stable device identity |
| `socketUrl` | `String?` | auto | LAN broker URL for physical devices |
| `tools` | `List<BuoyTool>` | `[]` | Extra custom tools beyond self-registered ones |

## How Tools Register

React Native discovers tools by optional-require; Dart has no equivalent, so Flutter tools register explicitly — but with the umbrella package you never write the call yourself:

- **Umbrella** — add a tool package, and `package:buoy` calls its `registerBuoyX()` for you when `BuoyDevTools` mounts. Install and it shows up:

  ```bash
  flutter pub add buoy
  ```

- **À la carte** — depend on the individual packages instead, call `registerBuoyNetwork()` (etc.) yourself before `runApp`, and mount `BuoyDevTools` from `buoy_core`.

A few tools still need one app-owned hook — the router for Routes, a `ProviderScope` observer for Riverpod, `BuoyImage` for Images, a search callback for Impersonate. The umbrella can't supply those, so each tool page notes its one line.

## Custom Tools

Need something specific to your app? Pass your own `BuoyTool`s via the `tools` prop (or `Buoy.registerTool` before mount):

```dart
import 'package:buoy/buoy.dart';
import 'package:flutter/material.dart';

MaterialApp(
  builder: (context, child) => BuoyDevTools(
    tools: [
      BuoyTool(
        id: 'flags',
        name: 'Flags',
        color: const Color(0xFF34D399),
        icon: (size, color) => Icon(Icons.flag, size: size, color: color),
        screenBuilder: (context) => const FeatureFlagDebugger(),
      ),
    ],
    child: child ?? const SizedBox.shrink(),
  ),
)
```

See [Custom Tools](./custom-tools) for the full schema and desktop-sync adapters.

## Draggable Button

The floating button can be dragged anywhere on screen. It remembers its position between sessions, so it stays where your team likes it, and it tucks out of the way on its own while the dial or a tool is open. Tools you minimize dock above it and come back with a tap.

## Beyond the in-app menu

`BuoyDevTools` is also the source of truth for Buoy's other surfaces. The same tools you see in the menu sync out over a local broker, so once this is set up you can — with no extra code — also:

- open the [Buoy Desktop](../desktop) dashboard and inspect the same live app on a full screen, or
- point an AI agent at your app with the [MCP server](../mcp).

Simulators and emulators connect automatically. Physical devices take one `socketUrl` pointing at your machine:

```dart
BuoyDevTools(
  socketUrl: 'http://192.168.1.20:42831',
  child: child ?? const SizedBox.shrink(),
)
```

The current sync target and connection state show up in the menu's Settings tab under desktop sync.

## Debug builds only

`BuoyDevTools` renders your child and nothing else outside a debug build — no button, no dial, no sync, no capture. There is no flag to change that on Flutter yet, so shipping the widget in your release build is safe by construction.

Two React Native options have no Flutter counterpart today:

- **`environment`** — the RN floating button prints an environment badge (`local`, `dev`, `staging`, `qa`, `prod`). The Flutter bubble doesn't.
- **`headless` and release-build sync** — RN can mount sync-only with no on-device UI, and can opt a release build into sync with `externalSync.enableInRelease` plus a Pro license. Flutter's debug-only gate means neither applies yet.

Both are on the Flutter roadmap.

## Next Steps

- [Custom Tools](./custom-tools) — Build team-specific debugging tools
- [Buoy Desktop](../desktop) — Inspect the same app on a full dashboard
- [AI / MCP Server](../mcp) — Drive your app from your AI editor
- [Quick Start](./quick-start) — Full setup walkthrough
