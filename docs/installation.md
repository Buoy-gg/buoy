---
title: Installation
id: installation
description: "Step-by-step guide to installing React Buoy devtools in a React Native or Expo app — requirements, core package setup, and picking your first tools."
---

Get Buoy running in your React Native app in minutes.

## Requirements

<!-- ::Requirements -->

## Quick Start

Install the core package and any tools you need:

<!-- ::Quick-Install -->

## Available Packages

Each package adds a new tool to your floating menu. Install only what you need.

<!-- ::Tool-Packages -->

## Register Your License Key

A license key is required for all plans, including free. Pass it as a prop to `FloatingDevTools`:

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

Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

## Desktop & AI (optional)

The packages above power the in-app floating menu. Two more surfaces connect to the same app — both Buoy Pro:

- **Buoy Desktop** — a full dashboard for macOS, Windows & Linux. [Download it](https://buoy.gg/pricing) and launch it — your app finds it automatically (the broker address is derived from Metro, so physical devices work zero-config too). See [Buoy Desktop](./desktop).
- **AI / MCP Server** — drive your app from Claude Code, Cursor, or any MCP editor:

```bash
npx -y @buoy-gg/mcp@latest init
```

See [AI / MCP Server](./mcp) for the full setup.

## TypeScript Support

All packages include TypeScript definitions out of the box. No additional `@types` packages needed.

## Monorepos & Enterprise Setups

Buoy is built to survive locked-down corporate React Native apps:

- **After installing a new `@buoy-gg` package, restart Metro with `--clear`.** Metro caches the "optional package missing" resolution — a plain reload never picks the new package up. This is the single most common "I installed it and nothing happened" cause.
- **`unstable_enablePackageExports: false` works.** Big monorepos often disable Metro's package-exports resolution for legacy dependencies; Buoy's packages ship legacy resolution shims so they resolve either way.
- **Physical devices work zero-config.** The desktop-sync broker address is derived from the Metro host, so devices on the same Wi-Fi find your machine automatically; `socketURL` overrides it for tunnels or `adb reverse` USB setups.
- **Scoped registries** — all packages live under the `@buoy-gg` scope, so a `.npmrc` scope rule (`@buoy-gg:registry=…`) is all a proxy registry needs.
- **No on-device UI for end users** — pass `headless` to `FloatingDevTools` for builds where only the desktop dashboard should see the session. See [FloatingDevTools](./floating-devtools).

## Next Steps

- [Quick Start](./quick-start) — Basic setup guide
- [FloatingDevTools](./floating-devtools) — Configuration options
- [Buoy Desktop](./desktop) — The full desktop dashboard
- [AI / MCP Server](./mcp) — Drive your app from your AI editor
- [Custom Tools](./custom-tools) — Build your own debugging tools
