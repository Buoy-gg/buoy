---
title: Environment Inspector
seoTitle: "Flutter Environment Config Debugger — validate env on-device"
id: flutter-tools-env
description: "View and validate your Flutter app's environment config on-device — required-variable checks, type detection, per-variable status badges, and a health score."
---

The Environment Inspector lets you view and validate environment config in your Flutter app — required-variable checks, type detection, per-variable status badges, and a 0–100% health score.

The React Native build of this tool, running here on mock data. The Flutter port ships the same panels — walk the tour, or skip it and start tapping.

<!-- ::env-live-demo -->

## Installation

<!-- ::pub package="buoy_env" -->

## Registration

Flutter has no enumerable `process.env` — `--dart-define` values are compile-time and can't be listed at runtime. So instead of auto-discovery, you hand Buoy the map explicitly:

```dart
import 'package:buoy_env/buoy_env.dart';

registerBuoyEnv(
  vars: {
    'API_URL': const String.fromEnvironment('API_URL'),
    'ENVIRONMENT': const String.fromEnvironment('ENVIRONMENT'),
    // ...any runtime config strings
  },
  requiredEnvVars: [
    envVar('API_URL').withType('url').build(),
    RequiredEnvVar.value('ENVIRONMENT', 'production'),
    'FEATURE_FLAG', // existence check
  ],
);
```

## The `envVar` Builder

The same fluent builder API as React Native:

```dart
envVar('API_KEY')
  .withType('string')          // Set expected type
  .withValue('sk_test_123')    // Or set expected value
  .withDescription('API Key')  // Add documentation
  .build()                     // Finalize config

// Shorthand for just checking existence
envVar('API_KEY').exists()
```

### Supported Types

```dart
// 'string' | 'number' | 'boolean' | 'array' | 'object' | 'url'
```

## `registerBuoyEnv` Options

```dart
registerBuoyEnv({
  required Map<String, String?> vars,   // hand-built map (no process.env)
  List<Object /* String | RequiredEnvVar */>? requiredEnvVars,
});
```

Required entries can be a bare `String` (existence), `RequiredEnvVar.value(...)`, or an `envVar(...).build()`.

## Features

- **Required Variable Validation** — Define which vars must exist with expected values/types
- **Type Detection** — Auto-detects: string, number, boolean, array, object, url, json
- **Search & Filtering** — Real-time search + filters for "All", "Missing", "Issues"
- **Health Status** — Health percentage (0–100%) with HEALTHY/WARNING/ERROR/CRITICAL states
- **Statistics** — Total count, required count, missing count, wrong value/type counts
- **Copy to Clipboard** — Copy any value with one tap

## Variable Status Types

| Status | Description |
|--------|-------------|
| `required_present` | Required var is set and correct |
| `required_missing` | Required var is not set |
| `required_wrong_value` | Set but doesn't match expected value |
| `required_wrong_type` | Set but wrong type |
| `optional_present` | Optional var that is set |

## Validation Visual Indicators

- **Green** — Variable exists and matches expected value/type
- **Yellow** — Variable exists but value/type differs from expected
- **Red** — Required variable is missing

## What's Next

- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit shared_preferences
- [Console](./console) — Every log call in a DevTools-style panel

---

## FAQ

### How do I check which --dart-define values my Flutter build actually got?

Flutter has no enumerable `process.env`, and compile-time `--dart-define` values can't be listed at runtime — so you hand Buoy the map in `registerBuoyEnv(vars: {...})`. The inspector then shows the value each variable resolved to, with per-variable status badges and a 0–100% health score.

### Can it validate types and required values?

Yes — declare expectations with the `envVar` builder (`withType`, `withValue`, `withDescription`, or `exists` for a plain existence check). Supported types are string, number, boolean, array, object, and url, and anything missing or wrong is flagged.
