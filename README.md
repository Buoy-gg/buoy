<div align="center">

# 🛟 Buoy

Developer tools inside your React Native app.

[Docs](https://buoy.gg/buoy/latest/docs/overview) · [Quick Start](#-quick-start) · [Desktop](#%EF%B8%8F-buoy-desktop) · [MCP](#-your-agent-gets-hands) · [Pricing](https://buoy.gg/pricing)

[![npm version](https://img.shields.io/npm/v/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/core)
[![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/core?style=flat-square&labelColor=1c1c1c&color=10B981&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/core)
[![legacy downloads](https://img.shields.io/npm/dt/react-native-react-query-devtools?style=flat-square&labelColor=1c1c1c&color=10B981&label=legacy%20downloads)](https://www.npmjs.com/package/react-native-react-query-devtools)
[![TypeScript](https://img.shields.io/badge/TypeScript-100%25-10B981?style=flat-square&labelColor=1c1c1c)](https://www.typescriptlang.org/)

Inspect captured requests, app state, storage, and performance on your device. Connect supported tools to Desktop or an AI assistant. Start with a development build and a Free or Pro Buoy account.

[Flutter setup](https://buoy.gg/buoy/latest/docs/flutter/quick-start) is available separately for debug builds.

![Buoy demo — the floating dev menu in action](https://github.com/user-attachments/assets/a732d6a3-9963-49e3-b0f1-0d974a0a74d7)

</div>


---

## ⚡ Quick Start

**Have a coding agent?** Paste this into Claude Code, Cursor or Codex. It reads your lockfile and
`package.json`, installs the core plus only the tools that match your app, mounts the menu below
your providers, wires your stores, and tells you what to check on the device. It follows
[buoy.gg/install.md](https://buoy.gg/install.md) — read it first if you like.

```text
Install Buoy, the in-app devtools for React Native and Expo, in this project.

Read the full instructions first:
  curl -fsSL https://buoy.gg/install.md
Read the raw text, not a summary. If curl is unavailable, use any HTTP tool you have. Ask me to paste the document only if nothing can fetch it.

Do the install yourself: inspect the repo, run the commands, edit the files. Do not hand me steps you can run.

Work out the routine decisions from the repo: package manager, which app to target, where the menu mounts, which Buoy tools match the dependencies already installed. If Buoy is already partly installed, repair and extend it; never add a second mount, provider, or package set.

When instructions conflict, follow this order: what I say here, then the document's rules about which packages exist and what needs my permission, then this project's own constraints, then the rest of the document, then your judgment.

Ask me before: adding a native dependency, opening a browser or creating an account, changing what a production build does beyond what the document specifies, or anything hard to undo. Do not commit.

You are not done when the packages install. Run this project's existing typecheck; add no tooling. Then report what you installed and why, what you skipped and why, every file you changed, what you verified, and the exact steps I take on the device to confirm the menu appears and captures a request.
```

**By hand:**

Install from your app’s directory, then sign in:

```bash
npm install @buoy-gg/core @buoy-gg/network
npx --package=@buoy-gg/core buoy login
```

In an Expo Router project, keep your existing navigator and providers. For example:

```tsx
import { Stack } from "expo-router";
import { Buoy, FloatingDevTools } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });

export default function RootLayout() {
  return (
    <>
      <Stack />
      <FloatingDevTools />
    </>
  );
}
```

The login command writes the Expo key to `.env.local`. Keep the navigator and menu inside your existing providers. Without Expo Router, keep your app’s root content in place of `Stack`. For React Native CLI, load the key through your app’s environment configuration and pass it to `Buoy.init`; `.env.local` is not loaded automatically.

Restart the development server after installation. Open Buoy, complete any account prompt, and select Network. Trigger a new HTTP request in your app and check its URL and status. If no request appears, check whether the app used cached data or an unsupported transport.

See the [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) for the full setup and [Installation](https://buoy.gg/buoy/latest/docs/installation) for requirements. Other tools may need store registration, provider context, or native dependencies.

---

## 🛟 One live session. Four ways in.

Inspect configured tools in the floating menu on your device. You can also connect them to:

- [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop), for desktop panels and remote actions. React Native apps need `@buoy-gg/external-sync`; follow the [connection guide](https://buoy.gg/buoy/latest/docs/desktop) and sign in to Desktop separately.
- [MCP](https://buoy.gg/buoy/latest/docs/mcp), for an AI editor to inspect and act on the app. Configure both process and device accounts; data and action tools require Pro.
- [Ask Buoy](https://buoy.gg/buoy/latest/docs/tools/ask-buoy), for an in-app assistant. It requires Pro, a model endpoint you configure, and the tool integrations its tasks depend on.

Supported features vary by platform, build, and installed tools. [Flutter](https://buoy.gg/buoy/latest/docs/flutter/quick-start) has a separate debug-build setup.

---

## 🧰 Tools

| Tool | Package | What it does |
| --- | --- | --- |
| [Network](https://buoy.gg/buoy/latest/docs/tools/network) | `@buoy-gg/network` | Inspect HTTP requests captured through your React Native app’s global `fetch` and `XMLHttpRequest` hooks. Open a request to see its URL, status, headers, timing, and captured body. |
| [Storage](https://buoy.gg/buoy/latest/docs/tools/storage) | `@buoy-gg/storage` | Browse and edit supported AsyncStorage, registered MMKV instances, and registered SecureStore keys inside your app. |
| [Time Machine](https://buoy.gg/buoy/latest/docs/tools/time-machine) | `@buoy-gg/time-machine` | Save and restore supported client state; external side effects are not reversed. |
| [Ask Buoy](https://buoy.gg/buoy/latest/docs/tools/ask-buoy) *(beta)* | `@buoy-gg/ask-buoy` | Use an in-app assistant with your model endpoint and configured tools. |
| [Env](https://buoy.gg/buoy/latest/docs/tools/env) | `@buoy-gg/env` | Inspect runtime environment values and validate required variables. |
| [Query](https://buoy.gg/buoy/latest/docs/tools/react-query) | `@buoy-gg/react-query` | Inspect the TanStack Query cache used by your app. Refetch or invalidate a query and simulate states while checking the screen that consumes it. |
| [Routes](https://buoy.gg/buoy/latest/docs/tools/routes) | `@buoy-gg/route-events` | Inspect recorded navigation events and supported routes. |
| [Debug Borders](https://buoy.gg/buoy/latest/docs/tools/debug-borders) | `@buoy-gg/debug-borders` | Show layout borders and component labels over your React Native app. Tap a label to inspect the component’s available properties. |
| [Highlight Updates](https://buoy.gg/buoy/latest/docs/tools/highlight-updates) | `@buoy-gg/highlight-updates` | Highlight observed React component updates in development builds and inspect the available render details. |
| [Bench](https://buoy.gg/buoy/latest/docs/tools/perf-monitor) | `@buoy-gg/perf-monitor` | Measure available frame-rate, CPU, and memory metrics on a device. Record comparable runs to investigate performance changes. |
| [JS Top](https://buoy.gg/buoy/latest/docs/tools/js-top) | `@buoy-gg/js-top` | Task Manager for the JS thread — a live ranked table of which timers, Promise chains & callbacks eat your JS FPS |
| [Images](https://buoy.gg/buoy/latest/docs/tools/images) | `@buoy-gg/images` | Inspect image loads observed by Buoy’s supported image hooks, including timing, dimensions, and estimated decoded memory. |
| [Assets](https://buoy.gg/buoy/latest/docs/tools/assets) | `@buoy-gg/assets` | Inspect assets visible to the runtime registry and, in development, the Metro dependency graph. Compare sizes and find possible duplicates. |
| [Events](https://buoy.gg/buoy/latest/docs/tools/events) | `@buoy-gg/events` | Review events from configured Buoy tools in one timeline. Filter by source, inspect an event, and export a selection for debugging. |
| [Console](https://buoy.gg/buoy/latest/docs/tools/console) | `@buoy-gg/console` | Read captured JavaScript console logs in your React Native app. Filter messages by level, search their contents, and inspect logged objects. |
| [Sentry](https://buoy.gg/buoy/latest/docs/tools/sentry) | `@buoy-gg/sentry` | Inspect captured Sentry envelopes and diagnostic estimates. |
| [Redux](https://buoy.gg/buoy/latest/docs/tools/redux) | `@buoy-gg/redux` | Inspect your connected Redux store, review captured actions and state changes, and dispatch actions from your device. |
| [Zustand](https://buoy.gg/buoy/latest/docs/tools/zustand) | `@buoy-gg/zustand` | Inspect the Zustand stores you register with Buoy. Review changes, edit state, and restore retained snapshots. |
| [Jotai](https://buoy.gg/buoy/latest/docs/tools/jotai) | `@buoy-gg/jotai` | Inspect registered Jotai atoms and their captured changes, with live values and per-atom history. |
| [Impersonate](https://buoy.gg/buoy/latest/docs/tools/impersonate) | `@buoy-gg/impersonate` | Connect your app’s user-switching flow to Buoy so testers can switch test accounts, roles, or flags without rebuilding. |
| [TV Remote](https://buoy.gg/buoy/latest/docs/tools/tv-remote) | `@buoy-gg/tv-remote` | Send supported TV input and replay recorded navigation sequences. |
| [Focus Inspector](https://buoy.gg/buoy/latest/docs/tools/focus-inspector) | `@buoy-gg/focus-inspector` | Inspect observed TV focus changes and possible focus problems. |
| [Camera](https://buoy.gg/buoy/latest/docs/tools/camera) | *(no package — desktop app)* | Give the iOS Simulator a real camera — point it at your Mac screen, webcam, an image or a video, and scan QR codes or driver's licences without a device |
| [Overlay](https://buoy.gg/buoy/latest/docs/tools/image-overlay) | `@buoy-gg/image-overlay` | Place a reference image over your app to compare spacing, alignment, and sizing on the device. |

Install tools as you need them. Follow each linked guide for its peer dependencies and app integration.

<details>
<summary><strong>See them in action</strong></summary>

<p align="center">
  <img src="https://github.com/user-attachments/assets/473ddf83-03cd-4bd1-8dc3-0f66eda9fa8a" width="30%" alt="Network tool" />
  <img src="https://github.com/user-attachments/assets/80ef1c60-d20c-4d8b-97e6-f37b21b315ea" width="30%" alt="Storage tool" />
  <img src="https://github.com/user-attachments/assets/258e892d-3eaf-41f8-9fae-d7d2dcd6c39d" width="30%" alt="React Query tool" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/90e55dc7-f8ab-423a-9770-84b9ff9c8446" width="30%" alt="Routes tool" />
  <img src="https://github.com/user-attachments/assets/945fdb5d-2546-442d-98e7-ef73231abbba" width="30%" alt="Debug Borders tool" />
  <img src="https://github.com/user-attachments/assets/75651046-33a0-4257-9011-3bcc4818a964" width="30%" alt="Env tool" />
</p>

</details>

---

## 🤖 Your agent gets hands

Install `@buoy-gg/external-sync` in your React Native app and complete the [MCP account and connection setup](https://buoy.gg/buoy/latest/docs/mcp). Then run:

```bash
npx -y @buoy-gg/mcp@latest init
```

Review the generated editor configuration. Setup preserves other server entries but updates the Buoy entry and its generated skill. Reconnect your MCP client, open your app, and start with `list_devices`.

Available actions include reading captured requests and state, changing supported stores, and running configured benchmarks. These actions can change your running app. UI inspection depends on React Native hooks; simulator screenshots and camera features require macOS and Xcode.

---

## 🖥️ Buoy Desktop

[Download Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop/releases/latest) for your platform and sign in. It displays connected tools, supports device switching, and exposes supported remote actions.

React Native connections require `@buoy-gg/external-sync`. Automatic address discovery depends on your Metro setup and network; follow the [Desktop guide](https://buoy.gg/buoy/latest/docs/desktop) for physical devices, tunnels, and release builds.

---

## Why Buoy?

Use Buoy when you want to inspect app behavior from the device and share that session with desktop tools or an assistant. Choose packages for your app’s libraries and verify their capture paths with a known test action.

---

<a id="safe-in-production"></a>

## Production and connections

Start with a development build. Before enabling Buoy in a shipped app, restrict access using your app’s authorization checks and review the [component reference](https://buoy.gg/buoy/latest/docs/floating-devtools). Production access requires Pro, and development-only capabilities remain unavailable.

Hiding the menu is not a substitute for backend authorization. Headless mode hides the on-device UI; it still requires account admission and sync configuration. Release sync is disabled by default and must be explicitly enabled.

Device sessions can travel over your LAN to the configured broker. Buoy also makes account and license requests; development telemetry is described in [Telemetry](https://buoy.gg/buoy/latest/docs/telemetry). Ask Buoy sends model requests to the endpoint you configure.

---

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.


---

## Feedback

Found a bug or want a tool that doesn't exist yet? [Open an issue](https://github.com/Buoy-gg/buoy/issues) — feature requests drive the roadmap.

## License

Proprietary software. © Buoy LLC. All rights reserved. See the [Terms of Service](https://buoy.gg/terms).

---
