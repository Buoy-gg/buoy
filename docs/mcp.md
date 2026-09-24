---
title: AI / MCP Server
seoTitle: "React Native MCP Server — let AI agents debug your app"
id: mcp
description: "Let AI agents like Claude Code and Cursor read your React Native or Flutter app's live network, state, and storage — and take actions — via the Buoy MCP server."
---

Drive your Buoy dev tools from an AI coding assistant. The Buoy MCP server lets Claude Code, Cursor, and other MCP clients read your app's live runtime — network requests, storage, routes, console, and framework state — and take actions against your running app.

Works with **React Native and Flutter** on the same broker. Some capabilities below are React Native–only today (called out inline).

<!-- ::mcp-showcase -->

## Requirements

- **Buoy Pro** — the MCP is a Pro feature. Configure the MCP process account as well as the device account. Data and action tools require Pro; device discovery remains subject to broker admission.
- **Node.js 18+** on the machine running your editor.
- **A running app** with Buoy devtools open on a device or simulator (React Native or Flutter).
- **macOS + Xcode** — only for the `screenshot_component` tool (it captures the iOS Simulator). Other host-driven features, such as camera and TV input, also have platform-specific requirements; check their tool pages.

## Install

Run setup from the app project. Configure the MCP process with your account key through its supported environment configuration; a device key does not sign the MCP process in. Use a trusted development network for the broker.

> Installed Buoy with the [agent prompt](./quick-start)? Ask the same agent for "the Desktop and MCP step" — the install instructions it followed cover `@buoy-gg/external-sync` and `npx @buoy-gg/mcp init`.

One command wires the server into your editor and installs the Buoy skill:

```bash
npx -y @buoy-gg/mcp@latest init
```

Setup writes `.mcp.json` and `.cursor/mcp.json`, and updates `.vscode/mcp.json` when `.vscode` exists. Other server entries are preserved; rerunning replaces the Buoy entry, including customizations. It copies the bundled `buoy-optimize` skill, overwriting matching files on reruns. Save skill customizations before updating. Existing debugging-guide blocks are preserved and need separate review.

Then restart your editor (or reconnect the MCP server) and open your app with Buoy devtools running:

