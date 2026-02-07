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

A license key is required for all plans, including free. Add `Buoy.init()` at the entry point of your app (e.g. `App.tsx` or `index.tsx`) before any components render:

```tsx
import { Buoy } from "@buoy-gg/core";

Buoy.init({
  licenseKey: "YOUR_LICENSE_KEY",
});
```

This validates your key and caches it locally for 30 days. Don't have a key yet? Grab one at [buoy.gg/pricing](https://buoy.gg/pricing).

## TypeScript Support

All packages include TypeScript definitions out of the box. No additional `@types` packages needed.

## Next Steps

- [Quick Start](./quick-start) — Basic setup guide
- [FloatingDevTools](./floating-devtools) — Configuration options
- [Custom Tools](./custom-tools) — Build your own debugging tools
