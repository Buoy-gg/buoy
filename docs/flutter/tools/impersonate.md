---
title: Impersonate Tool
seoTitle: "Flutter User Impersonation — test as any user, no rebuilds"
id: flutter-tools-impersonate
description: "Test your Flutter app as any user by injecting impersonation headers into your network requests — no logging out or switching accounts required."
---

Configure an authorized user-search API and attach the current impersonation headers to requests sent to your backend. Select a test user, verify the header in Network, then stop impersonation and confirm the header is removed.

Your backend must authenticate the operator and authorize the target user. Adding a header does not grant permission.

<!-- ::impersonate-live-demo -->

## Installation

<!-- ::pub package="buoy_impersonate" -->

Unlike other Buoy tools, Impersonate requires configuration because it needs to integrate with your user search API. Using the [`buoy` umbrella](../installation)? The dial entry is already registered — still call `registerBuoyImpersonate(...)` to wire search.

---

## Quick Start

```dart
import 'package:buoy_impersonate/buoy_impersonate.dart';

registerBuoyImpersonate(
  onSearchUsers: (query) async => api.searchUsers(query), // List<ImpersonateUser>
);
```

The snippets assume your app defines `api.searchUsers` and a Dio instance named `dio`. Add the interceptor only to the client for your authorized backend, not to a shared client that also sends requests to third-party services:

```dart
import 'package:dio/dio.dart';

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

> **Security Note:** Your backend should validate that the authenticated user has admin/impersonation permissions before honoring this header.

---

## Features

### User Search
Search for users by email, name, or ID. Results display in a clean card format showing user details and metadata. When viewing an active impersonation, the card shows a control to stop directly from the search results.

### Impersonation History
Quick-switch between recently impersonated users. History persists across app restarts. Each history entry shows when the user was last impersonated.

### Data Clearing
Pass optional clearing callbacks so switching users clears stale client state:

| Callback | Typical use |
|----------|-------------|
| `onClearReactQuery` | Invalidate Riverpod/query-style caches |
| `onClearRedux` | Reset a global store |
| `onClearAsyncStorage` | Clear `shared_preferences` (preserve Buoy keys yourself if needed) |
| `onClearMMKV` | Clear an MMKV / secure backend |

Unlike React Native, Flutter has no runtime auto-detection — “configured” means you passed the callback.

### Floating Banner
A floating banner appears when impersonation is active, to identify the selected user. Toggle it in Settings.

---

## Configuration

### Required: User Search

`onSearchUsers` should return `List<ImpersonateUser>`:

```dart
class ImpersonateUser {
  final String id;           // Sent in the impersonation header
  final String? displayName; // Shown in UI (falls back to email, then id)
  final String? email;
  final String? avatarUrl;
  final Map<String, Object?>? metadata;
}
```

### Defaults

Customize the header key and related defaults:

```dart
registerBuoyImpersonate(
  onSearchUsers: api.searchUsers,
  defaults: const ImpersonateDefaults(
    headerKey: 'x-admin-impersonate',
  ),
);
```

Read the live map anywhere with `BuoyImpersonate.instance.impersonationHeaders`.

---

## What's Next

- [Network Monitor](./network) — Inspect headers on captured requests
- [Storage Explorer](./storage) — Inspect the impersonated user's persisted state
- [Events Timeline](./events) — The whole flow in one stream

---

## FAQ

### How do I test a Flutter app as another user without logging out?

Register `buoy_impersonate` with your user-search callback, pick a user in the tool, and attach `BuoyImpersonate.instance.impersonationHeaders` in your HTTP client — a dio interceptor, for example. The state persists across launches and mirrors live to Buoy Desktop.

### Does impersonation bypass my authentication?

No. Dart has no global `fetch` to patch, so you decide where the headers go, and your backend decides what impersonation means and who is allowed to use it. Buoy is the switch, not the authority.
