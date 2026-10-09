---
title: Tools
seoTitle: "Buoy for Swift tools — network, storage, console and more for iOS"
id: swift-tools
description: "What each Buoy tool does in a native iOS app, how to connect it to your app's state, and what the Swift version does not support yet."
---

Network and Console start with `Buoy.start`. The other tools need a line of configuration or a view modifier to reach your app's state. Each section below lists how to connect the tool and what the native version leaves out compared with React Native.

## Network

Network capture uses URLProtocol registration and hooks for the default and ephemeral URLSession configurations. Install it before creating sessions or configurations that should be inspected. Libraries built on those configurations are captured too; check the client your app actually uses.

WebSocket tasks are excluded. Do not assume coverage for background sessions, WebKit networking or other native transports. Body and history limits can affect the data available in a request's detail view or a remote snapshot.

Response overrides can change app behavior. Test with a disposable request, check the matching rule, and turn the override off when you finish. A captured request does not by itself prove the backend received it.

Network throttling offers No throttling, Slow (+500 ms), Very slow (+2000 ms) and Offline. Open it from Network's menu. The floating controls stay active when Network is minimized or capture is paused; Close turns throttling off. Profiles reset when the app restarts. Slow profiles delay dispatch without limiting bandwidth, and Offline takes priority over response overrides. Overrides and throttling stay inactive until account access is established.

## Console

Console keeps `print` output from the moment `Buoy.start` runs, and reads the app's own `Logger`, `os_log` and `NSLog` entries for the whole process lifetime. Log entries from Apple's system frameworks are left out.

## Storage

Storage shows UserDefaults and keychain items you register. `BuoyStorageModule.configure(requiredKeys:)` declares the keys your app expects, with their backend, type and description.

To include MMKV, register existing instances with `BuoyMMKVRegistry.shared.register(...)`. Supply an instance ID, encryption and read-only flags, a typed `read` closure, and throwing `write` and `remove` closures. Values are `BuoyMMKVValue.string`, `.number`, `.boolean` or `.buffer`. Keep your app's type schema, because native MMKV needs the matching getter for each stored value.

Call `refresh(instanceId:)` after host writes, or from your storage-change observer, to record changes and update an open browser. Writes made through Buoy refresh automatically. Read-only instances reject writes, and binary values show their byte count without editing. History undo and jump work for UserDefaults only. The SDK does not add an MMKV dependency to apps that do not use it.

## Env

`BuoyEnv.configure(vars:required:)` supplies the values to show and the rules to check them against.

## Impersonate

`BuoyImpersonate.configure` supplies user search and host callbacks. Buoy adds the impersonation header to captured requests; your backend must authenticate and authorize the change. Data-clearing options are passed to your app's callbacks.

## Image Overlay

Mark targets with `.buoyImageOverlayTarget("Profile photo")`, or place the image freely. Settings cover opacity, scale, offsets, visibility, lock, flips, outline and automatic tracking. `loadImage` downloads an HTTP(S) image; check the snapshot's `loading` and `error` fields for the result.

## Images

Use `BuoyAsyncImage` to exercise loading, failure, retry, blank and replacement states. `.buoyImage(url:)` measures an existing view without controlling its content.

```swift
BuoyAsyncImage(url: imageURL) { phase in
    switch phase {
    case .empty: ProgressView()
    case .success(let image): image.resizable().scaledToFit()
    case .failure: Text("Image unavailable")
    @unknown default: EmptyView()
    }
}
```

Native image records group by URL. Expo disk-cache operations are not available.

A savings report needs a WebP encoder. iOS has none built in, and Buoy doesn't ship one. To get savings reports, give Buoy your own encoder. It gets the image and a quality from 0 to 1. It returns WebP bytes.

```swift
#if DEBUG
ImageSavings.encodeWebP = { image, quality in
    try MyWebPEncoder.encode(image, quality: quality)
}
#endif
```

With no encoder, `proveSavings` says "No WebP encoder is set up."

## Notifications

Capture starts when you call `BuoyNotifications.install()`. Call it early, in your app's initializer or `didFinishLaunching`, after your app sets its own `UNUserNotificationCenter` delegate, or pass that delegate to it:

```swift
#if DEBUG
BuoyNotifications.install()
#endif
```

Forward the APNs registration callback to `BuoyNotifications.observeDeviceToken(_:)`. Buoy records received notifications, your app's presentation decisions and user responses, and forwards every delegate call to your app unchanged. Sending a local test notification still needs notification permission.

## Routes

Supply route patterns and navigation callbacks with `BuoyRoutes.configure`, and mark screens with `.buoyRoute(...)`.

For accurate duplicate pushes and pops, pass `stack:` to `BuoyRoutes.configure`. Return `[RouteStackItem]` from root to top, with a stable, unique `key` for each entry, and call `BuoyRoutes.refreshStack()` whenever that navigation state changes. Tracking screen appearance alone cannot reliably tell apart two stack entries with the same path.

## Scenarios

`BuoyScenarios.define(...)` registers a scenario from app code. Scenarios saved remotely, for example by an AI agent over MCP, arrive as drafts that you review and accept before running. The runner checks that the needed actions and variables are available before the first step, records the effects it applies, and supports explicit undo recipes. Library entries and active effects survive a restart.

**Record a flow** captures supported taps and text in your app, adds waits for observed navigation, and opens a review before saving. Buoy's own controls and secure text fields are excluded. Scroll gestures, slider replay, long-press replay and state-store shortcuts are not supported.

Running and recording scenarios only work in development builds; see [Release Builds](./release-builds#development-only-features).

## Time Machine

Time Machine captures UserDefaults and registered MMKV instances and keeps their value types. It supports previews, item exclusions and scopes, named restore points, duplication and live restore. Each restore saves a safety point first. The floating bar supports capture, choosing a point, live restore and a ten-second Undo.

Register additional state with `BuoyTimeMachine.registerProvider(BuoySnapshotProvider(...))`. A provider returns keyed `BuoySnapshotItem` values and implements `restoreItem`; a nil payload removes the item. Payloads must be JSON compatible.

In-process reload, fresh-install baselines and wipe-all are not available. Keychain is excluded from the default provider.

## Events

Events combines captured Network, UserDefaults and MMKV, and Routes activity in one timeline, with source filters, search, pause, clear, copy and JSON detail. Exports support JSON, Markdown, plain text and Mermaid. React store and render events are not available.

## Assets

Assets scans loose resources in your app bundle, measures their size, finds duplicate content and saves a comparison baseline. Register other resource bundles with `BuoyAssets.registerBundle(...)`, and call `BuoyAssets.markLoaded(url)` after your app loads a resource.

Compiled `Assets.car` files appear as a single record each, and loaded status is what your app reports; a resource never marked as loaded is not necessarily unused. Read `scanStatus.coverage` and `warnings` before acting on the inventory.

## MCP

Follow the [MCP setup guide](../mcp) to connect your editor. Free includes the basic MCP. Pro adds the full MCP. Start with device discovery, then check the capabilities your Swift app advertises.

The UIKit interaction adapter lets an agent inspect and act on supported UI elements. Coverage depends on the view and accessibility information your app exposes, so check the result in the app after an action. Swift advertises only the actions it implements; React render tracking, Expo cache controls and in-process app reload are absent.
