---
title: Installation
id: installation
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

- **Buoy Desktop** — a full dashboard for macOS, Windows & Linux. [Download it](https://buoy.gg/pricing), launch it, and point your app's sync at `http://localhost:42831`. See [Buoy Desktop](./desktop).
- **AI / MCP Server** — drive your app from Claude Code, Cursor, or any MCP editor:

```bash
npx -y @buoy-gg/mcp@latest init
```

See [AI / MCP Server](./mcp) for the full setup.

## TypeScript Support

All packages include TypeScript definitions out of the box. No additional `@types` packages needed.

## Next Steps

- [Quick Start](./quick-start) — Basic setup guide
- [FloatingDevTools](./floating-devtools) — Configuration options
- [Buoy Desktop](./desktop) — The full desktop dashboard
- [AI / MCP Server](./mcp) — Drive your app from your AI editor
- [Custom Tools](./custom-tools) — Build your own debugging tools
