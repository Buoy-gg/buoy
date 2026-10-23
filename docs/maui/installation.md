---
title: Install
seoTitle: "Install Buoy for .NET MAUI"
id: maui-installation
description: "Set up the MAUI package, sign in to your account, and add each tool. Keep Buoy out of the app you ship."
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

## Sign in

Run this beside the app's `.csproj`.

```sh
npx --package=@buoy-gg/core buoy login
```

Your browser opens buoy.gg. Click Allow.
Then the command writes a dev token to `.env.development.local`.
Buoy reads it when your app builds.
The token works in Debug builds for 30 days.
Then run `buoy login` again.
Build your app again after you sign in.
Keep the file out of source control.

### Sign in with a code

People can also sign in with a code.
Set `SignIn` in your Buoy setup:

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

### Test builds

A dev token does not work in a Release build.
So a test build, like TestFlight or an internal track, signs in with a code.
That build must keep Buoy on purpose.
`#if DEBUG` leaves Buoy out of Release, so the setup above does not run there.
Use your own guard for that build, on the package and the code.
See [If you keep Buoy in Release](./release-builds#if-you-keep-buoy-in-release).
Release access needs a paid plan.
Desktop sync also needs `EnableSyncInRelease = true`.

### Keys still work

You don't have to sign in.
Put your account key in an env file as `BUOY_KEY`.
A Free key goes in `.env.development.local`.
A paid key goes in `.env.local`.
Debug builds read both files, with the dev file first.
Release builds read only `.env.local` when Buoy is included.
Keep both files out of source control.

An app can also set `BuoyOptions.LicenseKey` itself.
That key wins over the build-time env key.
Use your app's own key source.
Do not hardcode keys.
Set `BuoyReadEnvKey=false` to stop build-time env reads.
Buoy saves a checked key in MAUI SecureStorage.

For CI and test runs, use a bot key.
A team admin makes one on the Team page.
See [Keys for bots and CI](../sign-in#keys-for-bots-and-ci).

With a sign-in or a dev token, your Buoy settings follow you to your other devices.
See [Your settings follow you](../sign-in#your-settings-follow-you).

## Pick your tools

Use `builder.UseBuoy()` once, before `builder.Build()`.
It turns on each tool that needs nothing from your app.
Routes, Scenarios, Impersonate, Notifications, Sentry and Ask Buoy need their own call.
To give a tool options, call its setup before `UseBuoy()`.
If you call it after, the app stops with a message that says so.
Set `options.AutoTools = false` to add each tool yourself.
Use the [Quick start](./quick-start) for a full small setup.
The [tool guide](./tools) shows how each source connects.

Buoy adds its view to the MAUI window.
Keep your app's pages, Shell, and base class.
A Free or Pro account opens tools in Debug builds.
Some tools and tasks need Pro.
The app still runs before you sign in.
Buoy shows a sign-in button.

## Before you ship

Follow [Release builds](./release-builds) to remove Buoy from shipped builds.
A hidden button does not remove its DLLs.
If you choose to keep Buoy, Release access needs a paid plan.
Desktop sync also needs `EnableSyncInRelease = true`.
Network capture still only works in Debug builds.
