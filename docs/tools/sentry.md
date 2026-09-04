---
title: Sentry
seoTitle: "React Native Sentry Inspector — see what your app sends, before it bills you"
id: tools-sentry
description: "See every envelope your React Native app sends to Sentry, what it costs in billing units, and why an event never arrived — on-device, before you ship."
---

<!-- ::platform-badge platform="both" -->

The Sentry SDK is a black box that bills you. Buoy Sentry opens it: every envelope your app sends, what it costs in Sentry's own billing units, and — when an event never shows up in Sentry — which layer discarded it.

## Why this exists

Sentry bills on **receipt**. Data its server-side sampling later discards is still charged, so the only lever that removes cost without losing anything is client-side config. On mobile, that config ships frozen inside an app-store binary: you cannot fix a chatty release from the dashboard. Getting it right before you ship is the whole game, and until now nothing showed you what the SDK was actually doing.

## Installation

<!-- ::PM npm="npm install @buoy-gg/sentry" yarn="yarn add @buoy-gg/sentry" pnpm="pnpm add @buoy-gg/sentry" bun="bun add @buoy-gg/sentry" -->

The tool auto-appears in your `FloatingDevTools` menu and starts watching automatically:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools />  {/* Sentry tool auto-detected */}
    </>
  );
}
```

`@sentry/react-native` is an optional peer — without it the tool simply reports "SDK not found". Capture installs when your bundle evaluates, not when you open the tool, so the traffic `Sentry.init` sends during startup is already there the first time you look.

In a monorepo where Metro may resolve the SDK from package source rather than your app's copy, attach explicitly:

```tsx
import * as Sentry from "@sentry/react-native";
import { SentryRoot } from "@buoy-gg/sentry";

<SentryRoot getClient={Sentry.getClient} />
```

---

## What You Can Do

- **Watch every envelope leave** — item types (`event`, `transaction`, `log`, `session`, `client_report`, `attachment`, `replay_event`), byte sizes, span counts, and the final post-processing payload exactly as Sentry will receive it.
- **See what a session costs** — errors, spans, replays, log and attachment volume, projected against a plan quota. Spans are the surprise: Sentry bills each one individually, so a single transaction is many units.
- **Find out where your event went** — a `beforeSend` that returned `null`, a sample-rate roll, dedupe, React Native tracing discarding an empty transaction, or a native rate limit.
- **Grade the config you're actually running** — the options `Sentry.init` resolved, not the ones you typed, flagged for quota risk, privacy, and correctness.
- **Catch personal data before it ships** — payloads are scanned for emails, tokens, card numbers and sensitive fields, reported by JSON path with the value redacted.

---

## How it works

The tool subscribes to the Sentry client's public `beforeEnvelope` hook — the same tee point Sentry's own Spotlight integration uses. It only observes: the envelope is never modified and your transport is untouched. To attribute drops it also wraps your `beforeSend` callbacks, passing their result through unchanged; your callback keeps full authority over what is sent.

Nothing leaves the device. The inspector reads what the SDK is about to send; it does not send anything itself.

---

## Read it from your AI

With the [MCP server](../mcp), an agent can call `get_sentry_envelopes` to read the real exception, breadcrumbs and contexts **without waiting for ingestion or spending quota**, `get_sentry_cost` for the billing picture, and `get_sentry_drops` when an event is missing.

Sentry's own MCP server reads issues that already reached sentry.io. This one reads the error on the device in front of you — along with the network request, storage, and state that caused it.

---

## What's Next

- [Network Monitor](./network) — the requests behind an error
- [Console](./console) — the logs around it
- [AI / MCP Server](../mcp) — let an agent read all three

---

## FAQ

### Does this send my data anywhere?

No. It reads envelopes on their way out of your app and shows them to you. Your Sentry SDK sends exactly what it would have sent otherwise.

### Will it change what Sentry receives?

No. The envelope tee is read-only, and the `beforeSend` wrapper returns your callback's own answer untouched.

### Why does my transaction count as more than one unit?

Because Sentry bills spans individually, and a transaction is a bundle of them. A screen that opens with four spans costs four units every time it opens — which is why the Cost tab reports spans rather than transactions.

### Can it see native crashes?

Not yet. Native crashes, release-health sessions and Session Replay uploads are sent by the native SDK and never pass through the JavaScript layer this tool observes.
