---
title: Quick Start
seoTitle: "Swift iOS DevTools Setup — add Buoy to a SwiftUI app"
id: swift-quick-start
description: "Add Buoy to a SwiftUI app in a few minutes: add the Swift package, start Buoy in a debug build, and check a captured network request."
---

This guide adds Buoy to a SwiftUI app and checks that a network request shows up. For UIKit, see [Installation](./installation#uikit).

## 1. Add the package

In Xcode, choose **File > Add Package Dependencies…** and enter:

```text
https://github.com/Buoy-gg/Buoy-Swift.git
```

Use the dependency rule **Up to Next Minor Version** from `0.1.0` and add the **Buoy** library to your app target.

## 2. Add your account key

In your app's Xcode Run scheme, add a `BUOY_LICENSE_KEY` environment variable containing your account key. Xcode passes it to the app only when it launches the app from that scheme.

## 3. Start Buoy

Start Buoy in the app's initializer and add the overlay to your root view. Both are behind `#if DEBUG`, so Release builds never run Buoy:

```swift
import Foundation
import SwiftUI
import Buoy

@main
@MainActor
struct BuoyDemoApp: App {
    init() {
        #if DEBUG
        var config = BuoyConfig()
        config.deviceName = "Swift demo"
        config.licenseKey = ProcessInfo.processInfo.environment["BUOY_LICENSE_KEY"]
        Buoy.start(config)
        #endif
    }

    var body: some Scene {
        WindowGroup {
            #if DEBUG
            RequestCheckView().buoyDevTools()
            #else
            RequestCheckView()
            #endif
        }
    }
}

@MainActor
struct RequestCheckView: View {
    @State private var result = "Ready"

    var body: some View {
        VStack(spacing: 16) {
            Text(result)
            Button("Send test request") {
                Task { await sendRequest() }
            }
        }
        .padding()
    }

    private func sendRequest() async {
        guard let url = URL(string: "https://example.com/") else { return }
        let configuration = URLSessionConfiguration.ephemeral
        configuration.requestCachePolicy = .reloadIgnoringLocalCacheData
        let session = URLSession(configuration: configuration)
        defer { session.finishTasksAndInvalidate() }
        do {
            let (_, response) = try await session.data(from: url)
            if let response = response as? HTTPURLResponse {
                result = "HTTP \(response.statusCode)"
            }
        } catch {
            result = error.localizedDescription
        }
    }
}
```

In an existing app, keep your root view and add `.buoyDevTools()` to it. Start Buoy before you create networking singletons or state objects that create URLSessions; sessions created before `Buoy.start` are not captured.

## 4. Check the first request

Run the app in the iOS Simulator. Open the Buoy launcher and complete any account prompt. Then tap **Send test request**, open Network, and select the example.com request to see its URL, status and response.

If the request is missing, check that the button made a fresh request, the session was created after `Buoy.start`, and capture is on. Requests made before interception or account access started are not recorded.

## 5. Open it in Buoy Desktop

Open [Buoy Desktop](../desktop) on the same Mac and sign in. The simulator app connects to it automatically. For an iPhone or iPad, follow [Physical Devices](./devices).
