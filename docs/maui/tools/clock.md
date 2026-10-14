---
title: Clock
seoTitle: "Clock for .NET MAUI"
id: maui-tools-clock
description: "Set the time your MAUI app reads, jump ahead and test token expiry."
---

Set a date or jump ahead.
Test what happens when a token expires.
Pro adds freeze and speed.
It also saves time and adds token tests.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

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

See [all MAUI tools](../tools).
