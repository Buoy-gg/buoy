---
title: Lifecycle
seoTitle: "Lifecycle for .NET MAUI"
id: maui-tools-lifecycle
description: "Test app states, themes, links and power values in your MAUI app."
---

Test app states, themes, links, and power values.
This tool needs Pro.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

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

See [all MAUI tools](../tools).
