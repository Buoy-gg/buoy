---
title: Environment Inspector
seoTitle: "React Native Environment Variables Debugger — validate on-device"
id: tools-env
description: "View and validate your React Native app's environment variables on-device — checks runtime-visible values against declared requirements."
---

<!-- ::platform-badge platform="both" -->

Inspect environment values available to the running JavaScript process and validate them against required names, types, and expected values.

Expo's build-time substitution does not make every `EXPO_PUBLIC_` variable enumerable at runtime. A value missing from this tool is not proof that it is missing from your app's bundle. See the limitations below.

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

Mount the configured preset through `apps`; calling the factory alone does not change the discovered tool:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

<FloatingDevTools apps={[envTool]} />
```

This fragment assumes you have already configured your Buoy account and mounted the menu as described in [Quick Start](../quick-start).

## The `envVar` Builder

Use the builder to declare a requirement:

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
type EnvToolOptions = {
  name?: string;                    // default: "ENV"
  description?: string;
  colorPreset?: "orange" | "cyan" | "purple" | "pink" | "yellow" | "green"; // default: "green"
  id?: string;                      // default: "env"
  requiredEnvVars?: RequiredEnvVar[];
  enableSharedModalDimensions?: boolean;
};
```

## Features

- **Automatic Discovery** - Collects runtime-visible `EXPO_PUBLIC_` variables
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

Discovery reads the runtime `process.env` object and filters for `EXPO_PUBLIC_` names. Declaring a required name adds a validation target; it does not inject a value into the runtime.

[Expo substitutes statically referenced environment values](https://docs.expo.dev/guides/environment-variables/) during bundling. Runtime enumeration cannot reliably recover those substitutions. Check a known value in your actual build before using this inspector as a completeness check.

The tool does not read your `.env` file or change your build configuration. Apply environment changes through your app's normal build or update process.

## FAQ

### How do I check which env vars my Expo app actually loaded?

Install `@buoy-gg/env` and open the Env tool — it shows runtime-visible values. Expo-inlined values may be absent; check the limitations above.

### Can it tell me why an env var is wrong, not just missing?

Yes — declare expected types and values, and failures show the current value, expected value, and context.

## Web support (unreleased)

Supply public runtime values explicitly with setRemoteEnv. The inspector cannot enumerate variables that a bundler replaced at build time. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
