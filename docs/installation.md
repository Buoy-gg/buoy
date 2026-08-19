---
title: Installation
seoTitle: "Install React Native DevTools — @buoy-gg/core setup guide"
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

Buoy runs three ways, and a key is what moves you up:

| | Key | What you get |
| --- | --- | --- |
| **No key** | none | Every tool, capped at ~5 entries each — enough to see what they do. |
| **Free** | `npx buoy login` (no card) | 25 entries per tool, plus Pro free every weekend. |
| **Pro** | paid key | Unlimited capture, production builds, the MCP server. |

Every tool is available on every tier — the caps change, the tool list doesn't.

### Get your key

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

Opens your browser, signs you in, and writes the key to `.env.local` (adding it
to `.gitignore` if it isn't already). It picks the right variable name for your
setup — `EXPO_PUBLIC_BUOY_KEY` on Expo, `BUOY_KEY` on bare React Native — which
matters, because Expo only inlines `EXPO_PUBLIC_`-prefixed variables into the
bundle. It works the same whether your account holds a free key or a paid one.

Then read it in your app:

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

Restart your bundler with its cache cleared so the new variable is picked up:

```bash
npx expo start --clear
```

The `--clear` matters. Expo inlines `EXPO_PUBLIC_*` variables into the bundle at
transform time, and Metro caches transforms keyed on the *source* file — so
changing `.env.local` alone leaves the old value baked in, and a plain restart
silently keeps using it.

### Or pass it directly

```tsx
<FloatingDevTools licenseKey="YOUR_LICENSE_KEY" />
```

Fine for a solo project. On a team, prefer the env var: a key committed to a
shared repo is shared by everyone who clones it, so it stops identifying a
person and starts identifying a repository.

Don't have a key yet? A free one comes with an account — `npx buoy login` will
create it for you, or grab it from [buoy.gg/pricing](https://buoy.gg/pricing).

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
- **Physical devices work zero-config.** The desktop-sync broker address is derived from the Metro host, so devices on the same Wi-Fi find your machine automatically. Android over USB needs one command — `adb reverse tcp:42831 tcp:42831` — and `socketURL` overrides everything for tunnels or a broker on another machine.
- **Scoped registries** — all packages live under the `@buoy-gg` scope, so a `.npmrc` scope rule (`@buoy-gg:registry=…`) is all a proxy registry needs.
- **No on-device UI for end users** — pass `headless` to `FloatingDevTools` for builds where only the desktop dashboard should see the session. See [FloatingDevTools](./floating-devtools).

## Next Steps

- [Quick Start](./quick-start) — Basic setup guide
- [FloatingDevTools](./floating-devtools) — Configuration options
- [Buoy Desktop](./desktop) — The full desktop dashboard
- [AI / MCP Server](./mcp) — Drive your app from your AI editor
- [Custom Tools](./custom-tools) — Build your own debugging tools

---

## FAQ

### Which React Buoy package do I install first?

`@buoy-gg/core` — it renders the floating menu. Every tool is a separate package (`@buoy-gg/network`, `@buoy-gg/storage`, and so on) that registers itself in the menu once installed, so you only ship the tools you actually use.

### Do I have to configure each tool after installing it?

No. Auto-discovery finds installed tool packages and adds them to the floating menu with no wiring. Only tools that need to reach into your app — passing your Zustand stores, or wiring impersonation to your user-search API — take extra props.
