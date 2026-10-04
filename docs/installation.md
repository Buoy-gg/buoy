---
title: Installation
seoTitle: "Install React Native DevTools — @buoy-gg/core setup guide"
id: installation
description: "Step-by-step guide to installing React Buoy devtools in a React Native or Expo app — requirements, core package setup, and picking your first tools."
---

This is the reference for installing Buoy: packages, signing in, Desktop and MCP, and fixes for common problems. New to Buoy? [Quick Start](./quick-start) walks you through a first install and ends with a captured request.

## Requirements

<!-- ::Requirements -->

Core and Network are JavaScript only and run in Expo Go. A few tools ship native code and need a development build, such as Bench's native CPU and memory metrics. Each tool's page says what it needs.

## Install with your agent

Paste the prompt into Claude Code, Cursor or Codex. The agent reads your `package.json`, installs the tools that match your app and mounts the menu. Review its changes before you commit them.

<!-- ::agent-install where="docs-installation" -->

## Install by hand

Install the core menu and your first tool:

<!-- ::pm npm="npm install @buoy-gg/core @buoy-gg/network" yarn="yarn add @buoy-gg/core @buoy-gg/network" pnpm="pnpm add @buoy-gg/core @buoy-gg/network" bun="bun add @buoy-gg/core @buoy-gg/network" -->

To install several tools at once, pick them here and copy the command:

<!-- ::Quick-Install -->

## Available Packages

Each package adds one tool to the floating menu. Install only the ones you need.

<!-- ::Tool-Packages -->

## Sign In

Buoy needs a Free or Pro account. The plans have different history limits and features, and production access, MCP and Ask Buoy require Pro. See [pricing](https://buoy.gg/pricing) for details.

### Sign in from your project

```bash
npx --package=@buoy-gg/core buoy login
```

This opens your browser and signs you in. Then it writes a dev token to an env file. It adds that file to `.gitignore` too, if it isn't there yet. On Expo the name is `EXPO_PUBLIC_BUOY_KEY`. On bare React Native it is `BUOY_KEY`. Keep the prefix: Expo only puts `EXPO_PUBLIC_` names in your app.

A dev token works in dev builds and sims for 30 days. Then run `buoy login` again. On Expo it goes in `.env.development.local`. Expo reads that file for `expo start` only. Bare React Native gets `.env.local`. A dev token opens nothing in a TestFlight, QA or store build. For those, use your account key from [buoy.gg/dashboard/licenses](https://buoy.gg/dashboard/licenses), or a bot key. See [Sign in with Buoy](./sign-in).

Read the key in your app and keep the menu inside the same providers as your screens. In React Native CLI, use your app's env loader to read `BUOY_KEY`, because writing an env file doesn't put it in `process.env`.

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

### Keys still work

You don't have to sign in. Your account key from [buoy.gg/dashboard/licenses](https://buoy.gg/dashboard/licenses) works the same as before, and it always will. Put it in the same env var, or pass it in:

```tsx
<FloatingDevTools licenseKey="YOUR_LICENSE_KEY" />
```

Passing it in is fine for a solo project. On a team, use the env var. A key in a shared repo is shared by everyone who clones it.

For CI and test runs, use a bot key. A team admin makes one on the Team page. See [Keys for bots and CI](./sign-in#keys-for-bots-and-ci).

## Desktop & AI (optional)

The packages above power the in-app menu. Desktop is free with a Buoy account. MCP requires Pro.

- **Buoy Desktop** is a dashboard for macOS, Windows and Linux. Install the sync client in your app with `npm install @buoy-gg/external-sync`. It ships separately so apps that never use Desktop don't carry it. Then [download Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop/releases/latest) and launch it. Your app finds it on its own, because the broker address comes from the Metro host. See [Buoy Desktop](./desktop).
- **AI / MCP Server** lets Claude Code, Cursor or any MCP editor drive your app:

```bash
npx -y @buoy-gg/mcp@latest init
```

See [AI / MCP Server](./mcp) for the full setup.

## TypeScript Support

All packages include TypeScript definitions. You don't need any `@types` packages.

## Monorepos & Enterprise Setups

- **`unstable_enablePackageExports: false` works.** Big monorepos often turn off Metro's package-exports resolution for older dependencies. Buoy's packages ship legacy resolution shims, so they resolve either way.
- **Physical devices find Desktop on their own.** The broker address comes from the Metro host, so devices on the same Wi-Fi reach your machine. `socketURL` overrides it for tunnels or a broker on another machine.
- **Scoped registries.** Every package lives under the `@buoy-gg` scope, so a proxy registry only needs a `.npmrc` scope rule (`@buoy-gg:registry=…`).
- **No on-device UI for end users.** Pass `headless` to `FloatingDevTools` for builds where only the desktop dashboard should see the session. See [FloatingDevTools](./floating-devtools).

## Troubleshooting

### The floating button doesn't appear

Check that `FloatingDevTools` renders, including any access condition you wrapped it in, and that you restarted the dev server after installing.

### An installed tool is missing from the menu

Restart Metro with `--clear`. Metro caches the "optional package missing" result from before you installed the package.

```bash
npx expo start --clear
```

### Buoy asks for account setup

Your key didn't reach `Buoy.init`. On Expo, check the variable starts with `EXPO_PUBLIC_` and reload the app after changing it. On React Native CLI, check your env loader passes `BUOY_KEY` through.

### An Android device over USB can't reach Desktop

Forward the broker port:

```bash
adb reverse tcp:42831 tcp:42831
```

### Network shows no requests

Make sure the action sent a new request and didn't read cached data. [Network Monitor](./tools/network) lists the supported capture paths.

## Next Steps

- [Quick Start](./quick-start): a first install, start to finish
- [FloatingDevTools](./floating-devtools): configuration options
- [Buoy Desktop](./desktop): the desktop dashboard
- [AI / MCP Server](./mcp): drive your app from your AI editor
- [Custom Tools](./custom-tools): build your own debugging tools

---

## FAQ

### Which React Buoy package do I install first?

`@buoy-gg/core` — it renders the floating menu. Every tool is a separate package (`@buoy-gg/network`, `@buoy-gg/storage`, and so on) that registers itself in the menu once installed, so you only ship the tools you actually use.

### Do I have to configure each tool after installing it?

No. Auto-discovery finds installed tool packages and adds them to the floating menu with no wiring. Only tools that need to reach into your app — passing your Zustand stores, or wiring impersonation to your user-search API — take extra props.
