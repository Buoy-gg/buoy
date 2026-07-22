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

That's the whole setup. Every installed Buoy tool self-registers on mount: HTTP capture (`HttpOverrides`), the floating in-app menu, the live desktop connection, and the MCP server. Optional props: `licenseKey` for Pro, `socketUrl` for physical devices, `deviceId` to pin the device identity, and `tools` for your own custom tools.

## Devices

- **iOS Simulator / Android Emulator** — connects to Buoy Desktop automatically (`localhost` / `10.0.2.2`).
- **Physical devices** — pass your computer's LAN IP via the widget: `BuoyDevTools(socketUrl: 'http://192.168.1.x:42831', …)`. iOS will show the Local Network permission prompt on first connect — tap Allow.

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
