---
title: Console
seoTitle: "Console for .NET MAUI"
id: maui-tools-console
description: "See ILogger, Debug, Trace and text output from your MAUI app."
---

See app logs with search and level filters.
Each path is off until you opt in.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

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

See [all MAUI tools](../tools).
