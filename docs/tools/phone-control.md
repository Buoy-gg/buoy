---
title: Phone Control
seoTitle: "Phone Control — let your AI agent drive a whole iPhone or Android phone, not just your app"
id: tools-phone-control
description: "Let your AI agent drive a whole test phone from your Mac: Apple's pop-ups, Settings, the lock screen and other apps. Pair a phone once, and it works with no Buoy app on it."
---

Your AI can get stuck on a phone. A pop-up asks for the camera. The phone locks. A step needs Settings. Buoy's other tools only see your app, so the AI has to stop and wait for you.

Phone Control lets the AI drive the whole phone from your Mac. It can tap Apple's pop-ups, open Settings, unlock the phone and use other apps. It even works with no Buoy app on the phone.

It works on a physical iPhone and an Android phone. It's free with a Buoy account.

## What you need

- A Mac, signed in to Buoy. A free account works.
- The phone plugged in with a cable.
- **iPhone:** Xcode, and the phone set up for development in Xcode. Turn on **Enable UI Automation** in Settings > Developer.
- **Android:** `adb`, with USB debugging on.

## Pair a phone

Pair each test phone once. Run this in Terminal:

```bash
npx @buoy-gg/mcp phone-pair
```

With no id, it lists your phones. Then pair one by its id:

```bash
npx @buoy-gg/mcp phone-pair <udid or serial>
```

The command asks a person to type **y**. So your AI can't pair a phone on its own.

Now `list_devices` shows the phone as `phone:<id>`. Your AI passes that as `deviceId`.

To unpair a phone, add `--forget`.

## What works on a paired phone

These tools take a `phone:` id, with no Buoy app on the phone:

| Tool | What it does |
|---|---|
| `screenshot_component` | A picture of the whole screen |
| `native_inspect` | Lists all that is on the screen |
| `native_tap` | Taps by label or by spot |
| `native_type` | Types with the real keyboard |
| `native_swipe` | Swipes and scrolls, even to the end of a list |
| `native_pinch` | Zooms and turns with two fingers (iPhone) |
| `device_control` | Home, Lock, Unlock and more |
| `phone_driver` | Reads and taps the whole iPhone screen |
| `run_flow` | Runs steps, saves them and plays them back |
| `screen_recording` | Records a video of the screen |

Tools that read your app's data still need your app. That means web calls, logs and storage.

## The phone driver (iPhone)

An iPhone only shows Buoy its screen as a picture. The phone driver lets Buoy read every button on it instead.

Start it with `phone_driver` and `action: "start"`. It puts a small Apple test helper on the phone. The helper is Appium's WebDriverAgent.

With it on:

- `native_inspect` lists all that is on the screen. That includes Apple's pop-ups, Settings, the home screen and other apps.
- `native_tap` with a `label` can tap any of it, like **Allow** on a pop-up.
- `open_app` opens an app by its `bundleId`. Settings is `com.apple.Preferences`.

The first start builds the helper. That takes 1 to 2 minutes. Later starts take about 5 to 10 seconds. It even starts on the lock screen.

It stops when the MCP stops. It also stops after 30 idle minutes, or with `action: "stop"`.

The helper only listens on the cable. Other computers on your Wi-Fi can't reach it.

On Android, Buoy reads the whole screen with no helper.

## Home, Lock and Unlock

`device_control` can press `home` and `lock`.

Use `unlock` when a phone locks itself in a run. Buoy wakes the phone and types its passcode.

To let Buoy type it, save the passcode once on your Mac:

```bash
npx @buoy-gg/mcp phone-passcode <udid or serial>
```

Buoy keeps it in your Mac's login keychain. It never shows it to your AI or writes it to a log. To remove it, add `--forget`.

Some rules keep the phone safe:

- Buoy tries a passcode at most twice. So it won't run up wrong tries.
- After a phone restarts, a person must unlock it once. Apple and Android both require this. Buoy tells your AI when that happens.
- Face ID needs a face. Buoy types the passcode instead.

To keep a phone going while you're away:

- **iPhone:** set Auto-Lock to Never, and keep it on a charger.
- **Android:** `device_control` with `stay_awake` keeps the screen on while plugged in. Pass `on: false` to undo it.

## Flows and videos

`run_flow` works on a paired phone. Each tap is a real touch, found by its label. Typing uses the real keyboard. Waits and checks read the words on the screen. Saved flows play back the same way.

A few steps need your app, so they fail with a clear note: `reload`, `set` and `snapshot`. Buoy won't type a new line outside your app either. In a chat app, that could send a message.

`screen_recording` records the phone's screen. Most iPhones record through Xcode.

Some iPhones can't record through Xcode, like the iPhone SE. Buoy records those over the cable instead, at up to 60 frames a second.

Two phones on cables can have the same name, like "iPhone". Buoy tells them apart by screen size. If the sizes match too, it asks you to unplug one or rename it.

## Risky buttons

Buoy won't tap a button that says pay, buy or subscribe. That goes for your app too.

Outside your app, it also won't tap send, delete, erase, reset or call. So it won't wipe the phone or send a text by mistake.

A row that only opens another page is fine, like **Transfer or Reset iPhone**. The button on the next page, like **Erase All Content and Settings**, is not.

If the person asked for exactly that tap, the AI can pass `allowRisky: true` to `native_tap`.

## How fast it is

We timed these on an iPhone SE and an iPhone 14 Plus:

| Step | Time |
|---|---|
| Tap a button by its exact label | 1.2 to 1.6 s |
| Unlock with a saved passcode | about 6 s |
| Start the phone driver, after the first time | about 4 to 10 s |
| Unlock an Android emulator with a PIN | 3.3 s |

## Keep it to test phones

Pairing and the saved passcode are steps where a person says yes. They are not locks.

An AI that can run commands on your Mac could still read your keychain. It could also write the pair file. So use Phone Control on test phones, not your own phone.

## When something goes wrong

Ask your AI to run `buoy_doctor`. Its **Phones** part shows each phone:

- Is it paired?
- Is a passcode saved?
- Is it locked?
- Was it unlocked since it restarted?
- Is the phone driver on?

It also says what to fix.

## Questions

**Do I need a Buoy app on the phone?** No. That's what pairing is for. With your app on the phone, the same tools work on your app too.

**Is it free?** Yes, with a free Buoy account.

**Does it work over Wi-Fi?** Screen video needs the cable. Keep the phone plugged in for the rest too.

**Can it use Face ID?** No. Buoy types the passcode instead.

**Does it work on the iOS Simulator?** The simulator already lets Buoy tap Apple's pop-ups, so it doesn't need pairing. `device_control` can press Home, Lock and Unlock there too.
