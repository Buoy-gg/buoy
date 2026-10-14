---
title: Location
seoTitle: "Location for .NET MAUI"
id: maui-tools-location
description: "Test a place, route, weak signal or coarse fix in your MAUI app."
---

Test a place, route, weak signal, or coarse fix.
This tool needs Pro.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

```text
builder.UseBuoyLocation(store =>
{
    store.Places.Add(new(40.31, -111.67, "North Shop"));
});

var geo = services.GetRequiredService<IGeolocation>();
var fix = await geo.GetLocationAsync();
```

Read through that service or `BuoyGeolocation.Default`.
Direct calls to `Geolocation.Default` skip Buoy.
The phone's real place does not change.
Keep your app's OS location grants and usage text.

MAUI has no built-in geofence API.
The wrapper has three calls for fence tasks:

- `StartGeofences` starts the task.
- `StopGeofences` stops it.
- `HasGeofences` checks if it exists.
These calls work while the app runs.
They do not add OS background wakeups.
Bind saved fence callbacks on each app launch.
Routes use real time, even when Clock is paused.

See [all MAUI tools](../tools).
