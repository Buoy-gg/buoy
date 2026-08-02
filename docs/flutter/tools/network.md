---
title: Network Monitor
id: flutter-tools-network
description: "Inspect every HTTP request your Flutter app makes — package:http, dio, and image loads with headers, bodies, timing, and errors — live on the device, no proxy required."
---

See every API call your app makes. Request, response, headers, timing, errors — all in real-time with zero configuration.

<!-- ::network-demo -->

## Supported Clients

Everything riding `dart:io`'s `HttpClient` is captured automatically:

| Captured | Notes |
| --- | --- |
| `package:http` | default `IOClient` |
| dio | attributed as `dio` in the panel |
| `Image.network` / `NetworkImage` | Flutter's own image loading |
| `cached_network_image` | cache misses / revalidations |
| graphql_flutter / ferry | tag with `X-Request-Client: graphql` for operation names |

> **GraphQL gets special treatment** — Operation names are extracted from queries, mutations, and subscriptions, then displayed with variables using arrow notation: `GetUser › 123`. No more guessing which `/graphql` request is which.

---

## Installation

```sh
flutter pub add buoy_network
```

Using the [`buoy` umbrella](../../flutter)? It's already included — the Network Monitor self-registers when you wrap your app in `BuoyDevTools`. Standalone, add one call before `runApp`:

```dart
import 'package:buoy_network/buoy_network.dart';

void main() {
  if (kDebugMode) registerBuoyNetwork(); // installs the HTTP hook + registers the tool
  runApp(const MyApp());
}
```

---

## What You See

For every request:

<!-- ::request-fields-grid -->

---

## Status Colors

<!-- ::status-colors-grid -->

---

## Stepping Between Requests

Open a request and the detail view gets a **Previous / Next** footer, so comparing two calls no longer means going back to the list and finding your place again.

It steps through exactly what the list was showing — the same filters, the same search. Narrow the list to failures, open one, and Next walks you through the failures only; the counter reads `REQUEST 4 OF 11`, not "4 of everything captured". Requests arriving while you read re-scope it live.

The list is newest-first, so **Previous** moves toward the newer request — the same direction as scrolling up.

---

## Known Gaps

Documented and on the roadmap: `cupertino_http` / `cronet_http` native clients, gRPC (raw sockets), secondary isolates, and Flutter web.

---

## What's Next

- [Storage Explorer](./storage) — Browse and edit shared_preferences
- [Environment Inspector](./env) — Validate env vars with type checking
- [Riverpod Inspector](./riverpod) — Watch every provider's live value
