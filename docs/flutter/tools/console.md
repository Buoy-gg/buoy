---
title: Console
seoTitle: "Flutter Console — DevTools-style logs on-device, any build"
id: flutter-tools-console
description: "Capture every print, debugPrint, and log call from your Flutter app in a Chrome DevTools-style panel — filterable, on-device, in any build."
---

A Chrome-DevTools-style console for your Flutter app. Buoy Console captures every `print`, `debugPrint`, and `log` call — plus `FlutterError` reports and uncaught async errors — and shows them in a familiar, filterable panel: on your phone, on the desktop dashboard, or through your AI agent.

Don't take our word for it — this is the real tool UI, running here on a mock app session. Walk the guided tour, or skip it and start tapping:

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

The same crash arriving through two seams is recorded once. Two genuinely separate crashes with the same message are recorded twice — a tool whose job is "the app's last words" shouldn't quietly throw one away.

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

---

## FAQ

### How do I read print and debugPrint output without a debugger attached?

Wrap your entry point in `BuoyConsole.runZoned` — a `Zone` is the only way to observe `print` in Dart — and every `print`, `debugPrint`, and `log` call, plus `FlutterError` reports and uncaught async errors, appears in a filterable on-device panel in any build.

### Can I read the logs from a crash?

Yes. An uncaught error normally takes the desktop connection down before the throttled snapshot fires; Buoy pushes a crash entry out immediately while the connection is alive, tagged `[UNCAUGHT]` or `[RENDER ERROR]`, so the app's last words are readable from the dashboard and from an AI agent.
