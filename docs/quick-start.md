---
title: Quick Start
seoTitle: "React Native DevTools Setup — install Buoy in minutes"
id: quick-start
description: "Get React Buoy's floating in-app devtools menu running in your React Native or Expo app in minutes, then reach the same tools from desktop or an AI agent."
---

Install Buoy in a React Native or Expo app, then open Network Monitor and inspect a request your app made.

## Before you start

- A React Native app on 0.70 or newer, or an Expo app, with React 18 or newer.
- A debug build. Core and Network are JavaScript only, so Expo Go works.
- A Buoy account. Step 1 creates a free one if you don't have one yet.

## Let your agent do it

Claude Code, Cursor and Codex can do the whole install. Copy the prompt, paste it into your agent and review its changes. Then do the check in step 3.

<!-- ::agent-install where="docs-quick-start" -->

To install by hand, follow the steps below.

## 1. Install

Install the core menu and Network Monitor from your app's directory:

<!-- ::pm npm="npm install @buoy-gg/core @buoy-gg/network" yarn="yarn add @buoy-gg/core @buoy-gg/network" pnpm="pnpm add @buoy-gg/core @buoy-gg/network" bun="bun add @buoy-gg/core @buoy-gg/network" -->

Then sign in to your Buoy account:

```bash
npx --package=@buoy-gg/core buoy login
```

The command opens your browser, writes your key to an env file and adds that file to `.gitignore`. In Expo, a free key goes to `.env.development.local` as `EXPO_PUBLIC_BUOY_KEY`. That file is only read in development, so the key never ends up in a release build. A paid key goes to `.env.local`. React Native CLI apps get `BUOY_KEY` in `.env.local`. [Installation](./installation#get-your-key) has the details.

## 2. Mount the menu

Pick your setup. In all three, keep `FloatingDevTools` inside the same providers as your screens, so tools such as React Query can reach them.

<!-- ::start:tabs -->

#### Expo Router

Add Buoy to your root layout, `app/_layout.tsx` (or `src/app/_layout.tsx`). This example uses a Stack. Keep whichever navigator you already have.

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

#### Expo

Render `FloatingDevTools` next to your existing `App` content, inside any providers.

```tsx
import { Buoy, FloatingDevTools } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools />
    </>
  );
}
```

#### React Native CLI

Mount the menu in your root component. React Native doesn't load `.env.local` into `process.env` by itself, so pass `BUOY_KEY` in through the environment setup your app already uses.

```tsx
import { Buoy, FloatingDevTools } from "@buoy-gg/core";

// Read BUOY_KEY with the env loader your app already uses.
Buoy.init({ licenseKey: yourConfiguredKey });

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools />
    </>
  );
}
```

<!-- ::end:tabs -->

Restart the dev server so Metro picks up the new packages. In Expo:

```bash
npx expo start --clear
```

Open the app and tap the floating button. If Buoy asks you to set up an account, check that your key reached `Buoy.init`.

## 3. See your first request

Open **Network**, go back to your app and do something that makes an HTTP request, such as refreshing a list. Open Network again and select the request. You should see its URL, status, timing and response body.

If the list is empty, make sure the action sent a new request and didn't read cached data. [Network Monitor](./tools/network) lists the supported capture paths and overrides.

## 4. Add more tools

Each tool is its own package. Install the ones you want and restart the dev server, and Buoy adds them to the menu. [Installation](./installation#available-packages) lists every package.

A few tools need to be pointed at your app's data:

- **Zustand:** pass the stores you want to inspect through `zustandStores`. See the [Zustand setup](./tools/zustand).
- **Jotai:** register named atoms with `watchAtoms`, using your app's own store if it has a custom provider. See the [Jotai setup](./tools/jotai).

## Control who sees devtools

Render `FloatingDevTools` only for the users who should inspect your app. Use your app's existing authorization checks for internal users, QA, or support. A Buoy account key controls Buoy access; your app decides which users can reach the menu.

Start in development. Before enabling access in a shipped app, review the [component reference](./floating-devtools) and your plan's production restrictions.

## Next steps

- [Buoy Desktop](./desktop): see your connected apps in a desktop dashboard. Desktop is free to use. React Native apps need `@buoy-gg/external-sync` to connect.
- [AI / MCP Server](./mcp): let Claude Code, Cursor or Codex inspect and control the running app. Requires Pro.
- [Ask Buoy](./tools/ask-buoy): an in-app assistant that runs on the model endpoint you configure. Requires Pro.
- [Custom Tools](./custom-tools): add a tool that's specific to your app.
- [FloatingDevTools](./floating-devtools): component options and access controls.

## FAQ

### Do I need a license key to use React Buoy?

Use a Free or Pro Buoy account key for this setup. Run `npx --package=@buoy-gg/core buoy login` from your app's directory. Plan limits and paid features are listed on [pricing](https://buoy.gg/pricing).

### Does Buoy phone home?

Buoy makes account and license requests. Development telemetry is described in [Telemetry](./telemetry); disable that telemetry with `Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY, telemetry: false })`. Disabling telemetry does not disable account validation or connections you configure for Desktop, MCP, or Ask Buoy.

### How do I add a tool to the menu?

Install its package and restart the development server. Check that tool's setup page for required integration, such as registering stores or placing the menu inside a provider.

### Will the devtools ship to my users?

The menu renders where you mount `FloatingDevTools`. Control access in your app. Desktop sync is disabled by default outside development; production sync requires explicit configuration and a Pro license. See [FloatingDevTools](./floating-devtools).
