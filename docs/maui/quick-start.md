---
title: Quick start
seoTitle: "Add Buoy to a .NET MAUI app"
id: maui-quick-start
description: "Add Buoy to your MAUI app. Then check a web call in the app."
---

This guide adds Buoy and checks one web call.
Start with a Debug build of your MAUI app.
See [Install](./installation) for the .NET and OS floors.

## Let your agent do it

Copy this prompt to your coding agent.
Review its edits when the setup is done.

<!-- ::agent-install platform="maui" where="docs-maui-quick-start" -->

## 1. Add the package

Run this in the folder with your app's `.csproj`.
This command is for the first NuGet beta.
It will work once that beta is published.

```sh
dotnet add package Buoy.Maui --prerelease
```

This also brings in `Buoy.Core`.
Keep the resolved version in your project file.
Make its package entry Debug-only as shown in [Release builds](./release-builds).

## 2. Add your key

Run this in the same app folder.

```sh
npx buoy login
```

Sign in with your Free or Pro account.
The command writes a key to a local env file.
Buoy reads that file when the app builds.
Keep that file out of source control.

## 3. Start Buoy

Add these lines to your app's `MauiProgram.cs`.
Keep your app's own setup and service calls.
Call `UseBuoy` just once, before `Build`.

```text
#if DEBUG
using Buoy.Maui;
#endif
using Microsoft.Extensions.DependencyInjection;

var builder = MauiApp.CreateBuilder().UseMauiApp<App>();

#if DEBUG
builder.UseBuoyNetwork();
builder.UseBuoy();
builder.Services.AddSingleton<HttpClient>(services =>
    services.CreateNetworkClient());
#else
builder.Services.AddSingleton<HttpClient>(_ => new HttpClient());
#endif

return builder.Build();
```

Buoy adds its own view to your app window.
You do not need to wrap or replace pages.
If your app has an HTTP client, keep its setup.
Use the [Network guide](./tools#network) to wire that client.
Do not add a second client your app never uses.

## 4. Check a web call

Build and run your app in Debug mode.
Open Buoy once your account has access.
Have your app make a fresh call through that client.
Open Network and find its URL and status.

A plain `new HttpClient()` does not use Buoy.
If the call is missing, check the app's client first.
Then check your account and the tool's capture switch.
The package alone does not prove calls are captured.

## 5. Add more tools

Use the [tool guide](./tools) to add each source.
For in-app chat, see [Ask Buoy](./ask-buoy).
To link your phone, see [Devices and Desktop](./devices).
