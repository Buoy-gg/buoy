# BUOY Devtools

[![npm version](https://img.shields.io/npm/v/@buoy-gg/core?color=brightgreen)](https://www.npmjs.com/package/@buoy-gg/core)
[![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/core?color=blue&label=downloads%2Fmonth)](https://www.npmjs.com/package/@buoy-gg/core)
[![Legacy Package](https://img.shields.io/npm/dt/react-native-react-query-devtools?color=9cf&label=legacy%20downloads)](https://www.npmjs.com/package/react-native-react-query-devtools)
[![Dependents](https://img.shields.io/librariesio/dependents/npm/@buoy-gg/core?color=success)](https://www.npmjs.com/package/@buoy-gg/core)
[![TypeScript](https://img.shields.io/badge/TypeScript-100%25-blue)](https://www.typescriptlang.org/)

**Devtools that live in your app. And answer to your agent.**

Buoy ships inside your React Native app. Every request, state change, render, and frame — live on the device, on your desktop, and in Claude or Cursor. In dev, staging, and production.

- **Zero config** — install → tools appear in the floating menu
- **Three surfaces** — phone, desktop, and AI agent share one live session
- **Modular** — every tool is its own package; install only what you need

📚 **[Full Documentation](https://buoy.gg/buoy/latest/docs/overview)** · 🚀 **[Quick Start Guide](https://buoy.gg/buoy/latest/docs/quick-start)**

![BUOY Demo](https://github.com/user-attachments/assets/a732d6a3-9963-49e3-b0f1-0d974a0a74d7)

---

## One live app. Three ways in.

Every tool runs inside your app. Tap the floating menu, open the desktop dashboard, or point your agent at it — same data, same session.

- 📱 **On your phone** — Drop in one component and a floating dev menu appears inside your app — on any device, in any environment. No desktop app, no cable, no config.
- 🖥️ **On your desktop** — [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) mirrors the on-device tools to a real window on macOS, Windows & Linux — with a live performance HUD and remote control over the device. ([Desktop docs](https://buoy.gg/buoy/latest/docs/desktop))
- 🤖 **Through your agent** — The [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp) connects Claude Code, Cursor, or any MCP editor to the running app. Your agent sees what the app is doing at runtime — and can actually change it.

---

## Quick Start

### 1. Install

```bash
npm install @buoy-gg/core
# or: yarn add / pnpm add / bun add
```

### 2. Add to Your App

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* Your app */}
      <FloatingDevTools />
    </>
  );
}
```

That's it — installed tools automatically appear in the floating menu. Free in development builds; add a `licenseKey` prop to unlock [Pro](https://buoy.gg/pricing) (production builds, Buoy Desktop, and the MCP server):

```tsx
<FloatingDevTools licenseKey="YOUR_LICENSE_KEY" />
```

---

## Available Tools

| Tool                                                                            | Package                       | What It Does                                                       |
| ------------------------------------------------------------------------------- | ----------------------------- | ------------------------------------------------------------------ |
| [Network](https://buoy.gg/buoy/latest/docs/tools/network)                       | `@buoy-gg/network`            | Every request, response, timing, and error — streamed live         |
| [Storage](https://buoy.gg/buoy/latest/docs/tools/storage)                       | `@buoy-gg/storage`            | Browse and edit AsyncStorage, MMKV & SecureStore, with change history |
| [Env](https://buoy.gg/buoy/latest/docs/tools/env)                               | `@buoy-gg/env`                | Environment variables, validated with type checking                |
| [Query](https://buoy.gg/buoy/latest/docs/tools/react-query)                     | `@buoy-gg/react-query`        | TanStack Query cache inspection, refetch & mutation testing        |
| [Routes](https://buoy.gg/buoy/latest/docs/tools/routes)                         | `@buoy-gg/route-events`       | Navigation events, the live stack, and jump-to-any-screen          |
| [Debug Borders](https://buoy.gg/buoy/latest/docs/tools/debug-borders)           | `@buoy-gg/debug-borders`      | Visualize layout with colored borders — tap any element to inspect testIDs & styles |
| [Highlight Updates](https://buoy.gg/buoy/latest/docs/tools/highlight-updates)   | `@buoy-gg/highlight-updates`  | Visual overlays on every render, with the cause: mount, state, props, or parent |
| [Bench](https://buoy.gg/buoy/latest/docs/tools/perf-monitor)                    | `@buoy-gg/perf-monitor`       | FPS, CPU, memory & jank — record runs and compare them             |
| [Events](https://buoy.gg/buoy/latest/docs/tools/events)                         | `@buoy-gg/events`             | One timeline across every tool, with LLM-ready export              |
| [Console](https://buoy.gg/buoy/latest/docs/tools/console)                       | `@buoy-gg/console`            | A Chrome-DevTools console for every log on the device              |
| [Redux](https://buoy.gg/buoy/latest/docs/tools/redux)                           | `@buoy-gg/redux`              | Inspect store state and dispatch actions from any surface          |
| [Zustand](https://buoy.gg/buoy/latest/docs/tools/zustand)                       | `@buoy-gg/zustand`            | Store state, diffs, jump-to-state and reset                        |
| [Jotai](https://buoy.gg/buoy/latest/docs/tools/jotai)                           | `@buoy-gg/jotai`              | Atom values, diffs, and full event history                         |
| [Impersonate](https://buoy.gg/buoy/latest/docs/tools/impersonate)               | `@buoy-gg/impersonate`        | Switch users, roles, and feature flags without rebuilding          |
| [Overlay](https://buoy.gg/buoy/latest/docs/tools/image-overlay)                 | `@buoy-gg/image-overlay`      | Pin design mockups over the app, pixel-perfect                     |

Install any tool and it auto-appears in the menu:

```bash
npm i @buoy-gg/{core,network,storage,env,react-query,route-events,debug-borders,highlight-updates,perf-monitor,events,console,redux,zustand,jotai,impersonate,image-overlay}
```

> Full tool docs + screenshots: [buoy.gg docs](https://buoy.gg/buoy/latest/docs/overview)

---

## 🤖 AI / MCP — your agent debugs the live app

```bash
npx -y @buoy-gg/mcp@latest init
```

One command wires the Buoy MCP server into Claude Code, Cursor, and VS Code. Your agent gets real, structured tool calls into the running app — not screenshot-and-guess:

- **Read the live runtime** — `get_events` streams network requests, state changes, renders, routes, and storage writes as compact, token-friendly summaries
- **Inspect & change state** — read and dispatch Redux, set Zustand and Jotai state, invalidate React Query, edit storage, navigate
- **Drive the UI** — `describe_screen` + `tap_element` walk the live React tree and press real handlers; works on physical devices, no pixel coordinates
- **Benchmark** — `run_benchmark_batch` runs Bench cases on a real device and returns a ranked comparison
- **See components** — `screenshot_component` returns a tight crop of any component by `testID` (iOS Simulator)

It also installs the **buoy-optimize** skill — a guided performance wizard that benchmarks variants on the real device and applies the winner. One real run took a Skia LED display from 28 stuttering lights to over 12,000 with no lag.

📚 **[AI / MCP docs](https://buoy.gg/buoy/latest/docs/mcp)** · Pro feature

---

## 🖥️ Buoy Desktop

Every tool, full screen. **[Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop)** mirrors the on-device tools to a native dashboard for macOS, Windows & Linux — full-screen panels for Network, Storage, Console, and all your state tools, plus a live FPS · CPU · memory HUD, multi-device switching, remote actions, and a component screenshot tool.

➡️ **[Get Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop)** · 📚 **[Desktop docs](https://buoy.gg/buoy/latest/docs/desktop)** · Pro feature

---

## Why BUOY?

|                              | BUOY | Flipper | Reactotron | RN Debugger |
| ---------------------------- | :--: | :-----: | :--------: | :---------: |
| On-device (no desktop app required) | ✅   | ❌      | ❌         | ❌          |
| Works in production          | ✅   | ❌      | ❌         | ❌          |
| AI agent control (MCP)       | ✅   | ❌      | ❌         | ❌          |
| QA/support can use it        | ✅   | ❌      | ❌         | ❌          |
| Zero config setup            | ✅   | ❌      | ❌         | ❌          |

---

## Safe in Production

You control who sees it. BUOY's tools run inside your app's process — a tiny broker on your machine mirrors them to the desktop app and hands them to your MCP client. Localhost only; nothing leaves your machine.

```tsx
<>
  {/* Your app */}
  {isInternalUser && <FloatingDevTools />}
</>
```

---

## Pricing

**Free forever** in development builds. **Pro** adds production builds, Buoy Desktop, and the MCP server — 14-day trial, no credit card. And every weekend, Pro unlocks free for everyone.

💳 **[buoy.gg/pricing](https://buoy.gg/pricing)**

---

## Contributing

- Bugs / feature requests: [GitHub Issues](https://github.com/Buoy-gg/buoy/issues)
- PRs welcome

---

## License

Proprietary software. © Buoy LLC. All rights reserved.

See [Terms of Service](https://buoy.gg/terms) for usage terms.

## 💙 Credits

Big thanks to [galaxies.dev](https://galaxies.dev) — their content helped me get up to speed with React Native early on, and I strongly recommend it as a resource for anyone making the jump from web to mobile.

<a href="https://galaxies.dev">
  <img src="https://github.com/Galaxies-dev/react-native-ecommerce/blob/main/banner.png?raw=true" width="100%" />
</a>
