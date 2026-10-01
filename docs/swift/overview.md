---
title: Overview
seoTitle: "Buoy for Swift — developer tools for native iOS apps"
id: swift-overview
description: "Buoy for native iOS (beta): add network, storage, console, routes and other developer tools to a SwiftUI or UIKit app, and inspect them on the device, in Buoy Desktop or through MCP."
---

<!-- ::tool-film id="swift" -->

Buoy for Swift adds Buoy's developer tools to a native iOS app built with SwiftUI or UIKit. It is a separate SDK from the React Native packages: one Swift package, [Buoy-Swift](https://github.com/Buoy-gg/Buoy-Swift), that ships as a prebuilt, signed framework.

Swift support is in beta. The tools work on the device, in [Buoy Desktop](../desktop) and through the [MCP server](../mcp), but they do not match the React Native tools feature for feature. Each tool section in [Tools](./tools) lists what the native version leaves out.

## What you get

One `import Buoy` brings in every tool:

| Tool | What it covers on iOS |
|---|---|
| [Network](./tools#network) | URLSession requests on default and ephemeral configurations, response overrides and throttling |
| [Console](./tools#console) | The app's native console output |
| [Storage](./tools#storage) | UserDefaults, registered keychain items and registered MMKV instances |
| [Env](./tools#env) | Values and validation rules you supply |
| [Impersonate](./tools#impersonate) | User search and impersonation headers on captured requests |
| [Image Overlay](./tools#image-overlay) | A design image laid over the running app |
| [Images](./tools#images) | Loading, failure, retry, blank and replacement states for `BuoyAsyncImage` |
| [Notifications](./tools#notifications) | Received notifications, responses, permissions and local test delivery |
| [Routes](./tools#routes) | Screens, route patterns and navigation |
| [Scenarios](./tools#scenarios) | Reviewed, repeatable app setups, with recording in development builds |
| [Time Machine](./tools#time-machine) | Snapshots and live restore of UserDefaults and MMKV |
| [Events](./tools#events) | One timeline of network, storage and route activity |
| [Assets](./tools#assets) | Bundle resource sizes, duplicates and baselines |

Tools that depend on JavaScript stores or React rendering, such as React Query, Redux or Highlight Updates, are not part of the Swift SDK.

## Requirements

- An iOS app targeting iOS 16 or later, built with Xcode 16 or later. Apps can use Swift 5 or Swift 6 language mode.
- A Free or Pro Buoy account key.
- A development build for the first setup.

Buoy runs on iOS devices and the iOS Simulator. Mac Catalyst and macOS are not supported.

## Next steps

- [Quick Start](./quick-start): add the package and check your first captured request.
- [Installation](./installation): package versions, account keys and UIKit setup.
- [Physical Devices](./devices): connect an iPhone or iPad to Buoy Desktop.
- [Release Builds](./release-builds): keep Buoy out of the builds you ship.
