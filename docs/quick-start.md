---
title: Quick Start
seoTitle: "React Native DevTools Setup — install Buoy in minutes"
id: quick-start
description: "Get React Buoy's floating in-app devtools menu running in your React Native or Expo app in minutes, then reach the same tools from desktop or an AI agent."
---

Get the in-app menu running in under 2 minutes — then reach the same tools from your desktop or your AI agent.

## 1. Install the core

<!-- ::pm npm="npm install @buoy-gg/core" yarn="yarn add @buoy-gg/core" pnpm="pnpm add @buoy-gg/core" bun="bun add @buoy-gg/core" -->

## 2. Add to your app

Drop `FloatingDevTools` at the root of your app:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools />
    </>
  );
}
```

A floating button appears in the corner of your app. Tap it to open the menu.

Every tool works with no key at all, capped at about five entries each — enough
to see what they do. A free key raises that to 25 per tool and includes Pro free
every weekend; Pro unlocks everything: production builds, the MCP server, and
unlimited capture. See [pricing](https://buoy.gg/pricing).

Grab your key — free or paid, same command:

```bash
npx buoy login
```

> Run this from a project where `@buoy-gg/core` is installed — `npx` resolves
> the command from your own `node_modules`. If npx tries to download something
> instead (there is an unrelated `buoy` package on npm), name the package
> explicitly:
>
> ```bash
> npx --package=@buoy-gg/core buoy login
> ```

It signs you in, writes the key to `.env.local`, and gitignores it. Then:

```tsx
Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });
```

See [Installation](./installation#get-your-key) for the details.

## 3. Add tools

Install any tool package — it automatically appears in the menu. No wiring, no config.

<!-- ::pm npm="npm install @buoy-gg/network" yarn="yarn add @buoy-gg/network" pnpm="pnpm add @buoy-gg/network" bun="bun add @buoy-gg/network" -->

That's it. Open the menu, tap Network, and you're watching every API call in real-time.

### Zustand stores

If you use Zustand, pass your stores directly via `zustandStores`:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { useAuthStore } from "./stores/auth";
import { useCartStore } from "./stores/cart";

const stores = {
  authStore: useAuthStore,
  cartStore: useCartStore,
};

return (
  <FloatingDevTools zustandStores={stores} />
);
```

### Jotai atoms

If you use Jotai, call `watchAtoms` once at module scope with your store and a named map of atoms:

```tsx
import { getDefaultStore } from "jotai";
import { watchAtoms } from "@buoy-gg/jotai";
import { authAtom } from "./atoms/auth";
import { cartAtom } from "./atoms/cart";

watchAtoms(getDefaultStore(), {
  authAtom,
  cartAtom,
});
```

No wrappers, no middleware. Registered atoms automatically appear in the Jotai tool inside your FloatingDevTools menu.

## Available tools

<!-- ::tools-table -->

Install what you need. Skip what you don't.

## Control who sees devtools

Only show devtools to specific users — admins, QA, internal team members, or whoever your business needs:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  const { user } = useAuth();

  // Only render for internal users, admins, or QA
  const showDevTools =
    user?.role === "admin" ||
    user?.role === "qa" ||
    user?.email?.endsWith("@yourcompany.com");

  return (
    <>
      <YourApp />
      {showDevTools && (
        <FloatingDevTools
          licenseKey="YOUR_LICENSE_KEY"
          userRole={user?.role}
        />
      )}
    </>
  );
}
```

Or keep it available for everyone — your QA and support teams will thank you.

## Take it further

The tools you just installed aren't only in the floating menu — reach the same live app two more ways:

- **[Buoy Desktop](./desktop)** — mirror every tool to a full dashboard on macOS, Windows, or Linux, with a live performance HUD and multi-device switching.
- **[AI / MCP Server](./mcp)** — let Claude Code, Cursor, or any MCP editor inspect and control your running app. One command to wire it up:

```bash
npx -y @buoy-gg/mcp@latest init
```

Buoy Desktop is free to use; the MCP server is a Pro feature. Both talk to your app through one extra package — `npm install @buoy-gg/external-sync` in the app and restart Metro with `--clear` — and from there the connection is automatic, with no URLs to configure (physical devices included).

## What's next

- [FloatingDevTools](./floating-devtools) — Core component reference
- [Buoy Desktop](./desktop) — The full desktop dashboard
- [AI / MCP Server](./mcp) — Drive your app from your AI editor
- [Custom Tools](./custom-tools) — Build your own debugging tools

---

## FAQ

### Do I need a license key to use React Buoy?

No — every tool works without one, capped at about five entries each. A free key
raises that to 25 per tool and includes Pro free every weekend. Pro unlocks
everything: production builds, the MCP server, and unlimited capture.

The fastest way to get either is `npx buoy login`. A free key needs no card and
takes about thirty seconds.

### Does Buoy phone home?

Once a day, in development only, it sends a random install id, the Buoy version,
your platform, and your license tier. Never your project, your app, or anything
from the tools — that data never leaves your machine. Turn it off with
`Buoy.init({ telemetry: false })`. Full details: [Telemetry](./telemetry).

### How do I add a tool to the menu?

Install the package. Auto-discovery finds it and the tool appears in the floating menu with no wiring and no config — `npm install @buoy-gg/network`, open the menu, tap Network.

### Will the devtools ship to my users?

The menu only renders where you mount `FloatingDevTools`, and desktop sync is off whenever `__DEV__` is false unless you opt in explicitly with a Pro license — so a shipped app never dials a broker on a customer's phone.
