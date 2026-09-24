---
title: Overview
seoTitle: "React Buoy — in-app devtools for React Native & Expo"
id: overview
description: "Meet React Buoy, the in-app devtools platform for React Native — debug network, state, storage, and performance from your phone, desktop, or AI agent."
---

Buoy puts devtools inside your React Native app. Inspect requests, storage, state and performance on the phone, in Buoy Desktop or from your coding agent.

## Start here

The quickest way in is to let your coding agent install it:

<!-- ::agent-install where="docs-overview" -->

To install by hand, follow the [Quick Start](./quick-start). It goes from install to your first captured request.

## Where it runs

- **In your app.** A floating button opens the tools over your app, so QA and support can use them on the device that hit the bug. See [FloatingDevTools](./floating-devtools).
- **On your desktop.** [Buoy Desktop](./desktop) shows every connected app in one window. It's free with a Buoy account.
- **In your coding agent.** The [MCP server](./mcp) lets Claude Code, Cursor or Codex read and control the running app. Requires Pro.
- **In a chat inside the app.** [Ask Buoy](./tools/ask-buoy) (beta) drives the other tools in plain English, on the model endpoint you configure. Requires Pro.

## Pick your framework

- **[React Native and Expo](./quick-start)** has the full tool set.
- **[Flutter](./flutter/overview)** is in beta. It runs in debug builds only, and tool coverage differs from React Native.
- **[TV](./tv/overview)** is in beta for Apple TV and Android TV. Nothing renders on the TV screen; the desktop dashboard is the interface.

## What you can do

- **Inspect** requests, storage, React Query, Redux, Zustand and Jotai state, navigation, console output, image loads and re-renders.
- **Change** the running app: edit storage, refetch queries, dispatch actions, restore a [Time Machine](./tools/time-machine) snapshot, or switch to a test user through [Impersonate](./tools/impersonate).
- **Measure** FPS, CPU, memory and JavaScript thread time with [Bench](./tools/perf-monitor) and [JS Top](./tools/js-top).

Every tool is its own package, so you only install what you use. [Installation](./installation#available-packages) lists them all.

Some tools need app integration. Impersonation needs your backend to authorize the selected user, and state tools need access to the stores you want to inspect. Buoy doesn't bypass your app's authentication.

## For AI agents

- [buoy.gg/install.md](https://buoy.gg/install.md) holds the install instructions the prompt points to.
- [llms.txt](https://buoy.gg/llms.txt) indexes the docs, and [llms-full.txt](https://buoy.gg/llms-full.txt) has every page in one file.
- Add `.md` to any docs URL to get the page as Markdown.
- The [MCP server](./mcp) gives your agent tools to call against the running app.

## Build your own tools

Register any React component as a [custom tool](./custom-tools), such as a feature-flag panel or an order-state switcher. It appears in the same menu as the built-in tools.

## Next steps

- [Quick Start](./quick-start): install Buoy and inspect your first request
- [Installation](./installation): packages, account keys and troubleshooting
- [AI / MCP Server](./mcp): connect your coding agent
- [Custom Tools](./custom-tools): build a tool for your app
