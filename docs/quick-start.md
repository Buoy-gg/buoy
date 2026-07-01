---
title: Quick Start
id: quick-start
---

Get the in-app menu running in under 2 minutes — then reach the same tools from your desktop or your AI agent.

## 1. Install the core

<!-- ::pm npm="npm install @buoy-gg/core" yarn="yarn add @buoy-gg/core" pnpm="pnpm add @buoy-gg/core" bun="bun add @buoy-gg/core" -->

## 2. Add to your app

Drop `FloatingDevTools` at the root of your app with your license key:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools licenseKey="YOUR_LICENSE_KEY" />
    </>
  );
}
```

A floating button appears in the corner of your app. Tap it to open the menu.

Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

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
  <FloatingDevTools
    licenseKey="YOUR_LICENSE_KEY"
    zustandStores={stores}
  />
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

Both are Buoy Pro features and connect to the same app you just set up.

## What's next

- [FloatingDevTools](./floating-devtools) — Core component reference
- [Buoy Desktop](./desktop) — The full desktop dashboard
- [AI / MCP Server](./mcp) — Drive your app from your AI editor
- [Custom Tools](./custom-tools) — Build your own debugging tools
