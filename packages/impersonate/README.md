# @buoy-gg/impersonate

[![npm version](https://img.shields.io/npm/v/@buoy-gg/impersonate?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/impersonate) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/impersonate?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/impersonate)

Connect your app’s user-switching flow to Buoy so testers can switch test accounts, roles, or flags without rebuilding.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

## Install

```bash
npm install @buoy-gg/core @buoy-gg/impersonate
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

Unlike most Buoy tools, this one needs a little config — it has to know how to search *your* users:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { createImpersonateTool } from "@buoy-gg/impersonate";

const impersonateTool = createImpersonateTool({
  onSearchUsers: async (query) => {
    const { users } = await api.searchUsers({ email: query });
    return users.map((u) => ({
      id: u.id, // sent in the impersonation header
      displayName: u.name,
      email: u.email,
      metadata: { role: u.role },
    }));
  },
});

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools apps={[impersonateTool]} />
    </>
  );
}
```

While impersonating, requests passing through the patched global `fetch` and `XMLHttpRequest` carries `x-impersonate-user-id: <id>` (header name configurable via `defaults.headerKey`). Your backend checks the header — after verifying the caller is an admin — and returns data for that user instead.

## Check the integration

Switch between two test accounts and confirm both the visible identity and subsequent authenticated requests. Your backend must authorize the switch. Redux clearing requires a reducer that handles `@@RESET` or your own reset callback; Buoy cannot infer your app’s reset behavior.

## What you get

- **Header injection** — adds the configured header through patched global fetch and XHR paths while impersonation is active
- **Data clearing** — supports query-cache clearing and Redux reset integration. Your reducer must handle `@@RESET`, or provide a reset callback. Storage clearing uses optional callbacks
- **Floating banner** — identifies the active impersonation when the banner is enabled
- **Pause vs stop** — the banner's power button pauses header injection for instant A/B against your real account; X stops the session and triggers data clearing
- **History** — quick-switch between your last 10 impersonated users, persisted across app restarts
- **Developer defaults** — ship team-wide defaults for header name, banner, and clearing behavior; the Settings tab exports your current config as paste-ready code
- **`showSettingsTab: false`** — lock it down to just Search and History for simpler setups

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/impersonate) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
