---
title: Installation
seoTitle: "Install Buoy for Swift — Swift Package Manager setup for iOS"
id: swift-installation
description: "Install the Buoy Swift package with Xcode or Package.swift, configure your account key, and set up Buoy in SwiftUI and UIKit apps."
---

## Requirements

- An iOS app targeting iOS 16 or later, built with Xcode 16 or later.
- A Free or Pro Buoy account key. Get one through your [Buoy account](https://buoy.gg).
- A development build for the first setup and verification.

Buoy ships as a prebuilt, signed framework for iOS devices and the iOS Simulator. Mac Catalyst and macOS are not supported. Apps can use Swift 5 or Swift 6 language mode.

## Add the package

In Xcode, choose **File > Add Package Dependencies…** and enter:

```text
https://github.com/Buoy-gg/Buoy-Swift.git
```

Use the dependency rule **Up to Next Minor Version** from `0.1.0`. While the package is in 0.x, a minor version can contain breaking changes. Add the **Buoy** library product to your iOS app target.

In a `Package.swift` manifest:

```swift
.package(url: "https://github.com/Buoy-gg/Buoy-Swift.git", .upToNextMinor(from: "0.1.0"))
```

Import it with `import Buoy`. Every tool is part of that one module.

## Account key

The examples read the key from a `BUOY_LICENSE_KEY` environment variable in your Xcode Run scheme. Xcode sets it only when it launches the app from that scheme. For other internal builds, supply the key through your app's own configuration.

```swift
var config = BuoyConfig()
config.licenseKey = ProcessInfo.processInfo.environment["BUOY_LICENSE_KEY"]
```

## SwiftUI

Call `Buoy.start(config)` early, on the main actor, and add `.buoyDevTools()` to your root view. The [Quick Start](./quick-start) has a complete app.

Start Buoy before you create networking singletons or state objects that create URLSessions. Calling it in `App.init` can still be too late for clients created by stored-property initializers.

## UIKit

Call `Buoy.start(config)` on the main actor early in your app lifecycle, with the same account configuration and compilation guard as the SwiftUI example. After creating your app window, install the overlay in its `UIWindowScene`:

```swift
#if DEBUG
Buoy.install(in: windowScene)
#endif
```

This belongs in your existing scene delegate, where `windowScene` is the scene attached to your app window. The SwiftUI modifier and the UIKit installer mount the same overlay.

## Buoy Desktop

Open [Buoy Desktop](../desktop) and sign in. The default broker URL is `http://localhost:42831`, which works for the iOS Simulator on the same Mac. For an iPhone or iPad, see [Physical Devices](./devices).

Select the Swift device in Desktop and repeat the request check. A device connection and a populated Network panel are separate checks. Keep the default per-install device ID unless you need to manage identity yourself; a custom ID must be unique for each device.

## Lifecycle

Calling `Buoy.start` again returns the existing runtime and can update a supplied account key. It does not change the broker URL or device identity of a running session, so set the initial configuration before starting.

`Buoy.runtime?.stop()` stops the runtime connection and account lifecycle. `BuoyDevTools.uninstall()` removes the overlay. Neither call removes networking hooks that were already installed.
