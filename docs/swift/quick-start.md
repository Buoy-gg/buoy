---
title: Quick Start
seoTitle: "Swift iOS DevTools Setup — add Buoy to a SwiftUI app"
id: swift-quick-start
description: "Add Buoy to a SwiftUI app in a few minutes: add the Swift package, start Buoy in a debug build, and check a captured network request."
---

This guide adds Buoy to a SwiftUI app and checks that a network request shows up. For UIKit, see [Installation](./installation#uikit).

## Let your agent do it

Your coding agent can set up Buoy for you.
Use Claude Code, Cursor or Codex. Copy the prompt and paste it into your agent. Then review its changes.

<!-- ::agent-install platform="swift" where="docs-swift-quick-start" -->

To install by hand, follow the steps below.

## 1. Add the package

In Xcode, choose **File > Add Package Dependencies…** and enter:

```text
https://github.com/Buoy-gg/Buoy-Swift.git
```

Use the dependency rule **Up to Next Minor Version** from `0.1.0` and add the **Buoy** library to your app target.

## 2. Sign in

Sign in from your project folder:

```bash
npx --package=@buoy-gg/core buoy login
```

Your browser opens. Click Allow. Then the command writes a dev token to `.env.local` as `BUOY_LICENSE_KEY`.

In Xcode, open **Product > Scheme > Edit Scheme > Run > Arguments**. Add `BUOY_LICENSE_KEY` there, under Environment Variables. Its value is in `.env.local`. Buoy reads it by itself. Xcode passes it only when it runs the app from that scheme. Leave **Shared** off for the scheme, so the token stays out of git.

The token works in dev builds for 30 days. For TestFlight builds, see [Test builds](./installation#test-builds).

## 3. Start Buoy

Start Buoy in your app's `init()`. Keep it behind `#if DEBUG`, so Release builds never run Buoy. Buoy adds its own window over your app, so you don't add a view for it:

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
        Buoy.start(config)
        #endif
    }

    var body: some Scene {
        WindowGroup {
            RequestCheckView()
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

In an existing app, add the `init()` to your `App`. Start Buoy before your app makes its URLSessions, such as in shared clients or state objects. Buoy can't see a session made before `Buoy.start`.

To set options like `userRole`, add `.buoyDevTools(userRole: .internal)` to your root view. For no Buoy UI on the device at all, set `config.headless = true`. Desktop and the MCP still see the app.

## 4. Check the first request

Run the app in the iOS Simulator. Open the Buoy launcher and complete any account prompt. Then tap **Send test request**, open Network, and select the example.com request to see its URL, status and response.

If the request is missing, check that the button made a fresh request, the session was created after `Buoy.start`, and capture is on. Requests made before interception or account access started are not recorded.

## 5. Open it in Buoy Desktop

Open [Buoy Desktop](../desktop) on the same Mac and sign in. The simulator app connects to it automatically. For an iPhone or iPad, follow [Physical Devices](./devices).
