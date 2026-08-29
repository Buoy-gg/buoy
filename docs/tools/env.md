---
title: Environment Inspector
seoTitle: "React Native Environment Variables Debugger — validate on-device"
id: tools-env
description: "View and validate your React Native app's environment variables on-device — auto-discovers EXPO_PUBLIC_ vars with type detection and health monitoring."
---

<!-- ::platform-badge platform="both" -->

An env var bug never shows up where you look for it. The `.env` file is right, the CI secret is set, and the build still ships with `EXPO_PUBLIC_API_URL` pointing at staging — because what matters is what got inlined into the bundle, and nothing on your machine can tell you that.

The Environment Inspector reads the values the running build actually resolved, on the device, and checks them against a contract you declare in code. You find out on the TestFlight build instead of from a support ticket.

Vars stream in as discovered, a declared contract lights up what never shipped, you read exactly what's wrong, compare two builds' health, and land the fix.

<!-- ::env-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/env" yarn="yarn add @buoy-gg/env" pnpm="pnpm add @buoy-gg/env" bun="bun add @buoy-gg/env" -->

After installation, the Environment Inspector will be auto-detected and appear in your FloatingDevTools menu.

## Custom Configuration

For more control, use `createEnvTool` with the `envVar` builder to define required variables and validation rules:

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

## The `envVar` Builder

The fluent builder API makes it easy to define environment variable requirements:

```tsx
envVar("API_KEY")
  .withType("string")           // Set expected type
  .withValue("sk_test_123")     // Or set expected value
  .withDescription("API Key")   // Add documentation
  .build()                      // Finalize config

// Shorthand for just checking existence
envVar("API_KEY").exists()
```

### Supported Types

```typescript
type EnvVarType = "string" | "number" | "boolean" | "array" | "object" | "url";
```

## `createEnvTool` Options

```typescript
createEnvTool({
  name?: string;                    // default: "ENV"
  description?: string;
  colorPreset?: "orange" | "cyan" | "purple" | "pink" | "yellow" | "green"; // default: "green"
  id?: string;                      // default: "env"
  requiredEnvVars?: RequiredEnvVar[];
  enableSharedModalDimensions?: boolean;
});
```

## Features

- **Automatic Discovery** - Auto-collects all `EXPO_PUBLIC_` prefixed variables
- **Required Variable Validation** - Define which vars must exist with expected values/types
- **Type Detection** - Auto-detects: string, number, boolean, array, object, url, json
- **Search & Filtering** - Real-time search + filters for "All", "Missing", "Issues"
- **Health Status** - Health percentage (0-100%) with HEALTHY/WARNING/ERROR/CRITICAL states
- **Statistics** - Total count, required count, missing count, wrong value/type counts
- **Copy to Clipboard** - Copy any value with one tap

## Variable Status Types

| Status | Description |
|--------|-------------|
| `required_present` | Required var is set and correct |
| `required_missing` | Required var is not set |
| `required_wrong_value` | Set but doesn't match expected value |
| `required_wrong_type` | Set but wrong type |
| `optional_present` | Optional var that is set |

## Validation Visual Indicators

- **Green** - Variable exists and matches expected value/type
- **Yellow** - Variable exists but value/type differs from expected
- **Red** - Required variable is missing

## Try It Out

Use the interactive builder below to create your environment validation config. Add variables, configure checks (type, value, description), and export the code directly.

<!-- ::env-playground -->

---

## What It Can't Do

**Auto-discovery only sees `EXPO_PUBLIC_` variables.** Anything else has to be declared in your contract to appear — which is deliberate: a devtool that enumerated every value in scope would be a way to read secrets off a device.

**It reads the build, not your `.env` file.** The values shown are the ones inlined into the running bundle. That is the entire point — it is how you catch a build that shipped with the wrong value — but it means editing `.env` changes nothing until you rebuild.

## FAQ

### How do I check which env vars my Expo app actually loaded?

Install `@buoy-gg/env` and open the Env tool — it auto-discovers EXPO_PUBLIC_ variables and shows the value each one resolved to in the running build.

### Can it tell me why an env var is wrong, not just missing?

Yes — attach expected types and custom validators, and failures show the current value, expected value, and context.
