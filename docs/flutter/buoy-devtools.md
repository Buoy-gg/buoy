---
title: BuoyDevTools
id: flutter-buoy-devtools
description: "API reference for BuoyDevTools, the floating button that opens Buoy's in-app debugging menu in your Flutter app."
---

The `BuoyDevTools` widget is the entry point for Buoy on Flutter — the analog of React Native's `FloatingDevTools`. It wraps your app, renders a draggable floating button, and opens a menu containing every registered debugging tool.

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

That's it. With the [`buoy` umbrella](./installation), every first-party tool self-registers on mount. Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

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

Unlike React Native's optional-require auto-discovery, Flutter tools register explicitly:

- **Umbrella** — `package:buoy` calls each `registerBuoyX()` when `BuoyDevTools` mounts (debug only).
- **À la carte** — call `registerBuoyNetwork()` (etc.) yourself before `runApp`, then mount `BuoyDevTools` from `buoy_core`.

A few tools still need one app-owned hook (router, `ProviderScope` observer, `BuoyImage`, impersonate search) — noted on each tool page.

## Custom Tools

Pass your own `BuoyTool`s via the `tools` prop (or `Buoy.registerTool` before mount):

```dart
BuoyDevTools(
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
)
```

See [Custom Tools](./custom-tools) for the full schema and desktop-sync adapters.

## Beyond the in-app menu

`BuoyDevTools` is also the source of truth for Buoy's other surfaces. The same tools sync out over a local broker, so once this is set up you can — with no extra code — also:

- open the [Buoy Desktop](../desktop) dashboard and inspect the same live app on a full screen, or
- point an AI agent at your app with the [MCP server](../mcp).

Simulators/emulators connect automatically. Physical devices should pass `socketUrl: 'http://<your-lan-ip>:42831'`. Connection state shows up in the menu's Settings under desktop sync.

## Next Steps

- [Custom Tools](./custom-tools) — Build team-specific debugging tools
- [Buoy Desktop](../desktop) — Inspect the same app on a full dashboard
- [AI / MCP Server](../mcp) — Drive your app from your AI editor
- [Quick Start](./quick-start) — Full setup walkthrough
