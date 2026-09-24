# @buoy-gg/console

[![npm version](https://img.shields.io/npm/v/@buoy-gg/console?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/console) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/console?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/console)

Read captured JavaScript console logs in your React Native app. Filter messages by level, search their contents, and inspect logged objects.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/console
```

## Before you start

Use a development build with `@buoy-gg/core` and a Free or Pro Buoy account key. From your app’s directory, sign in:

```bash
npx --package=@buoy-gg/core buoy login
```

For Expo, initialize Buoy before rendering the menu:

```tsx
import { Buoy } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });
```

The login command writes the Expo key to `.env.local`. For React Native CLI, pass the key from your app’s environment configuration; React Native does not load `.env.local` automatically. Mount `FloatingDevTools` inside the same providers as your screens and restart the development server after installation. The [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) shows the complete root component setup.

## Quick start

After initialization and account admission, Console captures supported JavaScript logs. It can retain logs emitted before the panel opens:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools />  {/* Console tool auto-detected, capture auto-starts */}
    </>
  );
}
```

That's the whole setup — it patches `console.*` directly, so no logger integration or Babel plugin is required.

> Upgrading? Older versions required mounting `<ConsoleRoot />` manually. That's automatic now; an existing manual mount is harmless (capture install is idempotent) and can be deleted.

## Check the integration

After Buoy is initialized and your account is admitted, trigger a `console.log` in your app and find it in Console. Logs removed by your release build and native crash reports are not captured JavaScript console messages.

## What you get

- **Everything logged, everywhere** — patches `console.*` directly, so logs from your own code *and* your dependencies are captured with no extra setup.
- **Fatal crashes captured too** — uncaught JS errors (including fatal render crashes) are hooked from React Native's global error handler and recorded as `[FATAL]`-tagged error entries, so a crashed app's last words are in the log instead of nowhere.
- **Live, color-coded stream** — `log`, `info`, `warn`, and `error` stream in as they happen, color-coded by level.
- **Chrome-DevTools-style panel** — a 1:1 port of the Chrome console view: objects and arrays are formatted and expandable, just like the browser.
- **Filter by level** — focus on just errors and warnings when you're chasing a bug.
- **Search** — filter messages by substring to find the exact log you care about.
- **Preserve log** — an optional setting keeps the buffer across reloads, honored from the very first log.
- **Read production logs** — Buoy runs on-device in any build, so you can read console output from a release build with no cable and no Metro connection.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/console) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
