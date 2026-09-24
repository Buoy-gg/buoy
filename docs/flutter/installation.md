---
title: Installation
seoTitle: "Install Flutter DevTools — buoy pub package setup guide"
id: flutter-installation
description: "Step-by-step guide to installing Buoy devtools in a Flutter app — requirements, umbrella or à-la-carte packages, and picking your first tools."
---

Install Buoy in an existing Flutter app and open its floating tool menu. The `BuoyDevTools` widget runs in debug mode; in profile and release builds it returns only your app's child widget.

## Requirements

The package manifests require Dart `^3.9.0` and Flutter `>=3.27.0`. Use a Flutter SDK that includes Dart 3.9 or a compatible newer Dart 3 release. Check your installed versions with:

```bash
flutter --version
```

## Quick Start

For the full suite, run this from your Flutter app's directory:

```bash
flutter pub add buoy
```

Wrap your app through `MaterialApp.builder`. A complete minimal `lib/main.dart` looks like this:

```dart
import 'package:flutter/material.dart';
import 'package:buoy/buoy.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      builder: (context, child) => BuoyDevTools(
        licenseKey: const String.fromEnvironment('BUOY_KEY'),
        child: child ?? const SizedBox.shrink(),
      ),
      home: const Scaffold(
        body: Center(child: Text('Open the Buoy menu to inspect this app.')),
      ),
    );
  }
}
```

Get your account key as described below, then start a debug build:

```bash
flutter run --dart-define=BUOY_KEY=YOUR_LICENSE_KEY
```

Tap the floating button. Confirm that the menu opens and includes Network. To check capture, perform an action in your app that makes a new HTTP request, then open Network and inspect it. The minimal app above opens the menu but does not make a request.

## Available Packages

The `buoy` umbrella registers its bundled tools. Some tools still need app-specific setup, such as connecting a router or supplying environment values. Their pages describe those steps.

<!-- ::flutter-tool-packages -->

To install only Network and the core widget:

```bash
flutter pub add buoy_core buoy_network
```

Use these imports in place of the umbrella import:

```dart
import 'package:buoy_core/buoy_core.dart';
import 'package:buoy_network/buoy_network.dart' show registerBuoyNetwork;
```

Register Network before `runApp`; keep the `MyApp` widget from the example above:

```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized();
  registerBuoyNetwork();
  runApp(const MyApp());
}
```

Individual tool packages need their registration calls. Adding a dependency alone does not register it with the core widget.

## Register Your License Key

Use a Free or Pro Buoy account key. Visit [pricing](https://buoy.gg/pricing) for account options and plan limits. Pass the key through `--dart-define=BUOY_KEY=...` as shown above; the widget reads it with `String.fromEnvironment`.

If the widget shows account setup, confirm that the build received the key and complete the prompt. A Pro key does not enable this widget in profile or release mode.

## Desktop & AI (optional)

- [Buoy Desktop](../desktop) provides a free desktop dashboard for connected apps.
- [AI / MCP Server](../mcp) lets Claude Code, Cursor, or another MCP editor inspect and control your app. MCP requires Pro.

For MCP configuration, run:

```bash
npx -y @buoy-gg/mcp@latest init
```

Keep the Flutter app running in debug mode with `BuoyDevTools` mounted. Follow the connection guide for your chosen surface.

## Devices

- iOS Simulator uses `localhost`; Android Emulator uses `10.0.2.2` to reach the computer running Desktop or the MCP broker.
- On a physical device, set `socketUrl` to your computer's LAN address, for example `BuoyDevTools(socketUrl: 'http://192.168.1.20:42831', child: child)`. Replace the address with your computer's address and keep the device on a network that can reach it. Allow local network access if iOS asks.

If the device does not appear, confirm that the broker is running, the address is correct, and the network or firewall allows the connection.

## Dart Support

Buoy's Flutter implementation is Dart. Its dependencies may include Flutter plugins, so follow any platform setup required by the packages you install.

## Monorepos & Enterprise Setups

After adding packages or changing tool registration, stop and restart the app. For individual packages, call each tool's registration function before using it.

Flutter and React Native devices can connect to the same Desktop or MCP broker. Physical Flutter devices need an explicit reachable `socketUrl`.

Keep `BuoyDevTools` mounted for the setup on this page. Removing it also removes the initialization and connection it manages; this guide does not provide a separate headless setup.

## Not on Flutter (yet)

React Query, Redux, Zustand, render highlighting, debug borders, and JS Top are React Native tools. Check each Flutter tool page for supported features; a matching tool name does not imply complete feature parity. See the [roadmap](https://buoy.gg/roadmap) for proposed additions.

## Next Steps

- [Quick Start](./quick-start): Flutter setup and usage
- [BuoyDevTools](./buoy-devtools): widget reference
- [Buoy Desktop](../desktop): desktop connection setup
- [AI / MCP Server](../mcp): connect your AI editor
- [Custom Tools](./custom-tools): add an app-specific tool

## FAQ

### What's the difference between the `buoy` umbrella and the individual packages?

`buoy` imports and registers its bundled tools. Individual packages let you choose a smaller set, with explicit registration for each tool. Both use a `BuoyDevTools` widget, but the umbrella exports its own wrapper around the core widget.

### Where do I mount BuoyDevTools in a Flutter app?

Use `MaterialApp.builder` or `CupertinoApp.builder` to wrap the child so the menu appears above your screens. Keep it inside any providers needed by your tools.
