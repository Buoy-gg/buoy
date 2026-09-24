---
title: Quick Start
seoTitle: "Flutter DevTools Setup — install Buoy in minutes"
id: flutter-quick-start
description: "Get Buoy's floating in-app devtools menu running in your Flutter app in minutes, then reach the same tools from desktop or an AI agent."
---

Open Buoy in a Flutter debug build, then inspect a request. You need a Free or Pro account key. Profile and release builds do not show the widget or start its tools.

## 1. Install the core

```bash
flutter pub add buoy
```

The `buoy` umbrella pulls in the whole suite. Prefer à la carte? See [Installation](./installation).

## 2. Add to your app

Wrap your app via `MaterialApp.builder` (or `CupertinoApp.builder`):

```dart
import 'package:flutter/material.dart';
import 'package:buoy/buoy.dart';

MaterialApp(
  builder: (context, child) => BuoyDevTools(
    deviceName: 'My App',
    licenseKey: const String.fromEnvironment('BUOY_KEY'),
    child: child ?? const SizedBox.shrink(),
  ),
)
```

A floating button appears in the corner of your app. Tap it to open the menu.

Get a key from your Buoy account and run `flutter run --dart-define=BUOY_KEY=YOUR_LICENSE_KEY`. See [Installation](./installation) for a complete app example and SDK requirements.

## 3. Add tools

The umbrella registers its bundled Flutter tools. Perform an action that makes an HTTP request, then open Network and select the new row. Check the URL and response. If no row appears, confirm that the action made a fresh request and uses a supported client.

Individual packages require explicit registration. Follow the standalone Network example in [Installation](./installation#available-packages).

### Riverpod providers

If you use Riverpod, add the Buoy observer to your `ProviderScope`:

```dart
import 'package:flutter/widgets.dart';
import 'package:flutter_riverpod/flutter_riverpod.dart';
import 'package:buoy_riverpod/buoy_riverpod.dart';

void main() {
  registerBuoyRiverpod();
  runApp(
    ProviderScope(
      observers: const [buoyRiverpodObserver],
      child: const MyApp(),
    ),
  );
}
```

The observer reports provider changes to the Riverpod tool. See [Riverpod](./tools/riverpod) for package requirements and complete integration.

### go_router

If you use go_router, pass your router once at registration:

```dart
import 'package:buoy_routes/buoy_routes.dart';

registerBuoyRoutes(router: myGoRouter);
```

## Available tools

<!-- ::flutter-tools-table -->

Use the umbrella for the bundled suite, or register a smaller set of individual packages.

## Control who sees devtools

Mount `BuoyDevTools` only when your app allows the current tester to inspect its data. Use your existing authorization checks. Omitting the widget also omits the initialization and connection it manages; it is not a desktop-only mode.

## Take it further

You can also connect this debug build to Desktop or MCP:

- **[Buoy Desktop](../desktop)** — mirror every tool to a full dashboard on macOS, Windows, or Linux, with a live performance HUD and multi-device switching.
- **[AI / MCP Server](../mcp)** — let Claude Code, Cursor, or any MCP editor inspect and control your running app. One command to wire it up:

```bash
npx -y @buoy-gg/mcp@latest init
```

Buoy Desktop is free to use; the MCP server is a Pro feature. Both connect to the same app you just set up — Flutter devices appear next to React Native ones.

## What's next

- [BuoyDevTools](./buoy-devtools) — Core widget reference
- [Buoy Desktop](../desktop) — The full desktop dashboard
- [AI / MCP Server](../mcp) — Drive your app from your AI editor
- [Custom Tools](./custom-tools) — Build your own debugging tools

---

## FAQ

### How do I add Buoy devtools to a Flutter app?

Run `flutter pub add buoy`, then wrap your app in `BuoyDevTools` via `MaterialApp.builder`. A floating button appears in the corner — tap it to open the menu. With the umbrella install, every Flutter tool is already registered.

### Do I need a license key to try it?

Use a Free or Pro account key. Pro enables paid capabilities such as MCP, but does not enable the Flutter widget in profile or release mode.
