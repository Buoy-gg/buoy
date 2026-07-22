---
title: Flutter (Beta)
id: flutter
description: "Buoy for Flutter — capture every HTTP request your Flutter app makes and stream it live to Buoy Desktop and your AI editor. Network inspector in beta, more tools on the way."
---

# Buoy for Flutter (Beta)

Buoy now works in Flutter apps. The **network inspector** ships first — the same three surfaces you get on React Native:

1. **In your app** — a floating devtools bubble with the network panel.
2. **On your desktop** — live traffic in the [Buoy Desktop](https://buoy.gg) Network panel, side by side with your React Native devices.
3. **In your AI editor** — the Buoy MCP server can read your Flutter app's traffic (`get_snapshot` with `toolId: "network"`).

More tools are coming from the React Native suite one by one — [vote for what's next](https://buoy.gg/roadmap).

## Install

```sh
flutter pub add buoy
```

Or just the network tool: `flutter pub add buoy_network`.

## Wire it up

```dart
import 'package:buoy/buoy.dart';
import 'package:flutter/foundation.dart';

void main() {
  if (kDebugMode) {
    // Capture ALL dart:io HTTP traffic (package:http, dio, Image.network).
    BuoyHttpOverrides.install();

    // Stream to Buoy Desktop (auto-connects on simulators/emulators).
    BuoySyncClient(
      deviceName: 'My App',
      deviceId: 'my-app',
      platform: Platform.isIOS ? 'ios' : 'android',
      tools: {'network': networkSyncAdapter},
    ).connect();

    // Keep the in-app panel live even when desktop isn't watching.
    NetworkEventStore.instance.subscribe(() {});
  }
  runApp(const MyApp());
}
```

Mount the in-app panel by wrapping your app with `BuoyDevTools` (via `MaterialApp.builder`) and registering a `BuoyTool` with `NetworkModal` — full wiring in the [package example](https://pub.dev/packages/buoy_network/example).

## Devices

- **iOS Simulator / Android Emulator** — connects to Buoy Desktop automatically (`localhost` / `10.0.2.2`).
- **Physical devices** — pass your computer's LAN IP: `BuoySyncClient(socketUrl: 'http://192.168.1.x:42831', …)`. iOS will show the Local Network permission prompt on first connect — tap Allow.

## What gets captured

Everything riding `dart:io`'s `HttpClient` in one hook — no per-client setup:

| Captured | Notes |
| --- | --- |
| `package:http` | default `IOClient` |
| dio | attributed as `dio` in the panel |
| `Image.network` / `NetworkImage` | Flutter's own image loading |
| `cached_network_image` | cache misses / revalidations |
| graphql_flutter / ferry | tag with `X-Request-Client: graphql` for operation names |

Known gaps (by design, documented): `cupertino_http` / `cronet_http` native clients, gRPC (raw sockets), secondary isolates, Flutter web.

## Beta notes

- Package versions are `0.x` — APIs may shift between minor versions.
- The React Native suite's other tools (console, storage, state inspectors…) are being ported — the [roadmap](https://buoy.gg/roadmap) decides the order.
- Source is visible on [GitHub](https://github.com/Buoy-gg/Buoy-Flutter). Proprietary license — free in development, [Pro](https://buoy.gg/pricing) unlocks production and MCP, same as React Native.
