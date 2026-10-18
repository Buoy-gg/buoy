---
title: Sentry
seoTitle: "Sentry for .NET MAUI"
id: maui-tools-sentry
description: "See what your MAUI app sends to Sentry, what it may cost and what it dropped."
---

See each envelope your app sends to Sentry.
See what it may cost, and why an event went missing.

## Set up

Add Sentry to your app with `UseSentry`.
Then add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseSentry(options => { /* your options */ });
builder.UseBuoySentry();
```

The order of the two calls does not matter.
Buoy does not need the Sentry package itself.
It finds the Sentry SDK when the app runs.
With no SDK, the tool says "SDK not found."

Do you call `SentrySdk.Init` on your own?
Then pass your options to Buoy inside that callback:

```text
SentrySdk.Init(options =>
{
    // your options
    BuoySentry.Attach(options);
});
```

## The tabs

- **Feed**: each envelope, with its type, size and span count. Tap one to see its data as a tree.
- **Cost**: errors, spans, replays, logs and files, checked against a plan.
- **Drops**: events that were dropped, and why.
- **Audit**: your Sentry settings, graded. It also flags personal data in what you sent.

The Cost tab counts each span on its own.
So one transaction can count as many spans.
This is a guess from one run.
Your Sentry bill is the real number.

## How it works

Buoy reads each envelope as the SDK sends it.
It sends the envelope on as it was.
So Buoy does not change what Sentry gets.

Buoy also wraps your `BeforeSend` and `BeforeSendTransaction`.
It notes when they return `null`.
Your callback's answer always goes through as it was.

The list starts fresh each time the app starts.
The tool's window size is saved.

## What it can't see

The native SDK sends native crashes on its own.
They do not go through .NET, so Buoy can't see them.

See [all MAUI tools](../tools).
