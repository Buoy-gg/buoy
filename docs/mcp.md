---
title: AI / MCP Server
id: mcp
description: "Let AI agents like Claude Code and Cursor read your React Native app's live network, state, and storage — and tap through the UI — via the Buoy MCP server."
---

Drive your Buoy dev tools from an AI coding assistant. The Buoy MCP server lets Claude Code, Cursor, and other MCP clients read your app's live runtime — network requests, state (Redux/Zustand/Jotai), React Query, storage, routes, console — and take actions, run performance benchmarks, drive the UI by tapping through it, and even screenshot a specific on-screen component, all against your running app.

## Requirements

- **Buoy Pro** — the MCP is a Pro feature. `list_devices` always works, but data/action tools require a connected app on an active Buoy Pro license.
- **Node.js 18+** on the machine running your editor.
- **A running app** with Buoy devtools open on a device or simulator.
- **macOS + Xcode** — only for the `screenshot_component` tool (it captures the iOS Simulator). Everything else is platform-agnostic.

## Install

One command wires the server into your editor and installs the Buoy skill:

```bash
npx -y @buoy-gg/mcp@latest init
```

This merges a `buoy` server into your MCP config (`.mcp.json` for Claude Code, plus `.cursor/mcp.json` and `.vscode/mcp.json` when those folders exist) and drops the `buoy-optimize` skill into `.claude/skills/`. It's non-destructive — existing servers are preserved, and re-running just refreshes the Buoy entry.

Then restart your editor (or reconnect the MCP server) and open your app with Buoy devtools running.

The config it writes launches the server via `npx -y @buoy-gg/mcp@latest`, so **every editor restart re-resolves the newest published version** — you don't get pinned to a stale copy.

## Updating

Because the default config uses `@latest`, you're normally always current. To force a refresh (or update the installed skill), re-run:

```bash
npx -y @buoy-gg/mcp@latest init
```

The server also checks npm about once a day and, when a newer version is out, includes a one-line notice in the `list_devices` result so your assistant can prompt you to update.

## Manual configuration

If you'd rather wire it by hand, add this to your MCP config:

```json
{
  "mcpServers": {
    "buoy": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@buoy-gg/mcp@latest"]
    }
  }
}
```

## Usage

Ask your assistant to start with `list_devices` to see connected devices and the tools each exposes. From there it can:

- **Inspect runtime** — `get_events` (network, state changes, renders, route changes, storage writes), `get_snapshot`, and per-tool readers.
- **Take actions** — dispatch Redux actions, set Zustand/Jotai state, invalidate React Query caches, navigate routes, edit storage, and more via `call_action` or the tool-specific wrappers.
- **Drive the UI** — `describe_screen` and `tap_element` let the agent read what's on screen and interact with it, no screenshots (see below).
- **Benchmark performance** — `run_benchmark_batch` and the perf-monitor tools.
- **Profile the JS thread** — `get_js_thread_top` returns a live "Task Manager" of which timers, Promise chains & callbacks eat JS-thread time (with freeze attribution); `get_js_thread_origin_detail` drills into one origin's scheduling site and stats.
- **Screenshot a component** — `screenshot_component` locates a component by testID/name in the iOS Simulator and returns a tight, cropped image.

A good starting prompt: **"buoy optimize"** kicks off a guided performance pass using the bundled skill.

## Driving the UI

Two tools let your agent operate the app the way a user would — **without screenshots or pixel coordinates**, so it's fast and works on physical devices too:

- **`describe_screen`** walks the live React fiber tree and returns the on-screen elements as a compact, accessibility-style list — each with its label/text, `testID`, a normalized tap point, and (for controls) its type and current value, e.g. `Switch {toggle=true}`, `Slider {slider=75}`, `TextInput {text="Ada"}`. Inactive/covered navigator screens are pruned, so you see what's actually in front of the user.
- **`tap_element`** interacts with an element found by `testID`, `nativeTag`, or a fuzzy `query`. It invokes the element's handler directly in JS: tap a button (`onPress`), flip a switch or move a slider (`value`), or type into a field (`text`). Off-screen targets are scrolled into view first.

A typical loop is: `describe_screen` to see the options → `tap_element({ testID })` to act → `describe_screen` again to confirm the result. Prefer `testID` or `nativeTag` when a screen has repeated labels.

## The buoy-optimize wizard

`init` also installs a **`buoy-optimize` skill** — a guided wizard that automates mobile performance work end to end. Whether you're shipping a new feature or fixing a screen that's janky on device, your assistant benchmarks implementation variants on the **real device** with [Bench](./tools/perf-monitor), reads the ranked results, applies the winning change, and repeats until the metrics plateau.

Measuring on-device instead of guessing is what makes it fast: optimization passes that used to take days or weeks of AI back-and-forth finish in minutes. One real run took a Skia LED display from **28 lights stuttering** to **over 12,000 lights with no lag**.

It's close to fully automated — the parts that stay manual are the ones only a human can judge, like confirming the UI still renders correctly. When you're working with Skia or other GPU-drawn views, plan to glance at the screen each pass: the wizard drives the metrics, you confirm it still looks right. Kick it off with **"buoy optimize"**.

## How it works

Buoy tools run inside your app and sync to a local broker over the external-sync protocol. The MCP server connects to that broker as a "Dashboard" client — the same role the Buoy desktop app plays — or spawns its own broker in-process when no desktop app is running, so it works standalone.
