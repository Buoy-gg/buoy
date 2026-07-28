---
title: Riverpod Inspector
id: flutter-tools-riverpod
description: "Inspect Riverpod providers in your Flutter app — watch state changes, browse live provider values, and explore value diffs in real time, right on the device."
---

Full Riverpod provider inspection for Flutter. Monitor provider state changes, explore value diffs, and browse live provider values in real-time — directly on your device. It's the same inspector UI as the React Native atom inspector, reading your Riverpod providers.

<!-- ::jotai-demo -->

## Installation

```sh
flutter pub add buoy_riverpod
```

---

## Setup

Add the Buoy observer to your `ProviderScope` — no wrappers, no middleware, no modifications to existing providers:

```dart
import 'package:buoy_riverpod/buoy_riverpod.dart';

void main() {
  registerBuoyRiverpod(); // or use the `buoy` umbrella widget
  runApp(
    ProviderScope(
      observers: const [buoyRiverpodObserver],
      child: const MyApp(),
    ),
  );
}
```

> **Name your providers** (`StateProvider(..., name: 'counter')`) so the list reads well — unnamed providers fall back to their runtime type.

---

## Providers Tab

Browse all observed providers and their **live current value**:

- **Provider Name** — Color-coded for easy identification across both tabs
- **Value Type** — `number`, `boolean`, `object`, `array · N`, `null`, etc. at a glance
- **Live Value** — Tap any provider to expand and see the full value tree, updated in real-time
- **Change Count** — How many times this provider has changed this session
- **View History** — Jump straight to the filtered event history for a single provider

---

## Events Tab

Every state change is captured with rich metadata:

- **Provider Name** — Which provider changed, color-coded for quick identification
- **Value Transition** — `prev → next` at a glance (e.g. `0 → 5`, `null → {name, email}`)
- **Timestamp** — When the change occurred with relative time

Tap any event for the detail view: full value trees and a split-screen **diff** — additions in green, removals in red — so you see exactly what changed in complex state.

---

## What's Next

- [Events Timeline](./events) — Provider changes alongside network and route events
- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit persisted data
