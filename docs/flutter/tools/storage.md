---
title: Storage Explorer
seoTitle: "Flutter shared_preferences Viewer — browse storage on-device"
id: flutter-tools-storage
description: "Browse and edit every key-value pair your Flutter app persists — shared_preferences in one explorer with real-time updates and an event stream of every write."
---

Inspect shared_preferences values and supported registered backends. Edit a disposable test key, read it back through your app, and remove it after checking the integration.

<!-- ::tool-film id="storage" -->

The film and the demo show the React Native tool, and the demo uses mock data. Use the Flutter setup and feature descriptions below for supported behavior; the demo does not establish Flutter feature parity.

<!-- ::storage-live-demo -->

## Supported Backends

| Backend | Notes |
| --- | --- |
| `shared_preferences` | detected automatically, live browse + edit |
| Secure / MMKV backends | Require adapters implementing the package backend interfaces and explicit registration; they are not discovered from package installation alone |

---

## Installation

<!-- ::pub package="buoy_storage" -->

Using the [`buoy` umbrella](../installation)? It's already included. Standalone:

```dart
import 'package:flutter/foundation.dart';
import 'package:flutter/widgets.dart';
import 'package:buoy_storage/buoy_storage.dart';

void main() {
  if (kDebugMode) registerBuoyStorage();
  runApp(const MyApp());
}
```

> **Live monitoring** — `shared_preferences` has no change stream, so Buoy watches writes made through the app *and* re-scans on an interval, so changes visible to the configured backend appear on a later scan. Polling can miss intermediate writes and does not provide an atomic cross-process history.

---

## Required keys

Pass required keys when registering Storage. The same declarations validate the
local browser and reach Desktop through the `getRequiredKeys` action.

```dart
if (kDebugMode) {
  registerBuoyStorage(requiredKeys: const [
    RequiredStorageKey(
      key: 'theme',
      storageType: 'async',
      expectedValue: 'dark',
    ),
    RequiredStorageKey(
      key: 'auth_token',
      storageType: 'secure',
      expectedType: 'string',
    ),
  ]);
}
```

Use `async`, `secure`, or `mmkv` for `storageType`. Omitting it applies the
declaration to every registered backend. Checks distinguish missing keys, wrong
values, and wrong types. A required secure key can be read by name even when it
is absent from the backend's key list. Keys marked `requireAuthentication` in
that list remain unread and are shown as protected.

Calling `registerBuoyStorage()` again without `requiredKeys` preserves the
configuration, including when the umbrella registers the tool. Pass an empty
list to clear it.

## What You Can Do

<!-- ::storage-actions-grid -->

---

## What's Next

- [Network Monitor](./network) — Inspect supported HTTP requests
- [Environment Inspector](./env) — Validate env vars with type checking
- [Events Timeline](./events) — Storage writes alongside network and route events

---

## FAQ

### How do I view shared_preferences values in a Flutter app?

Add `buoy_storage` and call `registerBuoyStorage()` — every key-value pair is browsable and editable on the device, with an event stream of every write and a diff of what changed.

### Will it show writes made outside my own code?

`shared_preferences` has no change stream, so Buoy watches writes made through the app *and* re-scans on an interval — changes visible to the configured backend appear on a later scan. Polling can miss intermediate writes and does not provide an atomic cross-process history.
