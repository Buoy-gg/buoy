---
title: Routes
seoTitle: "Routes for .NET MAUI"
id: maui-tools-routes
description: "See the Shell page stack and route log, and open known paths."
---

See your page stack and the route log.
Open a known path or go back in Shell.
MAUI has no file routes, so list your route shapes.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

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

See [all MAUI tools](../tools).
