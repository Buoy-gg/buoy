---
title: Permissions
seoTitle: "Permissions for .NET MAUI"
id: maui-tools-permissions
description: "Test how your MAUI app reacts to each permission state."
---

Test how your app reacts to each grant state.
Sign in to use this tool.
It is free.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

```text
builder.UseBuoyPermissions();

var status = await BuoyPermissions.CheckStatusAsync<Permissions.Camera>();
var answer = await BuoyPermissions.RequestAsync<Permissions.Camera>();
await BuoyPermissions.OpenSettingsAsync("camera");
```

You can use `IBuoyPermissions` from app services too.
Calls to MAUI `Permissions` skip Buoy.
A test grant does not let you use the real camera.
With no test state, the wrapper uses the real API.
Keep your app's OS rights and usage text in place.

MAUI has no `blocked` state.
RN's `blocked` maps to MAUI's `Restricted`.
Real `Disabled` reads still reach your app as `Disabled`.
The sync log maps that state to `denied`.
MAUI has no built-in app tracking prompt.
Use `BuoyNotificationsPermission` for a real push prompt on either phone.

Listen to `BuoyPermissions.Changed` to refresh app reads.
The notify switch turns test change events on or off.
Allow Once lasts for the current app run.
Test states do not change the phone's real grants.

See [all MAUI tools](../tools).
