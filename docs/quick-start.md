---
title: Quick Start
id: quick-start
---

From zero to debugging in under 2 minutes.

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
      <FloatingDevTools environment="local" licenseKey="YOUR_LICENSE_KEY" />
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
    environment="local"
    licenseKey="YOUR_LICENSE_KEY"
    zustandStores={stores}
  />
);
```

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
          environment={__DEV__ ? "local" : "production"}
          licenseKey="YOUR_LICENSE_KEY"
          userRole={user?.role}
        />
      )}
    </>
  );
}
```

Or keep it available for everyone — your QA and support teams will thank you.

## What's next

- [FloatingDevTools](./floating-devtools) — Core component reference
- [Custom Tools](./custom-tools) — Build your own debugging tools