- **React Native** — install `@buoy-gg/external-sync`, restart Metro, and mount `<FloatingDevTools />` with your account key (broker address is derived from Metro; physical devices usually need no config). Profiling a **release build**? Sync is off there unless you opt in — see [release builds](./desktop#release-builds).
- **Flutter** — run a debug build and mount `BuoyDevTools` with your account key (simulators auto-connect; physical devices pass `socketUrl: 'http://<lan-ip>:42831'`).

The generated npx entry launches `@buoy-gg/mcp@latest` and may need registry access. Review package updates and verify the connected device after restarting your editor.

### Corporate / private npm registries

If your machine's `.npmrc` points npm at a private registry that isn't reachable (common on work laptops, e.g. off-VPN), `npx @latest` would hang trying to download the package on every editor launch. `init` probes that registry first and, when it's unreachable, automatically installs a **pinned local copy** from public npm and writes a `node <path>` config instead . Package download is removed from the local server launch path; account and broker connections can still use the network. You can also force the behavior:

```bash
npx -y @buoy-gg/mcp@latest init --local             # install a pinned local server
npx -y @buoy-gg/mcp@latest init --npx               # launch through npx @latest
npx -y @buoy-gg/mcp@latest init --registry <url>    # registry the local install pulls from
```

To update the local server, run `npx -y @buoy-gg/mcp@latest init --local`.

## Updating

To refresh configuration, rerun setup. Matching skill files are overwritten, while existing debugging blocks are preserved. Save skill customizations and review debugging-guide updates separately:

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
      "args": ["-y", "@buoy-gg/mcp@latest"],
      "env": { "BUOY_VERIFY": "auto" }
    }
  }
}
```

`BUOY_VERIFY` controls the reminder to check a change on the device before
calling it fixed — see [Confirming a fix actually worked](#confirming-a-fix-actually-worked).

## Usage

Ask your assistant to start with `list_devices` to see connected devices and the tools each exposes. From there it can:

- **Inspect runtime** — `get_events` (network, state changes, route changes, storage writes, …), `get_snapshot`, and per-tool readers. Available sources depend on which packages the app installed (Flutter includes Riverpod; React Native includes Redux/Zustand/Jotai/React Query/renders when those packages are present).
- **Work a single HTTP request** — `get_network_requests` lists requests *with their ids* and marks which are pinned/saved; `network_action` pins or saves one (see below).
- **Take the app offline, or slow it down** *(React Native, development builds)* — `network_conditions` applies offline or added latency to the whole device; [response overrides](./tools/network) cover one specific URL (see below).
- **Take actions** — navigate routes, edit storage, and more via `call_action` or the tool-specific wrappers. React Native also exposes dispatch Redux / set Zustand/Jotai / invalidate React Query when those tools are installed.
- **Drive the UI** *(React Native)* — `describe_screen` and `tap_element` let the agent read what's on screen and interact with it, no screenshots (see below).
- **Benchmark performance** *(React Native Bench)* — `run_benchmark_batch` and the perf-monitor tools.
- **Profile the JS thread** *(React Native)* — `get_js_thread_top` returns a live "Task Manager" of which timers, Promise chains & callbacks eat JS-thread time (with freeze attribution); `get_js_thread_origin_detail` drills into one origin's scheduling site and stats.
- **Screenshot a component** *(React Native / iOS Simulator)* — `screenshot_component` locates a component by testID/name and returns a tight, cropped image.
- **Reload the app** *(React Native)* — `reload_app` restarts the JS bundle (see below).

A good starting prompt on React Native: **"buoy optimize"** kicks off a guided performance pass using the bundled skill.

## Confirming a fix actually worked

After changing app code, repeat the interaction that exposed the problem. Record the device, build, interaction, and observed result. A successful tool call is not proof that the user-visible problem is fixed.

For render work, compare the same interaction before and after with `measure_renders`. Check both the metrics and the UI, and repeat other affected interactions to catch regressions.

`BUOY_VERIFY` controls reminders based on selected source-file edits and tool calls. The watcher does not detect every kind of project change, and clearing a reminder does not prove correctness.

| Value | Behaviour |
| --- | --- |
| `auto` (default) | Reminds once per observed edit burst. |
| `always` | Repeats while observed edits remain unaccounted for by the tool-call policy. |
| `never` | Disables reminders. |

Report actual verification separately. Documentation-only edits generally need static checks; behavior changes need suitable behavioral evidence.

## Driving the UI (React Native)

Two tools let your agent operate a React Native app the way a user would — **without screenshots or pixel coordinates**, so it's fast and works on physical devices too:

- **`describe_screen`** walks the live React fiber tree and returns the on-screen elements as a compact, accessibility-style list — each with its label/text, `testID`, a normalized tap point, and (for controls) its type and current value, e.g. `Switch {toggle=true}`, `Slider {slider=75}`, `TextInput {text="Ada"}`. Inactive/covered navigator screens are pruned, so you see what's actually in front of the user.
- **`tap_element`** interacts with an element found by `testID`, `nativeTag`, or a fuzzy `query`. It invokes the element's handler directly in JS: tap a button (`onPress`), flip a switch or move a slider (`value`), or type into a field (`text`). Off-screen targets are scrolled into view first.

A typical loop is: `describe_screen` to see the options → `tap_element({ testID })` to act → `describe_screen` again to confirm the result. Prefer `testID` or `nativeTag` when a screen has repeated labels.

## Pinned requests as a handoff

`get_events` is the right tool for skimming activity, but it deliberately emits no request ids — so it can't be used to act on a specific call. **`get_network_requests`** covers that: the same compact one-line-per-request style, but each row leads with the id that every network action is keyed by, and marks which requests are 📌 pinned or 🔖 saved. Narrow it with `status: "errors"`, a URL `pattern`, or `includeBodies` when you need payloads.

**`network_action`** pins, saves, or clears. Pinned and saved requests ([RN](./tools/network) · [Flutter](./flutter/tools/network)) keep a snapshot subject to storage and body-size limits. They are separate from the live list, and availability after restart depends on successful persistence. Use retained records for handoffs:

- **You → your agent.** Pin the request that's broken, then ask the assistant to look at "the pinned request". `get_network_requests({ flagged: "pinned" })` reads available retained records, including earlier sessions when persistence succeeded.
- **Your agent → you.** An assistant that finds a failing call can pin it, so it's waiting at the top of your Network list when you next open the tool.

```
get_network_requests({ status: "errors" })   → 3. `fetch_1021` ❌ 500 POST /api/checkout …
network_action({ action: "pin", id: "fetch_1021" })
```

Requests kept from an earlier run of the app come back with ids prefixed `saved:` — they're snapshots, not live requests, so they can't collide with a fresh capture.

## Network conditions

**`network_conditions`** reads or sets the condition the device applies to new requests: `normal`, `offline`, `slow` (+500 ms) or `verySlow` (+2000 ms). Offline rejects intercepted HTTP(S) calls before they are sent, so an agent can walk your app's error and retry paths without a proxy and without touching the server. Latency adds one wait before dispatch; it does not cap bandwidth or change what NetInfo reports.

```
network_conditions({ action: "set", profile: "offline" })
tap_element({ testID: "checkout-submit" })
describe_screen()                                   → the error state the app actually renders
network_conditions({ action: "set", profile: "normal" })
```

The condition lives in memory on the device. It resets on a full JS reload and is never persisted, so an agent that sets one should clear it when it's done rather than leave the next session offline. Setting a condition needs a development build and an admitted Free or Pro account; a release build refuses anything except `normal`. This control is a development preview, and native transports that bypass global fetch and React Native XHR are not affected by it.

## Reloading the app (React Native)

`reload_app` restarts the app's JS bundle from your editor — the same thing as shaking the device and hitting Reload, and the same primitive [Bench](./tools/perf-monitor) uses between benchmark cases. Use it when Fast Refresh didn't pick a change up, to clear leaked in-memory state before a measurement, or to re-run app startup.

In dev builds (including Expo Go and RN CLI) it uses React Native's `DevSettings.reload()`; otherwise it falls back to `expo-updates`, if your app installs it. By default the tool waits for the app to come back and reports how long the reload took, so your assistant can inspect the reconnection result — pass `wait: false` for fire-and-forget. All in-memory state is lost, so anything the assistant read before the reload is stale.

It ships with `@buoy-gg/core` itself, with supported app reload mechanisms. A failed or timed-out reload requires checking the app and connection.

## The buoy-optimize skill (React Native)

For a new skill installation, `init` adds a workflow for investigating rendering performance with [Bench](./tools/perf-monitor) and render measurements. Rerunning setup overwrites matching skill files; save customizations first. Ask your assistant for "buoy optimize" to compare a baseline and selected variants on the target device.

Keep device, build mode, workload and interaction comparable. Review failures, missing metrics and visual behavior before selecting a change. Simulator measurements do not establish results on physical hardware, and a ranking is not proof that the feature works correctly.

## How it works

Buoy tools run inside your app and sync to a local broker over the external-sync protocol. The MCP server connects to that broker as a "Dashboard" client — the same role the Buoy desktop app plays — or spawns its own broker in-process when no desktop app is running, so it works standalone.

React Native and Flutter devices appear together in `list_devices`.

## What's Next

- [Buoy Desktop](./desktop) — The full desktop dashboard on the same broker
- [React Native Quick Start](./quick-start) — Wire MCP against an RN app
- [Flutter Quick Start](./flutter/quick-start) — Wire MCP against a Flutter app
- [Network Monitor](./tools/network) · [Flutter Network](./flutter/tools/network) — Pin & save for agent handoffs

---

## FAQ

### How do I let Claude Code or Cursor debug my React Native app?

Run `npx -y @buoy-gg/mcp@latest init` from the app project, review the configuration changes described above, and restart the editor. Configure account access, app integrations and the broker connection, then inspect `list_devices`. Configuration alone does not establish a working app connection.

### Do I need Buoy Pro for the MCP server?

For data and actions, yes. Configure a verified account for the MCP process and the connected app. Reading runtime data and running actions require Pro.

### Does the MCP server work with Flutter?

Yes — React Native and Flutter apps connect to the same broker and the same server. A few capabilities are React Native–only today, and those are called out inline in the docs.
