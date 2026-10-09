---
title: Release builds
seoTitle: "Keep Buoy out of shipped MAUI apps"
id: maui-release-builds
description: "Use Debug guards for the package and app code. Check that your Release build has no Buoy DLLs."
---

Use two guards to keep Buoy out of shipped builds.
Guard its package in Release.
Guard all Buoy code too.
A hidden menu does not remove the SDK.

## 1. Guard the package

First add the package with the [install steps](./installation).
Keep the version that NuGet chose.
Add this guard to that `PackageReference`:

```xml
Condition="'$(Configuration)' == 'Debug'"
```

The entry should have this shape.
Use the beta version from your own app.

```xml
<PackageReference Include="Buoy.Maui"
                  Version="YOUR_RESOLVED_BETA_VERSION"
                  Condition="'$(Configuration)' == 'Debug'" />
```

Does your app have a Windows target?
Buoy has no Windows build, so skip Windows too.
Use this guard instead:

```xml
<PackageReference Include="Buoy.Maui"
                  Version="YOUR_RESOLVED_BETA_VERSION"
                  Condition="'$(Configuration)' == 'Debug' and $([MSBuild]::GetTargetPlatformIdentifier('$(TargetFramework)')) != 'windows'" />
```

Do not keep a second entry with no guard.
If you list `Buoy.Core` too, guard it.
Guard any `BuoyPlugin` build items you use.

## 2. Guard the code

Guard imports and setup calls with `#if DEBUG`.
Keep a normal client for Release builds.

```text
#if DEBUG
using Buoy.Maui;
#endif

#if DEBUG
builder.UseBuoyNetwork();
builder.UseBuoy();
builder.Services.AddSingleton<HttpClient>(services =>
    services.CreateNetworkClient());
#else
builder.Services.AddSingleton<HttpClient>(_ => new HttpClient());
#endif
```

With a Windows target, use `#if DEBUG && !WINDOWS` instead.
Also guard page hooks, fields, and types that name Buoy.
Give app code a real clock in Release.
Do the same for grant checks and place reads.
Your app must work when no Buoy types are present.

## 3. Check the Release build

Use your app's own target and build command.
For an iOS build check, for example:

```sh
dotnet build -c Release -f net10.0-ios -r iossimulator-arm64
```

Restore with `-c Release` too.
Do not reuse a Debug restore with `--no-restore`.
That restore still lists Buoy.

Check the Release package graph.
Check the final app files too.
There should be no `Buoy.Maui` or `Buoy.Core` entry.
Check that no other package pulls them in.
Test your app's normal paths without Buoy.
Use your real ship build for the final check.

## What was checked

We made a new app with `dotnet new maui`.
It used these guards, with the Windows check too.
Its Debug build had Buoy, and Buoy ran.
Its iOS and Android Release builds had no Buoy files.
We did not try a Windows build.

## If you keep Buoy in Release

Release access needs a paid plan.
Desktop sync also needs `EnableSyncInRelease = true`.
Network capture and traffic changes stay Debug-only.
Those runtime rules do not remove the package.
Use the guards above when you want Buoy left out.
