---
title: Route Inspector
seoTitle: "Flutter go_router Debugger — routes, stack & events on-device"
id: flutter-tools-routes
description: "Browse every go_router route in your Flutter app, jump to any screen, and watch navigation events in real time to debug navigation issues fast."
---

See every route in your app and track navigation in real-time. Browse your sitemap, jump to any screen, and debug navigation issues instantly.

The React Native build of this tool, running here on mock data. The Flutter port ships the same panels — walk the tour, or skip it and start tapping.

<!-- ::routes-live-demo -->

## Supported Libraries

Captures navigation from [`go_router`](https://pub.dev/packages/go_router) — the events timeline, route sitemap, and navigation-stack view, identical to the React Native tool.

---

## Installation

<!-- ::pub package="buoy_routes" -->

Add `BuoyRouteObserver.instance` to your router's `observers` and hand the router to `registerBuoyRoutes` so the sitemap and jump-to-route work:

```dart
import 'package:buoy_routes/buoy_routes.dart';
import 'package:go_router/go_router.dart';

final _router = GoRouter(
  observers: [BuoyRouteObserver.instance],
  routes: [ /* ... */ ],
);

void main() {
  if (kDebugMode) registerBuoyRoutes(router: _router);
  runApp(const MyApp());
}
```

---

## What You Can Do

<!-- ::route-features-grid -->

---

## Event Timeline

Every navigation is tracked with:

- **Path** — Where you navigated to
- **Params** — Route parameters passed
- **Timestamp** — When it happened
- **Duration** — Time since previous navigation

Tap any event to open its **detail page** — the full route template, from/to paths, timing, segments, and params, all copyable, plus a **Go to route** action to jump straight there.

---

## What's Next

- [Network Monitor](./network) — See every API call your app makes
- [Storage Explorer](./storage) — Browse and edit shared_preferences
- [Events Timeline](./events) — Navigation alongside network and storage events

---

## FAQ

### How do I debug go_router navigation in Flutter?

Add `BuoyRouteObserver.instance` to your router's `observers` and pass the router to `registerBuoyRoutes` — you get the route sitemap, the live navigation stack, jump-to-any-screen, and a real-time stream of navigation events on the device.

### Which routing packages are supported?

`go_router` — the events timeline, route sitemap, and navigation-stack view, identical to the React Native tool.
