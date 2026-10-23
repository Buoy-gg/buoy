---
title: Installation
seoTitle: "Install Buoy for Swift — Swift Package Manager setup for iOS"
id: swift-installation
description: "Install the Buoy Swift package with Xcode or Package.swift, sign in to your Buoy account, and set up Buoy in SwiftUI and UIKit apps."
---

## Requirements

- An iOS app targeting iOS 16 or later, built with Xcode 16 or later.
- A Free or Pro [Buoy account](https://buoy.gg).
- A development build for the first setup and verification.

Buoy ships as a prebuilt, signed framework for iOS devices and the iOS Simulator. Mac Catalyst and macOS are not supported. Apps can use Swift 5 or Swift 6 language mode.

## Install with your agent

Paste the prompt into Claude Code, Cursor or Codex. Your agent reads your app and picks the tools. Review its changes before you commit them.

<!-- ::agent-install platform="swift" where="docs-swift-installation" -->

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

## Sign in

Sign in from your project folder:

```bash
npx --package=@buoy-gg/core buoy login
```

Your browser opens buoy.gg. Click Allow. Then the command writes a dev token to `.env.local` as `BUOY_LICENSE_KEY`. It adds that file to your `.gitignore`, if you have one.

Now put the token in your Xcode Run scheme. Open **Product > Scheme > Edit Scheme > Run > Arguments**. Add `BUOY_LICENSE_KEY` under Environment Variables. Its value is in `.env.local`. Leave **Shared** off for the scheme, so the token stays out of git.

Buoy reads `BUOY_LICENSE_KEY` by itself when `Buoy.start` runs. Xcode sets it only when it runs the app from that scheme. After you get a new token, run the app from Xcode again.

A dev token works in dev builds for 30 days. Then run `buoy login` again. It opens nothing in a TestFlight or App Store build.

### Test builds

TestFlight and QA builds sign in with a code. That way, no key goes in the app. Turn it on with `config.signIn`:

```swift
var config = BuoyConfig()
config.signIn = BuoySignInConfig()
Buoy.start(config)
```

First, add your app at [buoy.gg/dashboard/sites](https://buoy.gg/dashboard/sites). Type `app:` and then your bundle id, like `app:com.acme.shop`. Buoy reads your bundle id by itself. To use a different id, pass `BuoySignInConfig(appId:)`.

In the app, tap **Sign in**. Scan the QR code with a phone that is signed in to buoy.gg. Or go to buoy.gg/activate and type the code. Then tap Allow.

Buoy in a TestFlight build needs a Pro or Business plan. TestFlight builds use Release, so `#if DEBUG` leaves Buoy out. Use your own flag there. See [Release Builds](./release-builds).

### Keys still work

You don't have to sign in. Your account key from [buoy.gg/dashboard/licenses](https://buoy.gg/dashboard/licenses) still works. Put it in `BUOY_LICENSE_KEY`, or pass it in. A key you pass in wins over the scheme's:

```swift
var config = BuoyConfig()
config.licenseKey = yourConfiguredKey
```

For CI and test runs, use a bot key. A team admin makes one on the Team page. See [Keys for bots and CI](../sign-in#keys-for-bots-and-ci).

## SwiftUI

Call `Buoy.start(config)` early, on the main actor. Buoy adds its own window over your app's first window. Add `.buoyDevTools()` to your root view only to set options, like `userRole`. The [Quick Start](./quick-start) has a complete app.

Start Buoy before you create networking singletons or state objects that create URLSessions. Calling it in `App.init` can still be too late for clients created by stored-property initializers.

## UIKit

Call `Buoy.start(config)` early, on the main actor. Sign-in works the same way. Use the same `#if DEBUG` guard as the SwiftUI example. Buoy adds its window to your first app scene when it becomes active. You don't need a scene delegate change.

To pick the scene yourself, call this in your scene delegate. Do it after you make your app window:

```swift
#if DEBUG
Buoy.install(in: windowScene)
#endif
```

Buoy shows on one scene only: the first one it is installed on. Set `config.headless = true` for no Buoy UI on the device.

## Buoy Desktop

Open [Buoy Desktop](../desktop) and sign in. The default broker URL is `http://localhost:42831`, which works for the iOS Simulator on the same Mac. For an iPhone or iPad, see [Physical Devices](./devices).

Select the Swift device in Desktop and repeat the request check. A device connection and a populated Network panel are separate checks. Keep the default per-install device ID unless you need to manage identity yourself; a custom ID must be unique for each device.

## Lifecycle

Calling `Buoy.start` again returns the existing runtime and can update a supplied account key. It does not change the broker URL or device identity of a running session, so set the initial configuration before starting.

`Buoy.runtime?.stop()` stops the runtime connection and account lifecycle. `BuoyDevTools.uninstall()` removes the overlay. Neither call removes networking hooks that were already installed.
