---
title: Network Monitor
seoTitle: "Flutter Network Inspector — debug http & dio requests"
id: flutter-tools-network
description: "Inspect supported HTTP requests in your Flutter app — package:http, dio, and image loads with headers, bodies, timing, and errors — live on the device, no proxy required."
---

Inspect requests that use the instrumented `dart:io` HttpClient path. Open a captured request to read its status, headers, body, timing, and error details.

The demo shows the React Native tool with mock data. Use the Flutter setup and feature descriptions below for supported behavior; the demo does not establish Flutter feature parity.

<!-- ::network-live-demo -->

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

<!-- ::pub package="buoy_network" -->

Using the [`buoy` umbrella](../installation)? It's already included — the Network Monitor self-registers when you wrap your app in `BuoyDevTools`. Standalone, add one call before `runApp`:

```dart
import 'package:flutter/foundation.dart';
import 'package:flutter/widgets.dart';
import 'package:buoy_network/buoy_network.dart';

void main() {
  if (kDebugMode) registerBuoyNetwork(); // installs the HTTP hook + registers the tool
  runApp(const MyApp());
}
```

Capture starts when `registerBuoyNetwork()` installs the hook and account access permits capture. Requests made before registration or through clients created outside the hooked path can be absent. With the umbrella, registration occurs when its widget mounts.

Run the app in debug mode with `BuoyDevTools` mounted. Trigger a fresh HTTP request, open Network, and check its URL and status. If it is missing, check registration timing, account state, and the transport used.

## Network throttling

Choose Network throttling from Network’s menu. The inspector minimizes and a
floating strip appears above the app. Tap the signal bars to cycle through
No throttling, Slow (+500 ms), Very slow (+2000 ms), and Offline. The profile
applies to new requests through the captured HttpClient path. Slow profiles add
a delay; download speed stays unchanged. Offline fails requests before sending.

Drag the strip by its handle or surface. Tap the handle to tuck it against the
right edge; choose Network throttling again to reveal it. Closing the inspector
or pausing capture leaves the profile active. Close on the strip turns throttling
off. Restarting the app restores an open strip at its saved position, but resets
the profile to No throttling.

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

## Override Responses

Use an override in a debug build to test how the app responds to a configured status, failure, or delay. After the test, disable the rule and repeat the request to confirm normal behavior.

Open any request and tap **Override** in the header. That takes you to the rule, prefilled from the request you were looking at — its endpoint, its method, its status, its real response body — so you're never starting from a blank field.

Pick an outcome from one grid: `500`, `401`, `404`, `403`, `429`, `503`, `400`, `200`, **Offline**, **Timeout**, **Real response**, or a custom status. Set a delay. Choose whether it fires always, once, N times, or **every other request** — that last one is how you test retry logic, because a rule that's always on or always off can't reach those paths.

**Transport coverage.** Overrides are applied at the same `HttpOverrides` layer as capture, so dio, `package:http` and raw `HttpClient` all see them. A forced failure arrives as a `SocketException`, which dio reports as `DioExceptionType.connectionError` — or `connectionTimeout` for a Timeout rule — exactly as a real network failure would.

**A delay behaves like a slow server, not a slow connection.** The wait is applied while the response is being received, so a 10s delay against a 5s `receiveTimeout` produces a receive timeout — the failure you were trying to reproduce.

**Rules survive a reload**, which is the point: force an endpoint to 500, restart, and watch what your boot path does.

**Automatic pause.** A body your app can't render would otherwise re-break it on every launch, with the controls to undo it locked inside an app that no longer draws. So if overrides sit armed and untouched across three launches, they pause themselves, with one tap to turn them back on.

Overridden requests pin to the top of the list under an **OVERRIDDEN** heading and carry a flask mark next to their status — a 500 you invented has to be distinguishable from a 500 your backend returned.

**Safety.** Overrides only run in debug builds, never touch Buoy's own licence traffic, and skip `OPTIONS` preflights. One master switch turns everything off without losing your rules.

Rules can also be driven from Buoy Desktop and from the `network_override` MCP tool — same rules, same device.

---

## Known Gaps

Documented and on the roadmap: `cupertino_http` / `cronet_http` native clients, gRPC (raw sockets), secondary isolates, and Flutter web.

---

## What's Next

- [Storage Explorer](./storage) — Browse and edit shared_preferences
- [Environment Inspector](./env) — Validate env vars with type checking
- [Riverpod Inspector](./riverpod) — Watch every provider's live value

---

## FAQ

### How do I inspect HTTP requests in a Flutter app without a proxy?

Add `buoy_network` (or the `buoy` umbrella) and call `registerBuoyNetwork()` — everything riding `dart:io`'s `HttpClient` is captured automatically, including `package:http`, dio, `Image.network`/`NetworkImage`, and `cached_network_image`. The panel opens on the device itself, so there is no proxy or desktop tool to attach.

### Does it capture dio and GraphQL requests?

Yes — dio traffic is captured and attributed as `dio`, and GraphQL operation names are extracted from queries, mutations, and subscriptions and shown with their variables (`GetUser › 123`). Tag graphql_flutter or ferry requests with `X-Request-Client: graphql` to get operation names.

### Are requests made during startup captured?

Requests made after hook installation can be captured. Register before the startup requests you need to inspect, and configure account access. Requests made before the umbrella widget mounts may precede its registration.
