---
title: Buoy Desktop
id: desktop
description: "Mirror your React Native app's Buoy devtools to a full-size desktop dashboard — live performance HUD, multi-device switching, and remote control included."
---

Buoy Desktop is a native dashboard for macOS, Windows, and Linux that mirrors your on-device Buoy tools to a full-size window in real time. Same tools as the floating menu — with dramatically more room — plus a live performance HUD, multi-device switching, and remote control over the running app.

## Requirements

- **Buoy Pro** — Desktop is a Pro feature.
- **A running app** with Buoy devtools installed and open on a device or simulator.
- The app and the desktop dashboard on the **same machine or local network**.

## Install

Download Buoy Desktop from [buoy.gg](https://buoy.gg/pricing). It auto-updates, so you stay on the latest build.

## Connect your app

Buoy tools sync to a local broker on **port 42831**. Point your app's sync at the desktop:

- **Simulator / emulator** — `http://localhost:42831` (Android emulator: `http://10.0.2.2:42831`).
- **Physical device** — `http://<your-computer-ip>:42831`, on the same Wi-Fi network.

Launch Buoy Desktop first; it starts the broker and auto-detects connected devices. Use the device switcher in the title bar to choose which device every tool inspects.

## What Desktop adds

- **Full-screen tools** — Network, Storage, Console, React Query, Redux/Zustand/Jotai, Routes, Events, and more, each in a real panel.
- **Live performance HUD** — Stream FPS, CPU, and memory from the device while you use it.
- **Multi-device** — Switch between every connected simulator and physical device.
- **Remote actions** — Edit storage, dispatch Redux, refetch queries, and navigate the app from your desk.
- **Screenshot tool** — Capture a region or a specific component from the iOS Simulator.

## How it works

The desktop app hosts the same local broker the [MCP server](./mcp) uses. Your app connects as a device; the dashboard connects as a "Dashboard" client and receives live state and sends actions over the external-sync protocol. Nothing leaves your machine.

## What's Next

- [AI / MCP Server](./mcp) — Drive the same tools from your AI editor
- [Quick Start](./quick-start) — Get Buoy into your app
