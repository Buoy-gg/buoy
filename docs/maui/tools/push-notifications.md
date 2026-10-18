---
title: Notifications
seoTitle: "Push notification testing for .NET MAUI"
id: maui-tools-push-notifications
description: "Send a test notification to your MAUI app. See what your app got and what it did."
---

Send a test notification to your app.
Then see each step your app saw.

## Set up

Add this call beside `UseBuoy` in your app setup.
Builder calls must come before `builder.Build()`.
They use `using Buoy.Maui;`.
Keep Buoy code in [Debug guards](../release-builds).

```text
builder.UseBuoyNotifications(options => options.AppId = "com.example.myapp");
```

On iOS, Buoy sits in front of your app's notification delegate.
Your delegate still gets every call.

To see the device token on iOS, pass it on from your `AppDelegate`:

```text
public override void RegisteredForRemoteNotifications(UIApplication app, NSData token) =>
    BuoyNotifications.ObserveDeviceToken(token.ToArray());
```

On Android, pass each start intent to Buoy in your `MainActivity`.
Do it in `OnCreate` and in `OnNewIntent`.

```text
BuoyNotifications.HandleIntent(Intent);
```

## Send a test

Open **NOTIFICATIONS**. The **Test** tab is first.

1. Type a title and a message.
2. Tap **Send notification**.
3. One second later, your app gets it.

The card then says what happened.
It says **Received by app** once your app gets it.
Tap the notification to test what it opens.

## See what your app got

The **Activity** tab lists each step.
You see when it was set to send, when the app got it, and when you tapped it.
Tap a row to see the full data.
Use the search box to find one.
The trash button clears the list.

The list stays after you close the app.
The power button starts and stops the log.

## Settings

Tap the gear to see what the log can do on this phone.
It also shows your notification permission and the device token.

## What MAUI can do

| | iOS | Android |
| --- | --- | --- |
| Local test notifications | Yes | Yes |
| Taps on a notification | Yes | Yes |
| Push from a server | Yes | No |
| Device token | Yes | No |

Android push needs Firebase, and Buoy does not add it.
React Native's Expo token is not in MAUI.
