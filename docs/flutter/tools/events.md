---
title: Events Timeline
seoTitle: "Flutter Event Timeline — all app activity in one stream"
id: flutter-tools-events
description: "Watch network requests, storage writes, state changes, and navigation in one chronological timeline to debug complex Flutter user flows."
---

Inspect captured events from registered source tools in one chronological timeline. Debug complex user flows by watching network requests, storage changes, state updates, and navigation happen in real-time.

The demo shows the React Native tool with mock data. Use the Flutter setup and feature descriptions below for supported behavior; the demo does not establish Flutter feature parity.

<!-- ::events-live-demo -->

## Installation

<!-- ::pub package="buoy_events" -->

With the umbrella, Events and its bundled source tools are registered for you. For a standalone install, register Events as well as the sources you need:

```dart
import 'package:buoy_events/buoy_events.dart';

registerBuoyEvents();
```

Call registration before using the tool and keep `BuoyDevTools` mounted in debug mode. Complete Network, Storage, Routes, or Riverpod integration on the corresponding tool pages. Trigger one event and check its source badge in the timeline.

---

## Event Sources

| Source | Events |
| --- | --- |
| [Network](./network) | every HTTP request with status and timing |
| [Storage](./storage) | every persisted write with diffs |
| [Routes](./routes) | every navigation with params |
| [Riverpod](./riverpod) | every provider state change |

> **Auto-detection** — A source must be registered and capturing events before it appears in the timeline.

---

## What You Can Do

- **One interleaved timeline** — newest-first, across all installed source tools.
- **Per-source filters** — toggle a source on/off with live subscriber + event counts.
- **Header search** — tap the magnifying glass and filter as you type; matches an event's title, its subtitle (status, duration, host, key), and the full URL of network events. Stacks with the source filters.
- **Real detail views** — a network event opens the same detail page the Network tool shows, including the shared ignore-domain / ignore-URL toggles that hide matches from both lists.
- **Capture toggle + export** — pause capture, and copy as markdown, JSON, plaintext, or a mermaid diagram.

---

## Search

Tap the magnifying glass in the header to filter the timeline as you type. Matches an event's title, its subtitle (status, duration, host, key), and the full URL of network events — so you can search a host, an action type, a storage key, or a query string value.

> Search stacks with the source badges — filter to Network, then search for the failing endpoint.

---

## LLM Export

Copy your event timeline in formats optimized for AI assistants. Reproduce a bug, export, and paste into Claude or ChatGPT with your question — or skip the copy-paste entirely and let an agent read it live with the [MCP server](../../mcp)'s `get_events`.

The export follows what's on screen: filter to a source or search the timeline first and you copy that slice, not the whole session.

---

## Correlation

Related events can share a `correlationId` (included in JSON / LLM exports) so you can trace a full lifecycle across sources when tools emit one.

---

## What's Next

- [Network Monitor](./network) — Inspect request details
- [Riverpod Inspector](./riverpod) — State inspection with diffs
- [Storage Explorer](./storage) — Browse and edit persisted data

---

## FAQ

### How do I see everything my Flutter app did in one timeline?

Install `buoy_events` — it aggregates automatically from the Buoy tools you already have: network requests, storage writes, navigation, and Riverpod state changes, newest-first, with per-source filters and live counts. Source tools must be registered and configured.

### Can I export the timeline?

Yes — pause capture and copy the timeline as markdown, JSON, plaintext, or a mermaid diagram.
