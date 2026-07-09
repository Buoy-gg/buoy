# @buoy-gg/env

[![npm version](https://img.shields.io/npm/v/@buoy-gg/env?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/env) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/env?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/env)

**View and validate your app's environment variables on-device — auto-discovers `EXPO_PUBLIC_` vars with type detection and a 0-100% health score.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

![Environment Inspector demo](https://github.com/user-attachments/assets/75651046-33a0-4257-9011-3bcc4818a964)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/env
```

## Quick start

Fully automatic. Render the floating menu once — the Environment Inspector is auto-discovered as soon as this package is installed:

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
```

## What you get

- **Automatic discovery** — every `EXPO_PUBLIC_`-prefixed variable is collected with zero configuration.
- **Required-variable validation** — declare which vars must exist, with expected values or types: `string`, `number`, `boolean`, `array`, `object`, `url`.
- **Type detection** — values are auto-classified (string, number, boolean, array, object, url, json).
- **Health score** — a 0-100% health percentage with HEALTHY / WARNING / ERROR / CRITICAL states, plus counts for total, required, missing, and wrong value/type.
- **Per-variable status** — green (present and correct), yellow (wrong value or type), red (required but missing).
- **Search and filters** — real-time search plus "All", "Missing", and "Issues" filters.
- **Copy to clipboard** — copy any value with one tap.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/env) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
