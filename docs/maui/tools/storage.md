---
title: Storage
seoTitle: "Storage for .NET MAUI"
id: maui-tools-storage
description: "Read, edit and check Preferences and SecureStorage keys in your MAUI app."
---

Read, edit, and check known app keys.
The stores use Preferences and SecureStorage.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).
Here, `services` is your app's service provider.

```text
builder.UseBuoyStorage(options =>
{
    options.Preferences.RegisterKey<string>("theme");
    options.Preferences.RegisterKey<int>("launchCount");
    options.SecureStorage.RegisterKey("auth.token");
});
```

MAUI has no API to list all keys.
List old keys with the types they hold.
Do this for secure keys on each app launch.

Get the store adapters from app services.
Use `PreferencesStorageAdapter` for plain keys.
Use `SecureStorageAdapter` for secure keys.
Their reads and writes feed the event log.
The open tool polls known Preferences keys.
That can miss short changes and direct reads.
SecureStorage reads keys on open and on Refresh.
This port has no MMKV or biometric lock tool.
You can add your own `IStorageAdapter` through `options.Stores`.

See [all MAUI tools](../tools).
