---
title: Install
seoTitle: "Install Buoy for .NET MAUI"
id: maui-installation
description: "Set up the MAUI package, your account, and each tool. Keep Buoy out of the app you ship."
---

## Check your app

Use .NET 10.
Add the MAUI workload for your target.
The repo pins SDK and workload set `10.0.200`.
Use the Apple build tools your workload needs.
The package uses `Microsoft.Maui.Controls` version `10.0.20`.

| Target | Lowest OS version |
| --- | --- |
| `net10.0-ios` | iOS 15.0 |
| `net10.0-android` | Android API 24 |
| `net10.0-maccatalyst` | Mac Catalyst 15.0 |

`Buoy.Core` targets `net10.0`.
Tests ran on iOS and Android.
The Mac Catalyst target has no test claim here.
There is no Windows target in this package.
Does your `.csproj` list a Windows target?
Then the plain package entry breaks Windows builds.
Use the Windows guard from the [release guide](./release-builds).
Remove any Buoy entry that has no guard.

## Add the package

Use this once the first beta is on NuGet.
Run it in your app's project folder.

```sh
dotnet add package Buoy.Maui --prerelease
```

`Buoy.Maui` brings in `Buoy.Core` for you.
You do not need one package per tool.
Keep the version that NuGet puts in your `.csproj`.
Guard that entry with the [release guide](./release-builds).

## Set up your account

Run this beside the app's `.csproj`.

```sh
npx buoy login
```

A Free key goes in `.env.development.local`.
A dev token uses that same file.
A paid key goes in `.env.local`.
Debug builds read both files, with the dev file first.
Release builds read only `.env.local` when Buoy is included.
Build your app again after you sign in.
Keep both files out of source control.

An app can also set `BuoyOptions.LicenseKey` itself.
That key wins over the build-time env key.
Use your app's own key source.
Do not hardcode keys.
Set `BuoyReadEnvKey=false` to stop build-time env reads.
Buoy saves a checked key in MAUI SecureStorage.

For sign-in with a code, use this setup:

```text
#if DEBUG
builder.UseBuoy(options =>
{
    options.SignIn = new BuoySignInOptions();
});
#endif
```

Add `using Buoy.Maui;` inside a Debug guard too.
At [Sites](/dashboard/sites), add `app:` plus your app ID.
By default, it uses the bundle or package ID.
Testers can then scan the code and sign in.
Hosted [Ask Buoy](./ask-buoy) needs that sign-in session.
A license key alone does not set up hosted chat.

## Pick your tools

Add tool calls before `builder.Build()`.
Use `builder.UseBuoy()` once for the host.
Use the [Quick start](./quick-start) for a full small setup.
The [tool guide](./tools) shows how each source connects.

Buoy adds its view to the MAUI window.
Keep your app's pages, Shell, and base class.
A Free or Pro account opens tools in Debug builds.
Some tools and tasks need Pro.
The app still runs with no key.
Buoy shows a sign-in button.

## Before you ship

Follow [Release builds](./release-builds) to remove Buoy from shipped builds.
A hidden button does not remove its DLLs.
If you choose to keep Buoy, Release access needs a paid plan.
Desktop sync also needs `EnableSyncInRelease = true`.
Network capture still only works in Debug builds.
