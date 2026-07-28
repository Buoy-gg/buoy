---
title: Impersonate Tool
id: flutter-tools-impersonate
description: "Test your Flutter app as any user by injecting impersonation headers into your network requests — no logging out or switching accounts required."
---

Admin user impersonation for Flutter. Test your app as any user by injecting impersonation headers into your network requests — without logging out or switching accounts. State persists across launches and mirrors live to Buoy Desktop.

## Installation

```sh
flutter pub add buoy_impersonate
```

Unlike other Buoy tools, Impersonate requires configuration because it needs to integrate with your user search API.

---

## Quick Start

```dart
import 'package:buoy_impersonate/buoy_impersonate.dart';

// Register with your app's user search + optional cache-clear callbacks.
registerBuoyImpersonate(
  onSearchUsers: (query) async => api.searchUsers(query), // returns List<ImpersonateUser>
  onClearCache: () => cache.clear(),
);
```

Dart has no global `fetch` to patch, so you apply the header in your HTTP client — e.g. a dio interceptor:

```dart
dio.interceptors.add(InterceptorsWrapper(onRequest: (options, handler) {
  options.headers.addAll(BuoyImpersonate.instance.impersonationHeaders);
  handler.next(options);
}));
```

---

## How It Works

When impersonation is active, `impersonationHeaders` contains:

```
x-impersonate-user-id: user_123
```

Your backend checks for this header and returns data for the specified user instead of the authenticated admin. Your backend decides what the headers mean — Buoy just makes toggling them one tap.

---

## What's Next

- [Network Monitor](./network) — Watch the headers on every request
- [Storage Explorer](./storage) — Inspect the impersonated user's persisted state
- [Events Timeline](./events) — The whole flow in one stream
