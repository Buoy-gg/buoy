---
title: Sentry
seoTitle: "React Native Sentry Inspector — see what your app sends, before it bills you"
id: tools-sentry
description: "Inspect captured Sentry envelopes, reported drops and usage estimates. Local capture does not confirm delivery or billing."
---

<!-- ::platform-badge platform="both" -->

Inspect JavaScript Sentry envelopes and captured drop reasons before or as the SDK sends them. Use the tool to review payloads, resolved SDK configuration, and estimated quota usage.

## Why this exists

A missing event can be caused by client configuration or processing before ingestion. Buoy helps inspect that part of the path. Its cost view estimates usage from observed envelopes; your Sentry plan and billing records determine actual charges.

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

- **Inspect captured envelopes** — item types (`event`, `transaction`, `log`, `session`, `client_report`, `attachment`, `replay_event`), byte sizes, span counts, and available post-processing payloads. Capture does not confirm transport success or server ingestion.
- **See what a session costs** — errors, spans, replays, log and attachment volume, projected against a plan quota. Spans are the surprise: Sentry bills each one individually, so a single transaction is many units.
- **Find out where your event went** — a `beforeSend` that returned `null`, a sample-rate roll, dedupe, React Native tracing discarding an empty transaction, or a native rate limit.
- **Grade the config you're actually running** — the options `Sentry.init` resolved, not the ones you typed, flagged for quota risk, privacy, and correctness.
- **Catch personal data before it ships** — payloads are scanned for emails, tokens, card numbers and sensitive fields, reported by JSON path with the value redacted.

---

## How it works

The tool subscribes to the Sentry client's public `beforeEnvelope` hook — the same tee point Sentry's own Spotlight integration uses. It only observes: the envelope is never modified and your transport is untouched. To attribute drops it also wraps your `beforeSend` callbacks, passing their result through unchanged; your callback keeps full authority over what is sent.

The inspector observes the SDK without changing its outgoing envelopes. Your Sentry SDK still sends its normal traffic. If you connect Desktop or MCP, captured data can also be read through that connection.

---

## Read it from your AI

With the [MCP server](../mcp), an agent can call `get_sentry_envelopes` to read the real exception, breadcrumbs and contexts **without waiting for ingestion; this does not prevent the SDK from sending the event**, `get_sentry_cost` for the billing picture, and `get_sentry_drops` when an event is missing.

Sentry's own MCP server reads issues that already reached sentry.io. This one reads the error on the device in front of you — along with the network request, storage, and state that caused it.

---

## What's Next

- [Network Monitor](./network) — the requests behind an error
- [Console](./console) — the logs around it
- [AI / MCP Server](../mcp) — let an agent read all three

---

## FAQ

### Does this send my data anywhere?

Your Sentry SDK sends its usual envelopes. Buoy displays its captured copy locally, and connected Desktop or MCP clients can read that copy. See [Telemetry](../telemetry) for separate Buoy account and telemetry traffic.

### Will it change what Sentry receives?

No. The envelope tee is read-only, and the `beforeSend` wrapper returns your callback's own answer untouched.

### Why does my transaction count as more than one unit?

A transaction can contain several spans. The Cost tab counts observed units to estimate usage; verify current billing rules and quotas against your Sentry plan.

### Can it see native crashes?

Not yet. Native crashes, release-health sessions and Session Replay uploads are sent by the native SDK and never pass through the JavaScript layer this tool observes.

## Web support (unreleased)

Pass the existing browser SDK’s getClient through the host’s sentryGetClient prop. Envelope capture and diagnostic panels use the shared implementation. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
