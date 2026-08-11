---
title: Storage Explorer
seoTitle: "Storage Explorer — setup, config & API"
id: flutter-tools-storage
description: "Browse and edit every key-value pair your Flutter app persists — shared_preferences in one explorer with real-time updates and an event stream of every write."
---

Browse, edit, and manage all your app's persisted data. See every key-value pair in real-time, with an event stream of every write and diffs of what changed.

Don't take our word for it — this is the real tool UI, running here on mock storage. Walk the guided tour, or skip it and start tapping:

<!-- ::storage-live-demo -->

## Supported Backends

| Backend | Notes |
| --- | --- |
| `shared_preferences` | detected automatically, live browse + edit |
| Secure / MMKV backends | optional — registered the same way |

---

## Installation

<!-- ::pub package="buoy_storage" -->

Using the [`buoy` umbrella](../installation)? It's already included. Standalone:

```dart
import 'package:buoy_storage/buoy_storage.dart';

void main() {
  if (kDebugMode) registerBuoyStorage();
  runApp(const MyApp());
}
```

> **Live monitoring** — `shared_preferences` has no change stream, so Buoy watches writes made through the app *and* re-scans on an interval, so changes from anywhere still show up.

---

## What You Can Do

<!-- ::storage-actions-grid -->

---

## What's Next

- [Network Monitor](./network) — See every API call your app makes
- [Environment Inspector](./env) — Validate env vars with type checking
- [Events Timeline](./events) — Storage writes alongside network and route events

---

*Looking for an overview with screenshots and FAQs? See the [Storage Explorer page on buoy.gg](https://buoy.gg/tools/storage).*
