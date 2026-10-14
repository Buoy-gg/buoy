---
title: Events
seoTitle: "Events for .NET MAUI"
id: maui-tools-events
description: "See web calls and store events from your MAUI app in one list."
---

See web calls and store events in one list.
Search, filter, pin, or copy the rows you need.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

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

See [all MAUI tools](../tools).
