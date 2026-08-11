---
title: Installation
id: flutter-installation
description: "Step-by-step guide to installing Buoy devtools in a Flutter app — requirements, umbrella or à-la-carte packages, and picking your first tools."
---

Get Buoy running in your Flutter app in minutes.

## Requirements

<!-- ::flutter-requirements -->

## Quick Start

Install the umbrella (whole suite) or pick individual tools:

<!-- ::flutter-quick-install -->

## Available Packages

Each package adds a new tool to your floating menu. Install only what you need — or use `buoy` for everything.

<!-- ::flutter-tool-packages -->

## Register Your License Key

A license key unlocks Pro features (production builds and MCP). Pass it as a prop to `BuoyDevTools`:

```dart
import 'package:buoy/buoy.dart';

MaterialApp(
  builder: (context, child) => BuoyDevTools(
    licenseKey: 'YOUR_LICENSE_KEY',
    child: child ?? const SizedBox.shrink(),
  ),
)
```

Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

## Desktop & AI (optional)

The packages above power the in-app floating menu. Two more surfaces connect to the same app — both Buoy Pro for MCP:

- **Buoy Desktop** — a full dashboard for macOS, Windows & Linux. [Download it](https://buoy.gg/pricing) and launch it — simulators/emulators find it automatically. See [Buoy Desktop](../desktop).
- **AI / MCP Server** — drive your app from Claude Code, Cursor, or any MCP editor:

```bash
npx -y @buoy-gg/mcp@latest init
```

See [AI / MCP Server](../mcp) for the full setup.

## Devices

- **iOS Simulator / Android Emulator** — connects to Buoy Desktop automatically (`localhost` / `10.0.2.2`).
- **Physical devices** — pass your computer's LAN IP via the widget: `BuoyDevTools(socketUrl: 'http://192.168.1.x:42831', …)`. iOS will show the Local Network permission prompt on first connect — tap Allow.

## Dart Support

All packages are pure Dart (no pods, no gradle edits, no FFI). Null-safe APIs and pub.dev docs ship with every package.

## Monorepos & Enterprise Setups

Buoy is built to survive locked-down Flutter apps:

- **After adding a new `buoy_*` package, do a full restart** — hot reload won't pick up new plugin/package registrations. Stop the app and run again.
- **Physical devices need a LAN `socketUrl`** — unlike React Native (Metro-derived broker), Flutter apps on a phone should pass `BuoyDevTools(socketUrl: 'http://<your-lan-ip>:42831')`.
- **Same broker as React Native** — Flutter and RN devices can sit side by side on one Desktop / MCP session.
- **No on-device UI for end users** — omit `BuoyDevTools` (or gate it behind a role check) for builds where only the desktop dashboard should see the session. See [Quick Start](./quick-start#control-who-sees-devtools).

## Not on Flutter (yet)

React Query, Redux, Zustand, render highlighting, debug borders, and JS Top remain **React Native only**. Coming soon for Flutter: benchmarking/batch reports for Perf Monitor, and Dart Top. [Vote on the roadmap](https://buoy.gg/roadmap).

## Next Steps

- [Quick Start](./quick-start) — Basic setup guide
- [BuoyDevTools](./buoy-devtools) — Core widget reference
- [Buoy Desktop](../desktop) — The full desktop dashboard
- [AI / MCP Server](../mcp) — Drive your app from your AI editor
- [Custom Tools](./custom-tools) — Build your own debugging tools
