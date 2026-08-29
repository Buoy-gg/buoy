---
title: Network Monitor
seoTitle: "Flutter Network Inspector — debug http & dio requests"
id: flutter-tools-network
description: "Inspect every HTTP request your Flutter app makes — package:http, dio, and image loads with headers, bodies, timing, and errors — live on the device, no proxy required."
---

See every API call your app makes. Request, response, headers, timing, errors — all in real-time with zero configuration.

The React Native build of this tool, running here on mock data. The Flutter port ships the same panels — walk the tour, or skip it and start tapping.

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
import 'package:buoy_network/buoy_network.dart';

void main() {
  if (kDebugMode) registerBuoyNetwork(); // installs the HTTP hook + registers the tool
  runApp(const MyApp());
}
```

**Boot traffic is already there when you open the tool.** The HTTP hook installs at `registerBuoyNetwork()`, not when something first watches, so requests fired during startup — `main()` fetches, session bootstrap, your first screen's loads — are held and appear in the list the moment you open the panel or connect a dashboard. If nothing ever watches, nothing is retained.

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

## Override Responses

Chrome DevTools has **Local Overrides** — serve different bytes for a request without touching the server. Buoy has the same idea on device, and goes further: Chrome can't change a status code (override a body there and it forces `200`), and it has no way to simulate latency or a dead connection. Those are the things you actually need on mobile.

Open any request and tap **Override** in the header. That takes you to the rule, prefilled from the request you were looking at — its endpoint, its method, its status, its real response body — so you're never starting from a blank field.

Pick an outcome from one grid: `500`, `401`, `404`, `403`, `429`, `503`, `400`, `200`, **Offline**, **Timeout**, **Real response**, or a custom status. Set a delay. Choose whether it fires always, once, N times, or **every other request** — that last one is how you test retry logic, because a rule that's always on or always off can't reach those paths.

**It works with every client the tool captures.** Overrides are applied at the same `HttpOverrides` layer as capture, so dio, `package:http` and raw `HttpClient` all see them. A forced failure arrives as a `SocketException`, which dio reports as `DioExceptionType.connectionError` — or `connectionTimeout` for a Timeout rule — exactly as a real network failure would.

**A delay behaves like a slow server, not a slow connection.** The wait is applied while the response is being received, so a 10s delay against a 5s `receiveTimeout` produces a receive timeout — the failure you were trying to reproduce.

**Rules survive a reload**, which is the point: force an endpoint to 500, restart, and watch what your boot path does.

**And it lets go.** A body your app can't render would otherwise re-break it on every launch, with the controls to undo it locked inside an app that no longer draws. So if overrides sit armed and untouched across three launches, they pause themselves, with one tap to turn them back on.

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

Yes. The HTTP hook installs at `registerBuoyNetwork()` rather than when something first watches, so boot traffic — `main()` fetches, session bootstrap, your first screen's loads — is held and appears the moment you open the panel or connect a dashboard.
