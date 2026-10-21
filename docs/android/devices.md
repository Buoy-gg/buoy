---
title: Physical Devices
seoTitle: "Connect an Android phone to Buoy Desktop"
id: android-devices
description: "Point a native Android app on a real phone at Buoy Desktop: adb reverse over USB, or your computer's address over Wi-Fi, plus the network security config Android needs."
---

The emulator and a phone on USB can both reach Buoy Desktop through `adb reverse`.
A phone on Wi-Fi needs your computer's address.

## Over USB

Plug in the phone and turn on USB debugging.
Then run:

```bash
adb reverse tcp:42831 tcp:42831
```

Now `localhost:42831` on the phone reaches your computer.
You don't change any code.
Run it again after you plug the phone back in.

## Over Wi-Fi

Tell Buoy your computer's address.
There are three ways. Pick one.

In code, before `Buoy.start`:

```kotlin
Buoy.start(this, BuoyConfig(socketUrl = "http://192.168.1.20:42831"))
```

In the debug manifest, with no code change:

```xml
<application>
    <meta-data android:name="gg.buoy.socketUrl" android:value="http://192.168.1.20:42831" />
</application>
```

From your computer, for a debug build:

```bash
adb shell setprop debug.buoy.socketUrl http://192.168.1.20:42831
```

The manifest and `setprop` ways only count when `socketUrl` is left at its default.
With those two ways, you can leave off the port. Buoy then uses 42831.
In code, always write the port.
Put your computer's own address in place of the example.

## Allow plain HTTP to your computer

Android blocks plain HTTP by default.
Add your computer's address to the debug network security config from the [Quick Start](./quick-start#5-let-buoy-reach-your-computer):

```xml
<domain-config cleartextTrafficPermitted="true">
    <domain includeSubdomains="false">localhost</domain>
    <domain includeSubdomains="false">10.0.2.2</domain>
    <domain includeSubdomains="false">192.168.1.20</domain>
</domain-config>
```

Keep this file in `src/debug` so release builds don't get it.

## Check the setup

Buoy writes a `setup:` line to logcat under the `Buoy` tag when something is wrong.
On a real phone, it warns when the address is `localhost`, since that points at the phone itself.
That's fine if you ran `adb reverse`.
It also warns when Android blocks plain HTTP to the address.

```bash
adb logcat -s Buoy
```

Make sure the phone can reach your computer on port 42831.
These settings do not open a firewall.

Use a network you trust.
Account checks go out to the internet, and device data crosses your Wi-Fi.
