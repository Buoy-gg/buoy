---
title: Ask Buoy
seoTitle: "Ask Buoy in Flutter"
id: flutter-tools-ask-buoy
description: "Chat with Buoy inside your Flutter app. It reads live state through your Buoy tools, and you can undo most changes."
---

Ask Buoy is an AI chat inside your Flutter app.
It uses the Buoy tools you set up.
It can read app data, like web calls and storage.
It can also make changes, like a fake web call.
It needs Pro, Business or a trial.
It is in beta.

Use Buoy's hosted AI, or use your own model.

## Set up chat

Pass `askBuoy` to the `BuoyDevTools` you already have.
Hosted AI also needs `signIn`.

```dart
MaterialApp(
  builder: (context, child) => BuoyDevTools(
    signIn: const BuoySignIn(appId: 'com.example.app'),
    askBuoy: hostedAskBuoy(
      policy: {'readOnly': true},
    ),
    child: child ?? const SizedBox.shrink(),
  ),
)
```

`readOnly` lets it look but not change things.
Remove it once you trust what it does.

It shows up in the dial as **ASK BUOY**.
Each person signs in with Buoy in your app first.
See [Sign in with Buoy](../../sign-in).

## What it can do

Chat can use these tools when you set them up:

- Network: read web calls, fake a reply, make a call fail.
- Storage: read and edit saved keys.
- Routes: read the screen stack and go to a route.
- Env, Console and Events: read values, logs and the timeline.
- Impersonate: see the app as one test user.
- Images and Perf Monitor: read image loads and app speed.
- Riverpod: read providers. It can't change them.

Each tool needs its own [setup](./network).
Chat can't read or tap the screen in Flutter.
Image Overlay is not in the chat.

Try one safe ask first: *"List the installed tools."*
Nothing in the app should change.

## Changes and Undo

Normal changes run right away.
Big changes, like wiping storage, wait for your tap.
The changes bar at the top counts what changed.
Tap **Undo** to put most of it back.
A change it can't undo is marked **permanent**.

To ask before every change, use this:

```dart
hostedAskBuoy(
  policy: {'requireApproval': ['write', 'destructive']},
)
```

## Pick a model

Hosted AI uses Claude first.
If Claude is down, Luna answers instead.
Pick Luna first in Ask Buoy's settings.
You can also pick it on buoy.gg, in the Ask Buoy tab.
The pick follows you to each device.

Settings also show your credits for the week.

## Use your own model

Point Ask Buoy at your own server.

```dart
BuoyDevTools(
  askBuoy: AskBuoyConfig(
    endpoint: 'https://ai.acme.com/v1/messages',
    protocol: 'anthropic', // or 'openai'
    model: 'YOUR_MODEL_ID',
    policy: {'readOnly': true},
    headers: (meta) async => {
      'Authorization': 'Bearer ${await auth.getToken()}',
    },
  ),
  child: child ?? const SizedBox.shrink(),
)
```

`headers` runs before each web call, so short tokens work.
Your server must check the token and the user.
Keep your AI key on the server.

## What it saves

The chat is saved, so it comes back after a restart.
Tool results are not saved.
The undo list is saved, so Undo still works.
Turn saving off with `persistTranscript: false`.

The full rules on what each ask sends are in [Ask Buoy](../../tools/ask-buoy#what-it-sends-and-what-it-saves).
