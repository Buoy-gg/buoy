---
title: Console
seoTitle: "Flutter Console — DevTools-style logs on-device in debug builds"
id: flutter-tools-console
description: "Capture every print, debugPrint, and log call from your Flutter app in a Chrome DevTools-style panel — filterable, on-device in debug builds."
---

A Chrome-DevTools-style console for your Flutter app. Buoy Console captures logs captured through the configured hooks — plus `FlutterError` reports and uncaught async errors — and shows them in a familiar, filterable panel: on your phone, on the desktop dashboard, or through your AI agent.

The demo shows the React Native tool with mock data. Use the Flutter setup and feature descriptions below for supported behavior; the demo does not establish Flutter feature parity.

<!-- ::console-live-demo -->

## Installation

<!-- ::pub package="buoy_console" -->

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

### Crashes don't disappear

An uncaught error normally takes the desktop connection down with it: the throttled snapshot never fires, and the dashboard just shows an app that stopped answering. Buoy pushes a crash entry out immediately instead, while the connection is still alive, so the app's last words are readable from the desktop dashboard and from your AI agent (`get_triage` leads with them).

Crash entries are tagged so you can tell what actually happened:

| Tag | Source | Meaning |
| --- | --- | --- |
| `[UNCAUGHT]` | `PlatformDispatcher.onError`, guarded zone | Nothing in your code handled this error. |
| `[RENDER ERROR]` | `FlutterError.onError` | A framework/build error. Flutter fires this for errors an `ErrorWidget` then recovers from, so it is reported without claiming your app crashed. |

Duplicate reports of the same error are deduplicated; separate error occurrences remain separate entries.

---

## What You Can Do

- **See every log, live** — logs, warnings, and errors stream in as they happen, color-coded by level.
- **Filter by level** — Focus on just errors and warnings when you're chasing a bug.
- **Search** — Filter messages by substring to find the exact log you care about.
- **Read debug-build logs** — Keep `BuoyDevTools` mounted in debug mode to inspect logs on-device or through Desktop. The widget does not enable this setup in profile or release mode.
- **Expand structured data** — Objects and lists are formatted and expandable, just like the browser console.

---

## Read the console from your AI

With the [MCP server](../../mcp), an AI agent can read the console tail directly with `get_console` — filtering by minimum level or message substring to pull just the errors it needs while debugging.

---

## What's Next

- [Events Timeline](./events) — Console logs alongside network, state, and route events
- [Network Monitor](./network) — Inspect the requests behind an error
- [AI / MCP Server](../../mcp) — Let an agent read the console for you

---

## FAQ

### How do I read print and debugPrint output without a debugger attached?

Wrap your entry point in `BuoyConsole.runZoned` — a `Zone` is the only way to observe `print` in Dart — and logs captured through the configured hooks, plus `FlutterError` reports and uncaught async errors, appears in the on-device panel of a debug build.

### Can I read the logs from a crash?

Yes. An uncaught error normally takes the desktop connection down before the throttled snapshot fires; Buoy pushes a crash entry out immediately while the connection is alive, tagged `[UNCAUGHT]` or `[RENDER ERROR]`, so the app's last words are readable from the dashboard and from an AI agent.
