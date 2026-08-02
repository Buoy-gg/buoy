---
title: Events Timeline
id: flutter-tools-events
description: "Watch network requests, storage writes, state changes, and navigation in one chronological timeline to debug complex Flutter user flows."
---

See every event across all your dev tools in one chronological timeline. Debug complex user flows by watching network requests, storage changes, state updates, and navigation happen in real-time.

<!-- ::events-demo -->

## Installation

```sh
flutter pub add buoy_events
```

Events Timeline automatically aggregates from all installed Buoy tools. No additional setup required — each tool registers its own event source when it registers itself.

---

## Event Sources

| Source | Events |
| --- | --- |
| [Network](./network) | every HTTP request with status and timing |
| [Storage](./storage) | every persisted write with diffs |
| [Routes](./routes) | every navigation with params |
| [Riverpod](./riverpod) | every provider state change |

> **Auto-detection** — If you have the tool installed, its events automatically appear in the timeline.

---

## What You Can Do

- **One interleaved timeline** — newest-first, across all installed source tools.
- **Per-source filters** — toggle a source on/off with live subscriber + event counts.
- **Header search** — tap the magnifying glass and filter as you type; matches an event's title, its subtitle (status, duration, host, key), and the full URL of network events. Stacks with the source filters.
- **Real detail views** — a network event opens the same detail page the Network tool shows, including the shared ignore-domain / ignore-URL toggles that hide matches from both lists.
- **Capture toggle + export** — pause capture, and copy as markdown, JSON, plaintext, or a mermaid diagram.

---

## LLM Export

Copy your event timeline in formats optimized for AI assistants. Reproduce a bug, export, and paste into Claude or ChatGPT with your question — or skip the copy-paste entirely and let an agent read it live with the [MCP server](../../mcp)'s `get_events`.

---

## What's Next

- [Network Monitor](./network) — Deep dive into API requests
- [Riverpod Inspector](./riverpod) — State inspection with diffs
- [Storage Explorer](./storage) — Browse and edit persisted data
