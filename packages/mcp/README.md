# @buoy-gg/mcp

[![npm version](https://img.shields.io/npm/v/@buoy-gg/mcp?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/mcp)
[![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/mcp?style=flat-square&labelColor=1c1c1c&color=10B981&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/mcp)

**Your agent gets hands.** The MCP server that connects Claude Code, Cursor, or any MCP editor to the [Buoy devtools](https://www.npmjs.com/package/@buoy-gg/core) running inside your React Native app — so your agent can read live runtime state, drive the UI, and benchmark on a real device instead of debugging blind.

## Install

```bash
npx -y @buoy-gg/mcp@latest init
```

One command, non-destructive:

- Registers a `buoy` server in your MCP configs — `.mcp.json` (Claude Code, always), plus `.cursor/mcp.json` and `.vscode/mcp.json` when those folders exist. Existing servers are preserved; re-running just refreshes the Buoy entry.
- Installs the **buoy-optimize** skill into `.claude/skills/`.

The config it writes launches the server via `npx -y @buoy-gg/mcp@latest`, so every editor restart re-resolves the newest published version — you never get pinned to a stale copy. Restart your editor, open your app with Buoy running, and start with `list_devices`.

## What your agent can do

**Read the runtime**

- `get_events` — one timeline across every tool: network, state changes, renders, routes, storage writes — in token-friendly summaries
- `get_console` — read the app's console logs, even from a release build

**Drive the UI**

- `describe_screen` — walks the live React tree into an accessibility-style element list with labels, testIDs, control values, and tap points
- `tap_element` — presses real JS handlers: tap a button, flip a switch, move a slider, type into a field. No screenshots, no pixel coordinates, works on physical devices. Off-screen targets are scrolled into view first.

**Change state**

- `redux_dispatch`, `react_query_action`, `storage_action`, `navigate` — dispatch actions, invalidate caches, edit storage, jump to any screen
- `get_redux_state`, `get_zustand_state`, `get_jotai_state`, `get_react_query`, `get_storage`, `get_routes` — read any store first

**Measure**

- `run_benchmark_batch` — runs Bench cases on a real device and returns a ranked comparison with per-component render data
- `screenshot_component` — locates a component by testID in the iOS Simulator and returns a tight crop
- `list_devices` — see connected devices and the tools each exposes

## The buoy-optimize skill

`init` also installs a guided performance wizard. Your assistant benchmarks implementation variants on the **real device**, reads the ranked results, applies the winning change, and repeats until the metrics plateau. Measuring instead of guessing is what makes it fast: one real run took a Skia LED display from 28 stuttering lights to over 12,000 with no lag. Kick it off with **"buoy optimize"**.

## Requirements

- **Node.js 18+** on the machine running your editor.
- **A running app** with [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core) and the tool packages you want to drive.
- The server connects to the local Buoy broker — or spawns its own in-process — so it works standalone, no Buoy Desktop required.
- **macOS + Xcode** only for `screenshot_component` (it captures the iOS Simulator); everything else is platform-agnostic.

## Pro

The MCP data and action tools require [Buoy Pro](https://buoy.gg/pricing); `list_devices` always works free. And every Saturday and Sunday, Pro unlocks free for everyone — built into the product, so you can try the whole thing for real before deciding.

Everything runs over a localhost-only broker — nothing ever leaves your machine.

## Links

- [AI / MCP documentation](https://buoy.gg/buoy/latest/docs/mcp)
- [Buoy on GitHub](https://github.com/Buoy-gg/buoy)
- [Full docs](https://buoy.gg/buoy/latest/docs/overview)

## License

Proprietary software. © Buoy LLC. All rights reserved. See the [Terms of Service](https://buoy.gg/terms).
