---
title: Tools
seoTitle: "Buoy tools for .NET MAUI"
id: maui-tools
description: "Set up each MAUI tool. See what it reads and where it differs from React Native."
---

Add these calls beside `UseBuoy` in your app setup.
All builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](./release-builds).
Here, `services` is your app's service provider.

## Network

See web calls, headers, bodies, status, and timing.
Mock replies or slow calls to test faults.

```text
builder.UseBuoyNetwork();
var client = services.CreateNetworkClient();
```

Wire this client into the code that makes calls.
You can pass your own handler to `CreateNetworkClient`.
Keep your auth and other client setup in place.
The [Quick start](./quick-start) shows a service setup.

In Debug builds, this handler sends calls to Buoy.
It does not hook all clients like RN does.
A plain HTTP client skips Buoy.
Native SDK calls and web views skip it too.
You need account access to capture or change calls.
Some rules need Pro.
Release builds do not capture or change traffic.

## Env

See app values and check them against your rules.

```text
using Buoy.Core.Env;

builder.UseBuoyEnv(
    vars: new Dictionary<string, string>
    {
        ["API_URL"] = "https://api.example.com",
        ["DEBUG_MODE"] = "true",
    },
    requiredEnvVars:
    [
        "API_URL",
        RequiredEnvVar.Type("DEBUG_MODE", "boolean"),
    ]);
```

Sign in to use this tool.
It is free.
Only values you pass in show up here.
MAUI does not read your whole process or env files.
You cannot edit values in this view.
Use `EnvStore.Configure` to send new values from your app.

## Storage

Read, edit, and check known app keys.
The stores use Preferences and SecureStorage.

```text
builder.UseBuoyStorage(options =>
{
    options.Preferences.RegisterKey<string>("theme");
    options.Preferences.RegisterKey<int>("launchCount");
    options.SecureStorage.RegisterKey("auth.token");
});
```

MAUI has no API to list all keys.
List old keys with the types they hold.
Do this for secure keys on each app launch.

Get the store adapters from app services.
Use `PreferencesStorageAdapter` for plain keys.
Use `SecureStorageAdapter` for secure keys.
Their reads and writes feed the event log.
The open tool polls known Preferences keys.
That can miss short changes and direct reads.
SecureStorage reads keys on open and on Refresh.
This port has no MMKV or biometric lock tool.
You can add your own `IStorageAdapter` through `options.Stores`.

## Events

See web calls and store events in one list.
Search, filter, pin, or copy the rows you need.

```text
builder.UseBuoyNetwork();
builder.UseBuoyStorage();
builder.UseBuoyEvents();
```

The built-in sources are Network and the Preferences store.
They use the IDs `network` and `storage-async`.
Add your own source through the setup callback.
It takes an `EventSourceRegistry` with `Register`.

MAUI has no Redux, Zustand, Jotai, or Query sources.
Routes and Render are not sources here either.
The Routes tool has its own log.
SecureStorage events are not a built-in Events source.

## Console

See app logs with search and level filters.
Each path is off until you opt in.

```text
builder.UseBuoyConsole(options =>
{
    options.CaptureLogging = true;
    options.CaptureDiagnostics = true;
    options.CaptureStandardOutput = true;
});
```

These read `ILogger`, Debug and Trace, and text output.
Text from standard error gets the error log level.
Buoy keeps the app's old output path working too.
Turn on Preserve log to keep logs across launches.

MAUI has no JS console or JS stack frames.
It does not read `logcat` logs back into the app.
Debug calls need a Debug build.
Trace calls need the `TRACE` build flag.
The host's log filters can still drop a call.

## Clock

Set a date or jump ahead.
Test what happens when a token expires.
Pro adds freeze and speed.
It also saves time and adds token tests.

```text
builder.UseBuoyNetwork();
builder.UseBuoyClock();

var now = BuoyClock.Now;
await Task.Delay(TimeSpan.FromHours(1), BuoyClock.TimeProvider);
```

Read app time through `BuoyClock`.
You can use `TimeProvider` from app services too.
`BuoyClock.Now` gives a UTC `DateTimeOffset`.
.NET cannot patch `DateTime.Now` or `DateTimeOffset.UtcNow`.
Those still read real time, unlike RN's patched Date.

The clock does not speed up OS or UI timers.
Freeze and speed change dates; timers keep real speed.
Forward jumps can run due timers from this clock.
The jump switch turns this on or off.
Token fault tests need the Buoy Network client.

## Lifecycle

Test app states, themes, links, and power values.
This tool needs Pro.

```text
builder.UseBuoyLifecycle();

// Bind a window after the app creates it.
var lease = BuoyLifecycle.AttachWindow(window, services);
window.Destroying += (_, _) => lease.Dispose();
```

Read `BuoyLifecycle.CurrentState` for the test state.
Use `StateChanged` and `Signal` to hear app events.
Read `BuoyLifecycle.Power` for test power values.
Drop your event handlers when the view is done.

Tests do not pause threads or stop the process.
Calls to `Battery.Default` still read real power.
Theme tests also set the app's MAUI theme.
MAUI has no RN JavaScript reload.
There is no app reload hook for relaunch here.
Real OS state events end a live state test.

## Routes

See your page stack and the route log.
Open a known path or go back in Shell.
MAUI has no file routes, so list your route shapes.

```text
Routing.RegisterRoute("about", typeof(AboutPage));
Routing.RegisterRoute("user", typeof(UserPage));
Routing.RegisterRoute("docs", typeof(DocsPage));

builder.UseBuoyRoutes(options =>
{
    options.Templates.Add(new("/about", "about"));
    options.Templates.Add(new("/user/[id]", "user"));
    options.Templates.Add(new("/docs/[...path]", "docs"));
});

// Bind your Shell once it exists. Keep the lease.
var routes = BuoyRoutes.Attach(shell, services);
```

Use your app's page types in these route calls.
The path `/user/42` maps to `user?id=42`.
Catch-all paths pass one query value, joined with slashes.
Use `RouteTemplate.QueryKeys` to map names that differ.
Map `/` too if you want the Home action.
Dispose the lease when that Shell is done.

For `NavigationPage`, use its `BuoyRoutes.Attach` overload.
Tag pages with `BuoyRoutes.SetPageRoute` and their values.
That host shows push, pop, and stack changes.
The Go action needs Shell.
There is no Expo file tree to read.

## Location

Test a place, route, weak signal, or coarse fix.
This tool needs Pro.

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

## Permissions

Test how your app reacts to each grant state.
Sign in to use this tool.
It is free.

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
