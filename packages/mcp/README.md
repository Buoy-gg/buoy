# @buoy-gg/mcp

[![npm version](https://img.shields.io/npm/v/@buoy-gg/mcp?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/mcp)
[![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/mcp?style=flat-square&labelColor=1c1c1c&color=10B981&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/mcp)

Connect an MCP client to supported Buoy tools in a running app. Read captured state, invoke tool actions, and run configured benchmarks.

## Before you start

Use Node.js 18 or later. For React Native, complete the [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start), install `@buoy-gg/external-sync`, and restart the app. Flutter uses its [debug-build connection setup](https://buoy.gg/buoy/latest/docs/flutter/quick-start).

Configure the MCP process account as well as the device account. The process accepts `BUOY_LICENSE_KEY` or supported project keys such as `BUOY_KEY` and `EXPO_PUBLIC_BUOY_KEY`. It checks the project’s `.env.local` and `.env` when no supported process environment key is set. Set `BUOY_PROJECT_DIR` if the editor launches the process outside your app directory. A connected device’s key does not sign the process in.

Data and action tools require Pro. Device discovery is still subject to broker account admission.

## Install

```bash
npx -y @buoy-gg/mcp@latest init
```

Run setup from your app’s directory. Review its changes:

- Registers a `buoy` server in your MCP configs — `.mcp.json` and `.cursor/mcp.json` (created when needed), plus `.vscode/mcp.json` when `.vscode` exists. Existing servers are preserved; re-running replaces the Buoy entry, including customizations to that entry.
- Copies the bundled `buoy-optimize` skill into `.claude/skills/`. Rerunning setup overwrites matching skill files, so save any customizations before updating.
- Appends debugging guidance to `CLAUDE.md` and `AGENTS.md` when the guide heading is absent. Existing blocks are preserved and may need manual updating.

The config it writes launches the server via `npx -y @buoy-gg/mcp@latest`, which may require registry access at launch. Review package updates as part of your development workflow. Restart your editor, open your app with Buoy running, and start with `list_devices`.

### Corporate / private npm registries

If your machine's `.npmrc` points npm at a private registry that isn't reachable (a common setup on work laptops, e.g. off-VPN), `npx @latest` would hang trying to download the package on every editor launch — so `init` probes that registry first and, when it's unreachable, automatically installs a **pinned local copy** (from public npm) and writes a `node <path>` config instead. This removes package download from the local server launch path. Account validation and broker connections can still use the network.

You can also force it:

```bash
npx -y @buoy-gg/mcp@latest init --local                 # install a pinned local server
npx -y @buoy-gg/mcp@latest init --npx                    # launch through npx @latest
npx -y @buoy-gg/mcp@latest init --registry <url>         # registry the local install pulls from
```

To update a local server install, run `npx -y @buoy-gg/mcp@latest init --local`. Review skill customizations before rerunning setup. Existing debugging-guide blocks are preserved and need separate review.

## What your agent can do

**Read the runtime**

- `get_events` — one timeline across every tool: network, state changes, renders, routes, storage writes — in token-friendly summaries
- `get_console` — read the app's console logs, including release logs when console calls are retained and capture is enabled
- `get_network_requests` — HTTP requests *with their ids* (which `get_events` omits), marked 📌 pinned / 🔖 saved; `flagged:"any"` reads the requests you kept
- `network_action` — pin or save a request. Pinned requests survive Clear, the history cap and restarts, so it doubles as a handoff: pin the broken call and ask your agent about "the pinned request", or let it pin what it wants you to see
- `network_conditions` — read or set the device's network conditions: offline, +500 ms, +2000 ms, or normal. Offline rejects new intercepted requests before they leave the device, so an agent can check an error path without touching the server. Development builds only, and the profile resets on a JS reload

**Drive the UI**

- `describe_screen` — walks the live React tree into an accessibility-style element list with labels, testIDs, control values, and tap points
- `tap_element` — presses real JS handlers: tap a button, flip a switch, move a slider, type into a field. No screenshots, no pixel coordinates, works on physical devices. Off-screen targets are scrolled into view first.
- `reload_app` — restarts the JS bundle (Expo Go, dev client, or RN CLI) and waits for the app to come back, for changes Fast Refresh missed or a clean-slate startup. Ships with `@buoy-gg/core` — no extra package needed.

**Change state**

- `redux_dispatch`, `react_query_action`, `storage_action`, `navigate` — dispatch actions, invalidate caches, edit storage, navigate to supported routes
- `get_redux_state`, `get_zustand_state`, `get_jotai_state`, `get_react_query`, `get_storage`, `get_routes` — read connected stores first

**Measure**

- `run_benchmark_batch` — runs Bench cases on a real device and returns a ranked comparison with per-component render data
- `screenshot_component` — locates a component by testID in the iOS Simulator and returns a tight crop
- `camera_devices` — booted simulators and the apps installed on one (where a bundle id comes from)
- `camera_inputs` — the Mac's cameras and capturable windows (where a camera uniqueId or `window:<id>` comes from)
- `camera_source` — gives the iOS Simulator a camera and chooses what it shows: a generated
  barcode (QR, PDF417, Aztec, Code 128), an image, a video, the Mac camera, or this Mac's screen
- `camera_status` — what the camera is publishing and what it is decoding right now
- `camera_launch` — relaunch a simulator app with the camera attached
- `camera_zero_setup` — inject into everything the simulator launches, so any launcher works
- `camera_stop` — turn the camera off
- `camera_diagnose` — why the camera is not working, including whether the app is reading it

The `camera_*` tools need Buoy Pro, read from Buoy Desktop's entitlement — `camera_diagnose` is ungated so it can report the tier. The Desktop panel requires an account. Its webcam, image, video, pattern and QR-generation sources are available at Free limits; screen-region capture and non-QR generation require Pro access.
- `list_devices` — see connected devices and the tools each exposes

## The buoy-optimize skill

`init` installs a performance workflow for comparing implementation variants on a device. Ask your assistant for "buoy optimize", review the proposed benchmark, and compare repeated runs under the same conditions. Results depend on the app and workload.

## Requirements

- **Node.js 18+** on the machine running your editor.
- **A running app** with [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core) and the tool packages you want to drive.
- The server connects to the local Buoy broker — or spawns its own in-process — so it works standalone, no Buoy Desktop required.
- **macOS + Xcode** only for `screenshot_component` and the `camera_*` tools (they drive the iOS Simulator); other tools depend on the connected app and its platform integrations.
- The `camera_*` tools need **no connected device** — the simulator camera is host-side and supports compatible simulator apps without an in-app Buoy integration. Host, simulator and account requirements still apply.

## Plans and connections

[MCP data and action tools require Pro](https://buoy.gg/pricing). Account admission also applies to discovery. Check current pricing for Weekend Pass terms.

The broker can accept connections over your LAN; it is not necessarily localhost-only. Use a trusted development network. Account and license validation make external requests. See [Telemetry](https://buoy.gg/buoy/latest/docs/telemetry) for additional data flows.

## Links

- [AI / MCP documentation](https://buoy.gg/buoy/latest/docs/mcp)
- [Buoy on GitHub](https://github.com/Buoy-gg/buoy)
- [Full docs](https://buoy.gg/buoy/latest/docs/overview)

## License

Proprietary software. © Buoy LLC. All rights reserved. See the [Terms of Service](https://buoy.gg/terms).
