---
title: Telemetry
seoTitle: "Buoy Telemetry — what the anonymous install ping sends, and how to turn it off"
id: telemetry
description: "Buoy sends one anonymous ping per day in development. Exactly what it contains, what it never contains, and how to disable it in one line."
---

Buoy's install ping is separate from account validation and the connections you configure for Desktop, MCP, or Ask Buoy. This page describes the ping and its opt-out; it does not describe all network traffic from those features.

## Turn it off

Each Buoy SDK has a `telemetry` switch. Set it to false.

React Native, web, Angular and Capacitor:

```ts
import { Buoy } from '@buoy-gg/core';

Buoy.init({
  licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY,
  telemetry: false,
});
```

This example uses an Expo env value. React Native CLI apps pass the key from their own env loader.

Flutter:

```dart
Buoy.init(telemetry: false);
// or
BuoyDevTools(telemetry: false, child: app);
```

Swift:

```swift
var config = BuoyConfig()
config.telemetry = false
Buoy.start(config)
```

.NET MAUI:

```csharp
builder.UseBuoy(options =>
{
    options.Telemetry = false;
});
```

For Desktop, set `BUOY_TELEMETRY=0` when you launch it.

For [Buoy Web](./buoy-web), set `BUOY_TELEMETRY=0` (or `DO_NOT_TRACK=1`) in its MCP config. Buoy Web sends the same kind of ping with `surface` set to `web-mcp`. It never sends web addresses, page text or which tools ran. Its random id is kept in `~/.buoy/web/install.json`.

Turning it off stops this ping. It also stops the ping from saving its state. It does not stop the account check. Your saved tool data stays. Your Desktop, MCP and Ask Buoy links keep working.

## What it sends

The SDKs send five fields. Desktop and Buoy Web send `surface` too. Buoy Web sends no `framework`.

| Field | Example | Meaning |
| --- | --- | --- |
| `installId` | `9f3c1d7a-…` | A random id saved on this machine |
| `version` | `7.0.22` | The Buoy version you have |
| `platform` | `ios` | The OS, like `ios`, `android` or `web` |
| `tier` | `free` | `free` or `pro` once your account is checked. `locked` if no account is checked yet |
| `framework` | `flutter` | The kind of app: `react-native`, `tv`, `web`, `angular`, `capacitor`, `flutter`, `swift` or `maui` |
| `surface` | `desktop` | Sent only by Desktop (`desktop`) and Buoy Web (`web-mcp`). It says which app sent the ping |

The id is made at random. It does not come from your machine or your account. It is saved with the ping's state. A reinstall may not clear it.

## What it never sends

The ping payload does not include tool events, console messages, storage values, screenshots, email, account keys, app names, bundle IDs, repository names, or environment values.

This payload restriction does not apply to other features. Account validation sends the information needed to validate access. Desktop and MCP exchange tool data over their configured connection. Ask Buoy sends conversation context and tool results to your configured model endpoint. Read each feature's setup and data guidance before enabling it.

## When it fires

Each SDK sends the ping only in dev builds. A shipped app never sends it. Desktop checks when it starts.

Each one sends at most one ping a day. If the ping fails, your app keeps going.

The first time a new install sends it, Buoy prints a note. The note tells you how to turn it off. You see it once.

## Why it exists

The ping measures installations and activity separately from package downloads and account records. Package downloads can include CI runs, while download totals cannot distinguish an update from a new installation.

## Blocked networks

The ping endpoint is `https://buoy.gg/api/t`. Blocking that endpoint disables this measurement without disabling the tools.

Do not treat that as permission to block all of `buoy.gg`: sign-in and other configured features may need it. Account validation uses `buoy.gg/api/license`. Desktop, MCP, and your AI gateway have their own connection requirements.
