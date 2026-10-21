---
title: Overview
seoTitle: "Buoy for Android — developer tools for native Android apps"
id: android-overview
description: "Buoy for native Android (beta): add network, storage, console, routes and other developer tools to a Kotlin app, and use them on the device, in Buoy Desktop or through MCP."
---

Buoy for Android adds Buoy's tools to a native Android app.
It works with Jetpack Compose and with plain Android views.
It is its own SDK, apart from the React Native packages.
It ships as one Maven package, `gg.buoy:buoy-android`.

Android support is in beta.
The tools work on the phone, in [Buoy Desktop](../desktop) and through the [MCP server](../mcp).
They don't match the React Native tools in every way.
Each tool in [Tools](./tools) says what Android leaves out.

## Start here

Follow the [Quick Start](./quick-start).

## What you get

One package brings in every tool:

| Tool | What it covers on Android |
|---|---|
| [Network](./tools#network) | Web calls made with OkHttp, fake answers and slow modes |
| [Console](./tools#console) | The app's own log lines, and `BuoyConsole` calls |
| [Storage](./tools#storage) | SharedPreferences, plus MMKV and safe stores you add |
| [Env](./tools#env) | Values and rules you give it |
| [Impersonate](./tools#impersonate) | User search, and a header on calls Buoy sees |
| [Image Overlay](./tools#image-overlay) | A design image laid over the app |
| [Images](./tools#images) | Load, fail, retry, blank and swap for `BuoyAsyncImage` |
| [Notifications](./tools#notifications) | Notifications sent through Buoy, taps and test alerts |
| [Routes](./tools#routes) | Screens, route patterns and moving around the app |
| [Scenarios](./tools#scenarios) | Saved app setups you can run again |
| [Time Machine](./tools#time-machine) | Save the app's state and put it back |
| [Events](./tools#events) | One list of web, storage and route events |
| [Assets](./tools#assets) | What the APK ships: sizes, copies and baselines |
| [Clock](./tools#clock) | Jump, freeze or speed up time read through `BuoyClock` |
| [Lifecycle](./tools#lifecycle) | What the app does when people leave and come back |
| [Location](./tools#location) | Fake places, routes and areas for `BuoyLocationProvider` |
| [Permissions](./tools#permissions) | Denied, blocked and not-asked states for `BuoyPermissions` |
| [Bench](./tools#bench) | A speed HUD, and test runs you can save and compare |
| [Highlight](./tools#highlight) | Redraws of views marked with `buoyTrackRenders` |
| [Debug Borders](./tools#debug-borders) | Lines around views, to check the layout |
| [Ask Buoy](./tools#ask-buoy) | Ask about the app in plain words. It can use the tools. |

Some tools only exist for JavaScript.
React Query, Redux, Zustand, Jotai, JS Top and Three are not in the Android SDK.
Sentry is not on Android yet.

## What you need

- An app with `minSdk` 24 or higher and `compileSdk` 35 or higher.
- Kotlin 2.0 or newer, if the app uses Kotlin.
- A Free or Pro Buoy account key.
- A debug build for the first setup.

Buoy runs on phones, tablets and the emulator.

## Next steps

- [Quick Start](./quick-start): add the package and check your first web call.
- [Installation](./installation): versions, account keys and every setup option.
- [Physical Devices](./devices): connect a phone to Buoy Desktop.
- [Release Builds](./release-builds): keep Buoy out of the builds you ship.
