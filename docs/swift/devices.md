---
title: Physical Devices
seoTitle: "Connect an iPhone to Buoy Desktop — Swift local network setup"
id: swift-devices
description: "Point a native iOS app on an iPhone or iPad at Buoy Desktop: BUOY_SOCKET_URL, the local network permission, and the App Transport Security entries iOS 17 requires."
---

The simulator reaches Buoy Desktop at `localhost`. A physical iPhone or iPad needs your computer's LAN address and two Info.plist changes.

## Point Buoy at your computer

The quickest way is a `BUOY_SOCKET_URL` environment variable in your Run scheme. Set it to the address alone (`192.168.1.20`) or a full URL (`http://192.168.1.20:42831`). Buoy uses it only when `socketURL` is left at its default.

To set it in code instead, do so before calling `Buoy.start`:

```swift
var config = BuoyConfig()
config.licenseKey = ProcessInfo.processInfo.environment["BUOY_LICENSE_KEY"]
if let brokerURL = URL(string: "http://192.168.1.20:42831") {
    config.socketURL = brokerURL
}
Buoy.start(config)
```

Replace the example IP address with your computer's address, and keep your app's startup guard around this code.

## Info.plist entries

Merge these entries into your app's Info.plist, keeping any transport-security configuration you already have:

```xml
<key>NSLocalNetworkUsageDescription</key>
<string>Connects to the Buoy desktop dashboard for debugging.</string>
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsLocalNetworking</key>
    <true/>
    <key>NSExceptionDomains</key>
    <dict>
        <key>192.168.0.0/16</key>
        <dict>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
        </dict>
    </dict>
</dict>
```

Since iOS 17, `NSAllowsLocalNetworking` alone no longer allows plain HTTP to a bare IP address, so the address also needs an `NSExceptionDomains` entry. Use the range that contains your computer's address (for example `10.0.0.0/8`) or the exact IP. CIDR keys require iOS 17; on iOS 16, list the exact IP.

Limit these entries to the builds that include Buoy, for example with a separate Info.plist per build configuration.

## Check the setup

On a physical device, `Buoy.start` prints a `[Buoy] setup:` line to the Xcode console when the URL still points at `localhost` or one of these entries is missing.

Allow local-network access when iOS asks, and make sure the device can reach the computer's broker port. These entries do not open a firewall. If the connection fails, check the connection error in the console.

Use a trusted development network. Account validation makes external requests, and device sessions cross your LAN.
