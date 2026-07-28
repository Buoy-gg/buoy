---
title: Console
id: flutter-tools-console
description: "Capture every print, debugPrint, and log call from your Flutter app in a Chrome DevTools-style panel — filterable, on-device, in any build."
---

A Chrome-DevTools-style console for your Flutter app. Buoy Console captures every `print`, `debugPrint`, and `log` call — plus `FlutterError` reports and uncaught async errors — and shows them in a familiar, filterable panel: on your phone, on the desktop dashboard, or through your AI agent.

## Installation

```sh
flutter pub add buoy_console
```

To capture `print`, wrap your entry point in `BuoyConsole.runZoned` (a `Zone` is the only way to observe `print` in Dart) — so capture starts before your first log fires:

```dart
import 'package:buoy_console/buoy_console.dart';

void main() {
  BuoyConsole.runZoned(() {
    runApp(const MyApp());
  });
}
```

If you never call `BuoyConsole.runZoned`, call `BuoyConsole.install()` once instead — everything except `print` (`debugPrint`, `FlutterError`, uncaught async errors) is still captured.

---

## What You Can Do

- **See every log, live** — logs, warnings, and errors stream in as they happen, color-coded by level.
- **Filter by level** — Focus on just errors and warnings when you're chasing a bug.
- **Search** — Filter messages by substring to find the exact log you care about.
- **Read production logs** — Because Buoy runs on-device in any build, you can read console output from a release build with no cable and no debugger attached.
- **Expand structured data** — Objects and lists are formatted and expandable, just like the browser console.

---

## Read the console from your AI

With the [MCP server](../../mcp), an AI agent can read the console tail directly with `get_console` — filtering by minimum level or message substring to pull just the errors it needs while debugging.

---

## What's Next

- [Events Timeline](./events) — Console logs alongside network, state, and route events
- [Network Monitor](./network) — Inspect the requests behind an error
- [AI / MCP Server](../../mcp) — Let an agent read the console for you
