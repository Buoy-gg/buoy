---
title: Overview
seoTitle: "Buoy for .NET MAUI"
id: maui-overview
description: "See web calls, logs, and app state in MAUI. Use the tools on your phone or in Buoy Desktop."
---

Buoy adds dev tools to your .NET MAUI app.
Open them from a small button in your app.
You can also use [Buoy Desktop](./devices) to see them.
MAUI support is in beta.

## Start here

Let your coding agent add Buoy to your app.
Review its edits when it is done.

<!-- ::agent-install platform="maui" where="docs-maui-overview" -->

To add it by hand, use the [Quick start](./quick-start).

## Tools

Each tool has its own setup call.
The [tool guide](./tools) shows the steps and limits.

| Tool | What you can do |
| --- | --- |
| [Network](./tools#network) | See web calls and test mock replies. |
| [Env](./tools#env) | Check app values and rules you pass in. |
| [Storage](./tools#storage) | Read and edit known app keys. |
| [Events](./tools#events) | See web calls and store events in one list. |
| [Console](./tools#console) | Read, search, and filter app logs. |
| [Clock](./tools#clock) | Change time read through the Buoy clock. |
| [Lifecycle](./tools#lifecycle) | Test app state, theme, and power changes. |
| [Routes](./tools#routes) | See the page stack and open known paths. |
| [Location](./tools#location) | Test places and trips through the Buoy wrapper. |
| [Permissions](./tools#permissions) | Test what the app does with each grant. |

[Ask Buoy](./ask-buoy) adds chat inside your app.
It needs Pro and uses the tools you add.
MAUI does not have React stores or React render tools.
Each tool page lists more gaps from React Native.

## What you need

Use .NET 10 and its MAUI workload.
The SDK has these targets and OS floors:

| Target | Lowest OS version |
| --- | --- |
| `net10.0-ios` | iOS 15.0 |
| `net10.0-android` | Android API 24 |
| `net10.0-maccatalyst` | Mac Catalyst 15.0 |

The host uses `Microsoft.Maui.Controls` version `10.0.20`.
The core targets `net10.0`.
Tests so far cover iOS and Android.
Mac Catalyst is a build target, with no test claim here.

Use a Free or Pro account in Debug builds.
See [Install](./installation) for keys and sign-in steps.
Read [Release builds](./release-builds) before you ship your app.
