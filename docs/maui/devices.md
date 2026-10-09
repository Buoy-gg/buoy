---
title: Devices and Desktop
seoTitle: "Link a MAUI app to Buoy Desktop"
id: maui-devices
description: "Point your phone at Buoy Desktop on your computer. Check the address, account, and sync state."
---

MAUI can sync tools with the Desktop app.
Use a Debug build for your first link.
Sign in to [Buoy Desktop](../desktop) and your app.
Your phone must be able to reach that app.

## Set the host

Set `BrokerUrl` in your existing `UseBuoy` call.
Use the IP of the computer that runs Desktop.
Put your own IP in the code below.

```text
#if DEBUG
builder.UseBuoy(options =>
{
    options.BrokerUrl = new Uri("http://192.168.1.20:42831");
    options.DeviceName = "My MAUI app";
});
#endif
```

Keep `ExternalSync` on; it is on by default.
Buoy does not search for a host on your LAN.
Buoy uses a WebSocket to reach that host.
It sends app and tool state to Desktop.
Use a network you trust for this test link.

With no host set, Buoy uses these URLs:

| App host | Default address |
| --- | --- |
| Android emulator | `http://10.0.2.2:42831` |
| iOS simulator | `http://127.0.0.1:42831` |
| Physical phone | `http://127.0.0.1:42831` |

On a phone, the last URL points to itself.
Set it to the host IP as shown above.
Your app's OS rules must allow the link.
On iOS, grant local network access if asked.
You may need to add local network text.
Put it in your app's plist.
A firewall must also allow the Desktop port.

## Check the link

Start Desktop and run your Debug app.
Pick the app by its device name in Desktop.
Make a web call through the Buoy client.
Check that it shows in both Network views.

You can read `BuoyRuntime.SyncStatus` in app code.
It has the state, URL, and last error.
A link alone does not prove the tools work.
Check each tool with a fresh app action.

## Limits of this guide

The code lets you set the Desktop host.
This docs check did not use a real phone.
The OS setup still needs a real phone check.
Do that before you share the first beta build.
See [Release builds](./release-builds) before you ship your app.
