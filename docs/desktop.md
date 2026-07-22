---
title: Buoy Desktop
id: desktop
description: "Mirror your React Native app's Buoy devtools to a full-size desktop dashboard — live performance HUD, multi-device switching, and remote control included."
---

<p>
  <a href="https://github.com/Buoy-gg/Buoy-Desktop/releases/latest" target="_blank" rel="noreferrer" style="display:inline-flex;align-items:center;gap:10px;padding:14px 28px;border-radius:10px;background:#10B981;color:#ffffff;font-weight:600;font-size:16px;text-decoration:none;box-shadow:0 8px 24px rgba(16,185,129,0.25);">
    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M12 3v12"/><path d="m7 11 5 5 5-5"/><path d="M5 21h14"/></svg>
    Download Buoy Desktop
  </a>
  <br/>
  <span style="font-size:12px;opacity:0.7;">Free download · macOS · Windows · Linux · auto-updates</span>
</p>

Buoy Desktop is a native dashboard for macOS, Windows, and Linux that mirrors your on-device Buoy tools to a full-size window in real time. Same tools as the floating menu — with dramatically more room — plus a live performance HUD, multi-device switching, and remote control over the running app.

## Requirements

- **A running app** with Buoy devtools installed and open on a device or simulator.
- The app and the desktop dashboard on the **same machine or local network**.

## Install

Download Buoy Desktop for macOS, Windows, or Linux from the [GitHub releases page](https://github.com/Buoy-gg/Buoy-Desktop/releases/latest). It auto-updates, so you stay on the latest build. Buoy Desktop is free to use — a [Buoy Pro license](https://buoy.gg/pricing) unlocks full history and unlimited capture, and every weekend Pro unlocks for everyone.

## Connect your app

Buoy tools sync to a local broker on **port 42831** — and the connection is **automatic**. The app derives the broker address from the Metro dev server that served the bundle, so simulators, emulators, and physical devices on the same Wi-Fi all find your machine with zero config.

Launch Buoy Desktop first; it starts the broker and auto-detects connected devices. Use the device switcher in the title bar to choose which device every tool inspects. If no device appears, the dashboard shows a troubleshooting panel with your machine's exact URLs and a test you can run from the phone's browser.

Need to point somewhere else? Pass `socketURL` in the `externalSync` prop:

- **Expo tunnel mode** — the derived host is the tunnel domain, which has no broker; pass your machine's LAN IP explicitly.
- **Android over USB** — just run `adb reverse tcp:42831 tcp:42831` once per cable session; no `socketURL` needed (the derived `localhost` is kept as-is on physical devices — the `10.0.2.2` rewrite only applies to emulators).
- **Broker on another machine** — pass that machine's `http://<ip>:42831`.

```tsx
<FloatingDevTools externalSync={{ socketURL: "http://192.168.1.20:42831" }} />
```

> **Just installed a @buoy-gg package?** Restart Metro with `--clear` — Metro caches the "optional package missing" resolution, and a plain reload never picks the new package up.

## What Desktop adds

- **Full-screen tools** — Network, Storage, Console, React Query, Redux/Zustand/Jotai, Routes, Events, and more, each in a real panel.
- **Live performance HUD** — Stream FPS, CPU, and memory from the device while you use it.
- **Multi-device** — Switch between every connected simulator and physical device.
- **Remote actions** — Edit storage, dispatch Redux, refetch queries, and navigate the app from your desk.
- **Screenshot tool** — Capture a region or a specific component from the iOS Simulator.
- **Built-in troubleshooting** — A "no devices" panel shows your machine's exact URLs with a phone-browser test; the Diagnostics console streams the broker's own connection log (handshakes, disconnect reasons, version mismatches — replayed even if they happened before you opened it); offline devices are removable and age out after a day.

## How it works

The desktop app hosts the same local broker the [MCP server](./mcp) uses. Your app connects as a device; the dashboard connects as a "Dashboard" client and receives live state and sends actions over the external-sync protocol. Nothing leaves your machine.

## What's Next

- [AI / MCP Server](./mcp) — Drive the same tools from your AI editor
- [Quick Start](./quick-start) — Get Buoy into your app
