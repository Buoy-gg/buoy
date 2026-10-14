---
title: Env
seoTitle: "Env for .NET MAUI"
id: maui-tools-env
description: "See the app values you pass to Buoy and check them against your rules."
---

See app values and check them against your rules.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

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

See [all MAUI tools](../tools).
