---
title: Overview
seoTitle: "Buoy for Flutter — in-app devtools for Flutter apps"
id: flutter-overview
description: "Meet Buoy for Flutter — in-app devtools for network, state, storage, and performance from your phone, desktop, or AI agent."
---

Buoy provides tools for inspecting Flutter apps: network requests, storage, state, navigation, and performance. Use the in-app menu or connect the app to [Buoy Desktop](../desktop). An AI editor can access supported tools through the [MCP server](../mcp) with Pro.

Flutter support is in beta. `BuoyDevTools` runs in debug mode; it does not enable tools in profile or release builds.

## Who It's For

Developers can inspect a failed request alongside the app state that produced it. QA can use configured tools to edit test data and exercise error states. Support teams can collect debugging context when your app grants them access.

Some tasks need app integration. Impersonation requires your backend to authorize the selected user, and state tools need access to the stores or providers you want to inspect. Buoy does not bypass your app's authentication.

## What You Get

| Tool | What It Does |
|------|--------------|
| **Network** | Inspect supported HTTP requests — `package:http`, dio, image loads, GraphQL |
| **Storage** | Browse and edit `shared_preferences` in real-time |
| **Environment** | Validate env/config with type checking and a health score |
| **Console** | A Chrome-DevTools-style console for every `print` / `debugPrint` / `log` |
| **Perf Monitor** | Live on-device HUD — FPS, jank, CPU, and memory |
| **Images** | Captured image loads with cache verdict, timing, oversize audit & failure diagnosis |
| **Riverpod** | Inspect provider state, live values, diffs, and history |
| **Route Inspector** | Track go_router navigation and browse your route structure |
| **Events Timeline** | Unified timeline across all tools with LLM-ready export |
| **Impersonate** | Inject configured headers for backend-authorized impersonation |
| **Image Overlay** | Overlay design mockups on your app for pixel-perfect comparison |

## Why Buoy

Install the tools you need and inspect the running app without adding a separate debug screen for each task. Desktop gives those tools more screen space, and MCP exposes supported actions to your AI editor. Follow each tool's setup instructions for app-specific configuration.

## Quick Start

```dart
import 'package:buoy/buoy.dart';

MaterialApp(
  builder: (context, child) => BuoyDevTools(
    licenseKey: 'YOUR_LICENSE_KEY',
    child: child ?? const SizedBox.shrink(),
  ),
)
```

The umbrella registers its bundled tools. Individual packages require explicit registration. Get your license key at [buoy.gg/pricing](https://buoy.gg/pricing).

## Build Your Own Tools

Need something specific to your app? Add custom tools via the `tools` prop on `BuoyDevTools`. Build internal debugging utilities, feature flag toggles, or team-specific inspectors that integrate with the floating menu.

## Next Steps

- [Installation](./installation) — Add Buoy to your project
- [Quick Start](./quick-start) — Install and inspect your first request
- [AI / MCP Server](../mcp) — Let AI agents drive your running app
- [Custom Tools](./custom-tools) — Build your own debugging tools
- [Tools Reference](./tools/network) — Detailed docs for each tool
