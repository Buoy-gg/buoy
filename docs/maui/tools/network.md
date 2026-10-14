---
title: Network
seoTitle: "Network for .NET MAUI"
id: maui-tools-network
description: "See the web calls your MAUI app makes, with headers, bodies, status and timing."
---

See web calls, headers, bodies, status, and timing.
Mock replies or slow calls to test faults.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

```text
builder.UseBuoyNetwork();
```

Do you make clients with `AddHttpClient`?
Then you are done.
Buoy sees every client the factory makes.
That covers named, typed and Refit clients.
Buoy runs after your own handlers.
So it sees the headers they add, like auth.

Do you make a client yourself?
Then make it with `CreateNetworkClient`:

```text
var client = services.CreateNetworkClient();
```

Wire this client into the code that makes calls.
You can pass your own handler to `CreateNetworkClient`.
Keep your auth and other client setup in place.
The [Quick start](../quick-start) shows a service setup.

Does your team share a list of calls to hide on buoy.gg?
Then those calls are hidden here too.
They show a TEAM badge in Filters.
Only a team admin can change them, on buoy.gg.

Buoy only sees calls in Debug builds.
A plain `new HttpClient()` skips Buoy.
Native SDK calls and web views skip it too.
You need account access to capture or change calls.
Some rules need Pro.
Release builds do not capture or change traffic.

See [all MAUI tools](../tools).
