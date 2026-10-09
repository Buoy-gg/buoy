---
title: Overview
seoTitle: "Buoy for React web apps — the React Native devtools, in the browser"
id: web-overview
description: "Meet Buoy on the web (beta): the same floating menu, tool panels and Desktop connection as React Native, running in a React web app through React Native Web."
---
<!-- ::tool-film id="web" -->

For Angular, use the [Angular guide](./angular).
It has its own [AI install prompt](./angular#start-here).

For Angular, use the [Angular guide](./angular).
It has its own [AI install prompt](./angular#start-here).

For 3D apps, use the [three.js (Beta) guide](./three).

Use the [phone app guide](../capacitor) for Capacitor / Ionic (Beta).

Buoy's web build is the React Native one. Every package ships a `/web` entry that uses the same tool panels, stores, filters, actions, snapshots and sync protocol, with React Native Web drawing the UI. A small set of browser modules replaces what differs: storage, routing, DOM inspection, the clipboard, images and performance timing. A fix to a shared tool reaches both platforms.

Web support is in beta. Browser builds first shipped in `7.0.41`.

**Three ways to reach your running web app:**

- **In the page** — the same floating menu and dial as on a phone.
- **On your desktop** — [Buoy Desktop](../desktop) lists the browser tab in the device switcher next to your phones.
- **Through your AI** — the [MCP server](../mcp) lets Claude Code, Cursor or another MCP client read and drive the tab. Free has the basic MCP. Pro adds the full MCP.

## Start here

Let your coding agent install Buoy in your app:

<!-- ::agent-install platform="web" where="docs-web-overview" -->

To install by hand, follow the [Quick Start](./quick-start).

## What you need

- React and React DOM, plus `react-native-web` 0.21. You don't need React Native or Expo.
- The state libraries your tools inspect, such as Zustand, Jotai, Redux or TanStack Query.
- A Free or Pro Buoy account key.

## What works in the browser

The tools in the web sidebar all have browser builds. Their docs are shared with React Native, and each page has a note on what changes on the web. The largest differences:

| Tool | In the browser |
|---|---|
| [Network](../tools/network) | The page's `fetch` and XHR calls from the moment the early hook loads, including overrides and network conditions. |
| [Storage](../tools/storage) | `localStorage` and `sessionStorage`. |
| [Routes](../tools/routes) | History API and hash navigation, plus any router you connect. |
| [Perf Monitor](../tools/perf-monitor) | Frame rate, JS heap where available, long tasks. |
| [Highlight Updates](../tools/highlight-updates) | React renders, through an import that loads before React DOM. |

[Installation](./installation#tool-setup) has the setup for every tool.

## Known limits

- **Only the current page is captured.** Workers, other frames, WebSockets and browser-internal traffic need separate collectors.
- **CORS still applies.** Buoy can only read the headers and bodies the page itself can read, and cross-origin timings may need `Timing-Allow-Origin`.
- **Browser APIs differ from native ones.** IndexedDB, cookies and secure storage don't show up in Storage, and CPU, memory and thermal readings have no browser equivalent.
- **Not every tool has a browser build.** Simulator Camera works with the iOS Simulator only, and TV Remote and Focus Inspector only observe keyboard and DOM focus events in a browser.
- **Development builds by default.** Release connections need a Pro license and an explicit opt-in, the same as on native.

Next: [Quick Start](./quick-start). For Next.js, React Router or TanStack Start, see [Frameworks](./frameworks) for where each piece goes.
