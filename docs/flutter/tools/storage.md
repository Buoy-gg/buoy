---
title: Storage Explorer
seoTitle: "Flutter shared_preferences Viewer — browse storage on-device"
id: flutter-tools-storage
description: "Browse and edit every key-value pair your Flutter app persists — shared_preferences in one explorer with real-time updates and an event stream of every write."
---

Inspect shared_preferences values and supported registered backends. Edit a disposable test key, read it back through your app, and remove it after checking the integration.

The demo shows the React Native tool with mock data. Use the Flutter setup and feature descriptions below for supported behavior; the demo does not establish Flutter feature parity.

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
