---
title: Telemetry
seoTitle: "Buoy Telemetry — what the anonymous install ping sends, and how to turn it off"
id: telemetry
description: "Buoy sends one anonymous ping per day in development. Exactly what it contains, what it never contains, and how to disable it in one line."
---

Buoy's install ping is separate from account validation and the connections you configure for Desktop, MCP, or Ask Buoy. This page describes the ping and its opt-out; it does not describe all network traffic from those features.

## Turn it off

For the React Native SDK, include `telemetry: false` in your initialization:

```ts
import { Buoy } from '@buoy-gg/core';

Buoy.init({
  licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY,
  telemetry: false,
});
```

This example uses Expo's environment variable. React Native CLI apps should pass the key from their configured environment loader.

For Desktop, set `BUOY_TELEMETRY=0` in its launch environment. The opt-out prevents this ping and its telemetry-state writes. It does not disable account validation, tool persistence, or configured remote connections.

## What it sends

The SDK sends four fields. Desktop adds `surface`:

| Field | Example | Meaning |
| --- | --- | --- |
| `installId` | `9f3c1d7a-…` | Random identifier saved locally for this installation |
| `version` | `7.0.22` | Installed Buoy version |
| `platform` | `ios` | Operating-system platform |
| `tier` | `free` | `free` or `pro` for an admitted account; `locked` when no account has been verified |
| `surface` | `desktop` | Added by Desktop to identify the sending application |

The install identifier is generated independently of hardware and account properties. It persists with local telemetry state; reinstalling does not necessarily remove that state.

## What it never sends

The ping payload does not include tool events, console messages, storage values, screenshots, email, account keys, app names, bundle IDs, repository names, or environment values.

This payload restriction does not apply to other features. Account validation sends the information needed to validate access. Desktop and MCP exchange tool data over their configured connection. Ask Buoy sends conversation context and tool results to your configured model endpoint. Read each feature's setup and data guidance before enabling it.

## When it fires

The React Native SDK checks the development flag before sending. Desktop checks on launch. Both throttle attempts to at most once per 24 hours for an installation and handle request failures without blocking normal startup.

The first SDK attempt for a new install prints a notice with the opt-out setting. Existing telemetry state suppresses repeated notices.

## Why it exists

The ping measures installations and activity separately from package downloads and account records. Package downloads can include CI runs, while download totals cannot distinguish an update from a new installation.

## Blocked networks

The ping endpoint is `https://buoy.gg/api/t`. Blocking that endpoint disables this measurement without disabling the tools.

Do not treat that as permission to block all of `buoy.gg`: sign-in and other configured features may need it. Account validation uses `buoy.gg/api/license`. Desktop, MCP, and your AI gateway have their own connection requirements.
