---
title: Quick Start
id: flutter-quick-start
description: "Get Buoy's floating in-app devtools menu running in your Flutter app in minutes, then reach the same tools from desktop or an AI agent."
---

Get the in-app menu running in under 2 minutes — then reach the same tools from your desktop or your AI agent.

## 1. Install the core

<!-- ::pub package="buoy" -->

The `buoy` umbrella pulls in the whole suite. Prefer à la carte? See [Installation](./installation).

## 2. Add to your app

Wrap your app via `MaterialApp.builder` (or `CupertinoApp.builder`):

```dart
import 'package:buoy/buoy.dart';

MaterialApp(
  builder: (context, child) => BuoyDevTools(
    deviceName: 'My App',
    child: child ?? const SizedBox.shrink(),
  ),
)
```

A floating button appears in the corner of your app. Tap it to open the menu.

Every tool is free in development — no key needed. A `licenseKey` prop unlocks [Pro](https://buoy.gg/pricing): production builds, the MCP server, and unlimited capture.

## 3. Add tools

With the umbrella install, every Flutter tool is already registered. Prefer individual packages? Install any tool — it appears in the menu:

<!-- ::pub package="buoy_network" -->

That's it. Open the menu, tap Network, and you're watching every API call in real-time.

### Riverpod providers

If you use Riverpod, add the Buoy observer to your `ProviderScope`:

```dart
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

No wrappers, no middleware. Providers automatically appear in the Riverpod tool inside your Buoy menu.

### go_router

If you use go_router, pass your router once at registration:

```dart
import 'package:buoy_routes/buoy_routes.dart';

registerBuoyRoutes(router: myGoRouter);
```

## Available tools

<!-- ::flutter-tools-table -->

Install what you need. Skip what you don't. Or keep the umbrella and get them all.

## Control who sees devtools

Only show devtools to specific users — admins, QA, internal team members, or whoever your business needs:

```dart
import 'package:buoy/buoy.dart';

Widget build(BuildContext context) {
  final user = context.watch<AuthCubit>().state.user;

  final showDevTools = user?.role == 'admin' ||
      user?.role == 'qa' ||
      (user?.email.endsWith('@yourcompany.com') ?? false);

  return MaterialApp(
    builder: (context, child) {
      final app = child ?? const SizedBox.shrink();
      if (!showDevTools) return app;
      return BuoyDevTools(
        licenseKey: 'YOUR_LICENSE_KEY',
        child: app,
      );
    },
  );
}
```

Or keep it available for everyone — your QA and support teams will thank you.

## Take it further

The tools you just installed aren't only in the floating menu — reach the same live app two more ways:

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
