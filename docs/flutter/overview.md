---
title: Overview
seoTitle: "Buoy for Flutter — in-app devtools for Flutter apps"
id: flutter-overview
description: "Meet Buoy for Flutter — in-app devtools for network, state, storage, and performance from your phone, desktop, or AI agent."
---

Buoy is a complete devtools platform for Flutter (beta). It turns **hours into seconds** for developers, QA, and product teams on tasks like regression testing, bug reproduction, performance work, and environment validation.

**One devtools engine, three ways to reach your running app:**

- **On your phone** — a zero-config floating menu inside your app, in dev, staging, and production.
- **On your desktop** — Buoy Desktop, a free dashboard for macOS, Windows & Linux that mirrors your tools with a live performance HUD.
- **Through your AI** — the [MCP server](../mcp) lets Claude Code, Cursor, or any MCP editor inspect and control your live app (Pro).

Install once. Reach every tool from wherever you work. Flutter devices show up next to your React Native devices on the same broker.

## Who It's For

<!-- ::start:Audience-Grid -->

<!-- ::start:Audience title="Developers" tagline="Stop guessing. See exactly what's happening." -->
- **Storage event history** — Watch data changes in real-time. Step forward and backward through events.
- **Network inspector** — Every request, response, header, and error. No more print debugging.
- **Reproduce anything** — Open the tools, see the exact state, and reproduce bugs locally in seconds.

*"Before: Bug report → Ask for details → Add prints → Rebuild → Try to reproduce → Guess at state → Repeat"*

*"After: Bug report → Open Buoy → See exact state → Fix"*
<!-- ::end:Audience -->

<!-- ::start:Audience title="QA" tagline="Test things that were previously untestable." -->
- **Trigger any state** — Loading states, error states, empty states, edge cases. Now you can test them all.
- **Edit data live** — Change values in seconds. No more waiting for database changes.
- **Validate everything** — Env and storage validators flag missing keys, wrong types, and invalid values instantly.
- **Real regression testing** — Test the app AND the API. Stale data, race conditions, permission edge cases.

*"Before: Need edge case → Ask dev to update DB → Wait 30+ min → Test → Need another change → Wait again → Repeat"*

*"After: Need edge case → Edit value in Buoy → Test → Edit again → Test again → Done in seconds"*
<!-- ::end:Audience -->

<!-- ::start:Audience title="Customer Support" tagline="See exactly what your users see." -->
- **Impersonation tools** — Sync as a user and see their exact state. No more "works on my machine."
- **Copy debug data** — Grab storage state, network logs, and environment info for bug reports.
- **Instant answers** — Is the data wrong or is it a refresh issue? Check storage. Is the API failing? Check network.

*"Before: Customer reports issue → Ask for screenshots → Ask for more info → Escalate to dev → Dev can't reproduce → Back and forth for days"*

*"After: Customer reports issue → Impersonate user → See exact state → Copy debug data → Dev reproduces instantly"*
<!-- ::end:Audience -->

<!-- ::start:Audience title="Product & Everyone Else" tagline="Remove the friction that slows everyone down." -->
- **Feature flags in seconds** — No more asking someone to enable a feature on your account.
- **Skip the login dance** — Need to test as admin? As a new user? As a banned user? Just set it.
- **Jump to any route** — Go directly to the screen you need. Validate auth checks and permissions.

*"Before: Need feature flag → Slack dev → Wait → Need admin role → Slack another dev → Wait → Log out → Log in → Navigate to screen"*

*"After: Open Buoy → Toggle flag → Set role → Jump to route → Done"*
<!-- ::end:Audience -->

<!-- ::end:Audience-Grid -->

---

This isn't a "nice to have." It's the difference between hours of debugging and coordination versus seconds. The time savings compound across your entire team.

## What You Get

| Tool | What It Does |
|------|--------------|
| **Network** | See every API call — `package:http`, dio, image loads, GraphQL |
| **Storage** | Browse and edit `shared_preferences` in real-time |
| **Environment** | Validate env/config with type checking and a health score |
| **Console** | A Chrome-DevTools-style console for every `print` / `debugPrint` / `log` |
| **Perf Monitor** | Live on-device HUD — FPS, jank, CPU, and memory |
| **Images** | Every image load with cache verdict, timing, oversize audit & failure diagnosis |
| **Riverpod** | Inspect provider state, live values, diffs, and history |
| **Route Inspector** | Track go_router navigation and browse your route structure |
| **Events Timeline** | Unified timeline across all tools with LLM-ready export |
| **Impersonate** | Inject impersonation headers to debug as any user |
| **Image Overlay** | Overlay design mockups on your app for pixel-perfect comparison |

## Why Buoy

- **Zero config** — `flutter pub add buoy`, wrap one widget, tools appear.
- **Three ways in** — The same tools on your phone, on your desktop, and through your AI agent.
- **Works everywhere** — Dev, staging, production. Same tools for everyone.
- **Modular** — Only install what you need. Each tool is a separate pub package.
- **Same protocol as RN** — Flutter devices show up next to React Native on Desktop and MCP.

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

Install the umbrella (or any tool package) and it appears in the menu. Get your license key at [buoy.gg/pricing](https://buoy.gg/pricing).

## Build Your Own Tools

Need something specific to your app? Drop in custom tools via the `tools` prop on `BuoyDevTools`. Build internal debugging utilities, feature flag toggles, or team-specific inspectors that integrate with the floating menu.

## Next Steps

- [Installation](./installation) — Add Buoy to your project
- [Quick Start](./quick-start) — Full setup in 2 minutes
- [AI / MCP Server](../mcp) — Let AI agents drive your running app
- [Custom Tools](./custom-tools) — Build your own debugging tools
- [Tools Reference](./tools/network) — Detailed docs for each tool
