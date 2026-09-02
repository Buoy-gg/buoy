---
title: Buoy Desktop
seoTitle: "React Native Desktop DevTools Dashboard — Buoy Desktop"
id: desktop
description: "Mirror your React Native or Flutter app's Buoy devtools to a full-size desktop dashboard — live performance HUD, multi-device switching, and remote control included."
---

<!-- ::desktop-download -->

Buoy Desktop is a native dashboard for macOS, Windows, and Linux that mirrors your on-device Buoy tools to a full-size window in real time. Same tools as the floating menu — with dramatically more room — plus a live performance HUD, multi-device switching, and remote control over the running app.

React Native and Flutter devices speak the **same protocol**, so they show up side by side in one dashboard.

## Requirements

- **A running app** with Buoy devtools installed and open on a device or simulator (React Native or Flutter).
- **React Native:** the `@buoy-gg/external-sync` package installed in the app — it's the sync client, and it ships separately from the tools (see below).
- The app and the desktop dashboard on the **same machine or local network**.

## Install

The download button above grabs the right build for your machine automatically; every build for macOS, Windows, and Linux is on the [GitHub releases page](https://github.com/Buoy-gg/Buoy-Desktop/releases/latest). It auto-updates, so you stay on the latest build. Buoy Desktop is free to use — a [Buoy Pro license](https://buoy.gg/pricing) unlocks full history and unlimited capture, and every weekend Pro unlocks for everyone.

## Connect your app

Buoy tools sync to a local broker on **port 42831**. Launch Buoy Desktop first; it starts the broker and auto-detects connected devices. Use the device switcher in the title bar to choose which device every tool inspects — every install of your app is its own entry, named after the app and hardware (`Acme App (iPhone 17 Pro · 2c1d)`). If no device appears, the dashboard shows a troubleshooting panel with your machine's exact URLs and a test you can run from the phone's browser.

### React Native

First, install the sync client in your app. It's a separate package on purpose — apps that never use the desktop dashboard don't carry the sync code at all:

<!-- ::pm npm="npm install @buoy-gg/external-sync" yarn="yarn add @buoy-gg/external-sync" pnpm="pnpm add @buoy-gg/external-sync" bun="bun add @buoy-gg/external-sync" -->

With it installed, the connection is **automatic** — no wiring, no config. `FloatingDevTools` detects the package and derives the broker address from the Metro dev server that served the bundle, so simulators, emulators, and physical devices on the same Wi-Fi all find your machine with zero config.

Need to point somewhere else? Pass `socketURL` in the `externalSync` prop:

- **Expo tunnel mode** — the derived host is the tunnel domain, which has no broker; pass your machine's LAN IP explicitly.
- **Android over USB** — just run `adb reverse tcp:42831 tcp:42831` once per cable session; no `socketURL` needed (the derived `localhost` is kept as-is on physical devices — the `10.0.2.2` rewrite only applies to emulators).
- **Broker on another machine** — pass that machine's `http://<ip>:42831`.

```tsx
<FloatingDevTools externalSync={{ socketURL: "http://192.168.1.20:42831" }} />
```

> **Just installed a @buoy-gg package?** Restart Metro with `--clear` — Metro caches the "optional package missing" resolution, and a plain reload never picks the new package up.

#### Release builds

A shipped app must never dial a broker on a customer's phone, so sync is **off** whenever `__DEV__` is false. To profile a release build you own — a local `--configuration Release` run, an internal TestFlight/EAS build, a field build that ships headless — opt in explicitly. It also requires a real Pro license.

```tsx
<FloatingDevTools
  externalSync={{
    enableInRelease: true,
    // iOS physical devices only: there is no Metro host to derive
    socketURL: "http://192.168.1.20:42831",
  }}
/>
```

There is no Metro server in a release bundle, so the broker host can't be derived and `socketURL` defaults to `http://localhost:42831`. That is already right for the iOS Simulator, the Android emulator, and Android over USB (`adb reverse tcp:42831 tcp:42831`) — pass it explicitly for a physical iOS device or a broker on another machine.

Most tools work the same in a release build — network capture, storage, console, the state tools, routes, images, assets, the performance HUD, and remote actions. Three things stay off, by design and not by choice:

- **Highlight Updates** (render counts, and the MCP `describe_screen` / `tap_element` / `measure_renders` calls) needs React's DevTools hook, which React only installs in dev builds.
- **Network response overrides** stay disabled — a shipped build must not be able to mock its own responses.
- **Reload** falls back to `expo-updates`; without that package installed, `reload_app` reports that it has no mechanism instead of reloading.

### Flutter

- **iOS Simulator / Android Emulator** — connects automatically (`localhost` / `10.0.2.2`).
- **Physical devices** — pass your computer's LAN IP:

```dart
BuoyDevTools(
  socketUrl: 'http://192.168.1.20:42831',
  child: child ?? const SizedBox.shrink(),
)
```

iOS will show the Local Network permission prompt on first connect — tap Allow. After adding a new `buoy_*` package, do a **full restart** (hot reload won't pick up new registrations).

## What Desktop adds

- **Full-screen tools** — Network, Storage, Console, Routes, Events, Env, Images, Impersonate, and more — plus React Native–only panels (React Query, Redux/Zustand/Jotai, Bench, JS Top) when those packages are installed.
- **Live performance HUD** — Stream FPS, CPU, and memory from the device while you use it.
- **Multi-device** — Switch between every connected simulator and physical device (RN and Flutter mixed).
- **Remote actions** — Edit storage, navigate routes, and drive installed tools from your desk.
- **Screenshot tool** — Capture a region or a specific component from the iOS Simulator (React Native).
- **Built-in troubleshooting** — A "no devices" panel shows your machine's exact URLs with a phone-browser test; the Diagnostics console streams the broker's own connection log (handshakes, disconnect reasons, version mismatches — replayed even if they happened before you opened it); offline devices are removable and age out after a day.

## How it works

The desktop app hosts the same local broker the [MCP server](./mcp) uses. Your app connects as a device; the dashboard connects as a "Dashboard" client and receives live state and sends actions over the external-sync protocol. Nothing leaves your machine.

## What's Next

- [AI / MCP Server](./mcp) — Drive the same tools from your AI editor
- [React Native Quick Start](./quick-start) — Get Buoy into an RN app
- [Flutter Quick Start](./flutter/quick-start) — Get Buoy into a Flutter app

---

## FAQ

### How do I see my React Native devtools on my desktop?

Download Buoy Desktop for macOS, Windows, or Linux and open your app with Buoy running. The app derives the broker address from the Metro dev server that served the bundle, so simulators, emulators, and physical devices on the same Wi-Fi connect with zero config.

### Which port does Buoy Desktop use?

Buoy tools sync to a local broker on port 42831. Launch Buoy Desktop first — it starts the broker and auto-detects devices. For Android over USB, run `adb reverse tcp:42831 tcp:42831` once per cable session.

### Can I use it with a release build?

Sync is off whenever `__DEV__` is false, so a shipped app never dials a broker on a customer's phone. To profile a release build you own — a local Release run, an internal TestFlight/EAS build — opt in explicitly; it also requires a real Pro license.
