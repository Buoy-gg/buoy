# @buoy-gg/env

[![npm version](https://img.shields.io/npm/v/@buoy-gg/env?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/env) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/env?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/env)

Inspect the environment values available to your app at runtime and check required variables against expected types and values.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

![Environment Inspector demo](https://github.com/user-attachments/assets/75651046-33a0-4257-9011-3bcc4818a964)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/env
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

After initialization, render the menu in your app root:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools />
    </>
  );
}
```

To validate required variables, build the tool with `createEnvTool` and the fluent `envVar` builder, then pass it via `apps={[...]}`:

```tsx
import { createEnvTool, envVar } from "@buoy-gg/env";

const envTool = createEnvTool({
  requiredEnvVars: [
    envVar("EXPO_PUBLIC_API_URL").exists(),
    envVar("EXPO_PUBLIC_DEBUG_MODE").withType("boolean").build(),
    envVar("EXPO_PUBLIC_ENVIRONMENT").withValue("development").build(),
    envVar("EXPO_PUBLIC_MAX_RETRIES")
      .withType("number")
      .withDescription("Maximum API retry attempts")
      .build(),
  ],
});

// Render this menu inside your existing app root.
<FloatingDevTools apps={[envTool]} />;
```

## Check the integration

Open Env and check one known variable and one deliberately missing required variable. Expo statically replaces direct `process.env.EXPO_PUBLIC_NAME` references; enumerating `process.env` does not guarantee that those values are available. An empty list does not prove your app has no environment configuration.

## What you get

- **Runtime discovery** — collects enumerable `EXPO_PUBLIC_` values available at runtime. Expo’s static inlining limits what enumeration can see.
- **Required-variable validation** — declare which vars must exist, with expected values or types: `string`, `number`, `boolean`, `array`, `object`, `url`.
- **Type detection** — values are auto-classified (string, number, boolean, array, object, url, json).
- **Health score** — a 0-100% health percentage with HEALTHY / WARNING / ERROR / CRITICAL states, plus counts for total, required, missing, and wrong value/type.
- **Per-variable status** — green (present and correct), yellow (wrong value or type), red (required but missing).
- **Search and filters** — real-time search plus "All", "Missing", and "Issues" filters.
- **Copy to clipboard** — copy any value with one tap.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/env) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

Supply public runtime values explicitly with setRemoteEnv. The inspector cannot enumerate variables that a bundler replaced at build time. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
