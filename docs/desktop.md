---
title: Buoy Desktop
seoTitle: "React Native Desktop DevTools Dashboard — Buoy Desktop"
id: desktop
description: "Mirror your React Native or Flutter app's Buoy devtools to a full-size desktop dashboard — live performance HUD, multi-device switching, and remote control included."
---

<!-- ::desktop-download -->

Buoy Desktop is a native dashboard for macOS, Windows, and Linux that mirrors your on-device Buoy tools to a full-size window in real time. It includes a live performance HUD, multi-device switching, and remote control over the running app.

React Native and Flutter devices speak the **same protocol**, so they show up side by side in one dashboard.

## Requirements

- A Buoy account, signed in on Desktop and in the app. In Desktop, click **Sign in with buoy.gg**. For the app, run `npx --package=@buoy-gg/core buoy login` in its folder. A connected device does not sign Desktop in. Desktop takes a key too, under **Already have a key?**
- **A running app** with Buoy devtools installed and open on a device or simulator (React Native or Flutter).
- **React Native:** the `@buoy-gg/external-sync` package installed in the app — it's the sync client, and it ships separately from the tools (see below).
- The app and the desktop dashboard on the **same machine or local network**.

## Install

The download button above grabs the right build for your machine automatically; every build for macOS, Windows, and Linux is on the [GitHub releases page](https://github.com/Buoy-gg/Buoy-Desktop/releases/latest). It auto-updates, so you stay on the latest build. Buoy Desktop is free to use — a [Buoy Pro license](https://buoy.gg/pricing) unlocks full history and unlimited capture, See pricing for current plan allowances.

On a Mac, the download is a `.dmg`. Open it and drag Buoy into Applications. If you open Buoy from somewhere else, it offers to move itself there, because it updates itself from Applications.

## Connect your app

Buoy tools sync to a local broker on **port 42831**. Launch Buoy Desktop first; it starts the broker and auto-detects connected devices. Use the device switcher in the title bar to choose which device every tool inspects — every install of your app is its own entry, named after the app and hardware (`Acme App (iPhone 17 Pro · 2c1d)`). If no device appears, the dashboard shows a troubleshooting panel with your machine's exact URLs and a test you can run from the phone's browser.

### Who can connect

The broker listens on your local network, so phones on the same Wi-Fi reach it without setup. Only Buoy Desktop and the Buoy MCP server on your machine can read app data or send actions to an app. They prove it with a token the broker writes when it starts, to `~/.buoy/broker-token` (`%APPDATA%\Buoy\broker-token` on Windows), a file only your user account can read. Anything else that connects is treated as an app: it can send its own data and receive actions addressed to it, and can't see or control anything else.

Each app has a device id. One machine can hold an id on many sockets. A second machine that shows up with an id already in use is refused, and the Desktop log says which address tried. If you pin `externalSync.deviceId`, give each device its own.

Web apps served from your machine or your local network connect as usual. When a page from any other site tries to connect, such as your production site with Buoy turned on for admins, Buoy Desktop asks whether to allow that site. It remembers an Allow; a Don't Allow lasts until Desktop restarts.

To accept connections from this machine only, create `broker-settings.json` containing `{ "allowNetworkDevices": false }` in Buoy Desktop's data folder (`~/Library/Application Support/Buoy` on macOS, `%APPDATA%\Buoy` on Windows, `~/.config/Buoy` on Linux) and restart Desktop, or launch it with `BUOY_BROKER_HOST=127.0.0.1`. Simulators, emulators and Android over USB keep working; phones on Wi-Fi can't connect.

Traffic between a phone and your machine isn't encrypted, so connect over a network you trust.

### React Native

First, install the sync client in your app. It's a separate package on purpose — apps that never use the desktop dashboard don't carry the sync code at all:

<!-- ::pm npm="npm install @buoy-gg/external-sync" yarn="yarn add @buoy-gg/external-sync" pnpm="pnpm add @buoy-gg/external-sync" bun="bun add @buoy-gg/external-sync" -->

Restart Metro after installing it. In development, `FloatingDevTools` detects the package and derives the broker address from Metro. The device must be able to reach that address; use the options below when the derived host is unsuitable.

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
<FloatingDevTools externalSync={{ enableInRelease: true }} />
```

There is no Metro server in a release bundle, so the broker host can't be derived and `socketURL` defaults to `http://localhost:42831`. That is already right for the iOS Simulator, the Android emulator, and Android over USB (`adb reverse tcp:42831 tcp:42831`).

Release sync carries real user data, such as session tokens in network captures. So a release build won't connect over plain `http://` to another machine, where anyone on the same network could read the traffic; it logs a warning instead. It connects to `localhost` or over `https://`. For a physical iOS device on a network you trust, opt in to plain http as well:

```tsx
<FloatingDevTools
  externalSync={{
    enableInRelease: true,
    socketURL: "http://192.168.1.20:42831",
    allowInsecureNetwork: true,
  }}
/>
```

Most tools work the same in a release build. That means network capture, storage, console, the state tools, routes, images, assets and the performance HUD. Two things stay off:

- **Highlight Updates** needs React's DevTools hook. React only installs it in dev builds. This also turns off the MCP `describe_screen`, `tap_element` and `measure_renders` calls.
- **Reload** falls back to `expo-updates`. Without that package, `reload_app` says it has no way to reload.

#### Release builds are read-only from Desktop and the MCP

A release build runs for real people on real accounts. So Buoy Desktop and the MCP can read each tool, but they can't change the app. A storage write, a response override, a tap or a restore is refused. The error says how to allow it. The tools on the device itself work as before.

To let Desktop and the MCP change one tool, list it:

```tsx
<FloatingDevTools
  externalSync={{ enableInRelease: true, releaseActions: ["storage", "network"] }}
/>
```

To allow every tool, pass `"all"`:

```tsx
<FloatingDevTools
  externalSync={{ enableInRelease: true, releaseActions: ["all"] }}
/>
```

Impersonate and the OTA inspector need their own name, even with `"all"`. Impersonate acts as another person, and the OTA inspector changes the code the app runs. Stopping an impersonation always works.

```tsx
<FloatingDevTools
  externalSync={{ enableInRelease: true, releaseActions: ["all", "impersonate"] }}
/>
```

#### Secrets are hidden before they leave the app

Your app covers login tokens, cookies and keys first. Then it sends its data. Buoy Desktop and the MCP see `[hidden by Buoy]` where a secret was. By default, a real token does not reach an AI model or the Wi-Fi.

This works the same in React Native, web, Swift, Flutter, Android and MAUI.

What gets hidden:

- Auth headers and cookies. `Bearer abc123` turns into `Bearer [hidden by Buoy]`.
- Values under names like `password`, `accessToken` or `apiKey`.
- Values that look like a token, such as a JWT or a Stripe key.
- Passwords and tokens inside links.
- A token Buoy has seen once. It stays hidden in every other tool too.

Names, emails and other user data are not hidden.

No filter is perfect. A secret under a plain name like `v` can get through until Buoy has seen it in a header.

Need the real value? Click **Secrets hidden** at the bottom of Buoy Desktop. The app then sends real values, and the chip says **Secrets shown**. Click it again to hide them.

It also turns off on its own:

- when you pick another device
- when you close Desktop
- when the app restarts
- after 10 minutes with Desktop closed or frozen

Some more things to know:

- The MCP hides secrets too, even when Desktop shows them. Set `BUOY_SHOW_SECRETS=1` where the MCP runs to turn that off.
- An AI agent can't turn the switch on. Only a person in Desktop can.
- Buoy will not write a hidden value back to your app. An edit with `[hidden by Buoy]` in it is refused. Type the real value to change it.
- A release build keeps secrets hidden. In React Native and web you can allow the switch with `"secrets"`:

```tsx
<FloatingDevTools
  externalSync={{ enableInRelease: true, releaseActions: ["secrets"] }}
/>
```

In Swift, Flutter, Android and MAUI, a release build always hides secrets.

### Flutter

Run a debug build with `BuoyDevTools` mounted and your account configured. Flutter's widget does not enable this connection in profile or release mode.

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
- **[Ask Buoy](./tools/ask-buoy), mirrored** — Follow a tester's in-app AI conversation live from your desk: what it says, what it changed, whether each change can be put back, and what the turn cost in tokens. Read-only, plus remote undo — there is no desktop composer on purpose, because conversations are started on the device. Keep the broker on a trusted development network; account admission does not authorize individual users to control particular devices.
- **Built-in troubleshooting** — A "no devices" panel shows your machine's exact URLs with a phone-browser test; the Diagnostics console streams the broker's own connection log (handshakes, disconnect reasons, version mismatches — replayed even if they happened before you opened it); offline devices are removable and age out after a day.

## How it works

The desktop app hosts the same local broker the [MCP server](./mcp) uses. Your app connects as a device; the dashboard connects as a "Dashboard" client and receives live state and sends actions over the external-sync protocol. Tool data travels between the device and the configured broker, including across your LAN for physical devices. Account validation and telemetry use separate services; see [Telemetry](./telemetry).

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

Sync is off whenever `__DEV__` is false, so a shipped app never dials a broker on a customer's phone. To profile a release build you own — a local Release run, an internal TestFlight/EAS build — opt in explicitly; it also requires a real Pro license. Release builds connect to `localhost` or over `https://` unless you set `allowInsecureNetwork`; see [Release builds](#release-builds).
