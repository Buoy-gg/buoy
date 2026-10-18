---
title: Impersonate
seoTitle: "Impersonate for .NET MAUI"
id: maui-tools-impersonate
description: "Test your MAUI app as another user. Buoy adds a header to your web calls."
---

Test your app as another user.
Search your own users and pick one.
Buoy then adds a header to each web call.
Your server decides what the header does.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyImpersonate(options =>
{
    options.SearchUsers = async (query, cancel) =>
    {
        var users = await adminApi.FindUsersAsync(query, cancel);
        return users
            .Select(u => new BuoyImpersonateUser(u.Id, u.Name, u.Email))
            .ToList();
    };
    // Optional: clear your caches when the user changes.
    options.ClearData = settings => cache.ClearAsync();
});
```

`BuoyImpersonateUser` is in `Buoy.Core.Impersonate`.
`adminApi` and `cache` stand for your own code.
Without `SearchUsers`, the Search tab says search is off.

## Which calls get the header

The header goes on calls from `IHttpClientFactory`.
That covers typed clients and Refit clients too.
It also goes on calls from `CreateNetworkClient`.
Do you make an `HttpClient` by hand?
Wrap its handler with `BuoyImpersonate.CreateHandler()`.

```text
var client = new HttpClient(BuoyImpersonate.CreateHandler());
```

The header is `x-impersonate-user-id` unless you change it.
Its value is the user's id.
Dev server calls, such as port 8081, never get it.

## How it works

Pick a user from Search or History.
A banner shows at the top of the screen.
Tap the power button to pause the header.
Tap the X to stop.

The header only goes out while you are signed in to Buoy.
Your pick is saved, so it comes back after a restart.

The Settings tab changes the header name and the banner.
It also has four clear switches.
Buoy passes all four to your `ClearData` code.
MAUI has no React Query or Redux, so you choose what each switch clears.

## Read it in your app

`BuoyImpersonate.CurrentUser` is the user calls go out as.
It is null when you are not impersonating, or when paused.
`BuoyImpersonate.Subscribe` runs your code when that changes.

See [all MAUI tools](../tools).
