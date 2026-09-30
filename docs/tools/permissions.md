---
title: Permissions
seoTitle: "React Native Permissions Testing: fake denied, blocked and limited states"
id: tools-permissions
description: "Make your React Native app see any permission state (not asked, allowed, limited, denied or blocked) for location, camera, notifications and more, without changing device settings or reinstalling."
---

<!-- ::platform-badge platform="both" -->

Every permission screen in your app has several paths: the first ask, the refusal, the "turn it on in Settings" message, approximate location, and a user who said no months ago. To see them on a real device you'd have to dig through Settings or reinstall the app. Permissions lets you pick the state your app sees, and only your app sees it. The device's real settings stay the same.

## Install

<!-- ::PM npm="npm install @buoy-gg/permissions" yarn="yarn add @buoy-gg/permissions" pnpm="pnpm add @buoy-gg/permissions" bun="bun add @buoy-gg/permissions" -->

Permissions shows up in your `FloatingDevTools` menu on its own. If your app checks a permission while it starts up, import it first in your entry file:

```js
// index.js
import "@buoy-gg/permissions";
import "expo-router/entry";
```

It works with these libraries, with no setup:

- Expo modules: expo-location, expo-camera, expo-notifications, expo-image-picker, expo-media-library, expo-contacts, expo-calendar, expo-tracking-transparency, expo-audio, expo-sensors and expo-maps, including their `use…Permissions` hooks
- React Native's `PermissionsAndroid`
- react-native-permissions

## How to use it

Open Permissions and you'll see the permissions your app has checked. Tap one and pick what the app sees:

| Option | What your app sees |
|---|---|
| Phone's Setting | What the device really says. This is the default. |
| Not Asked | The app has never asked. The next request shows a prompt. |
| Allowed | Full access. |
| Approximate, Selected Photos, Selected Contacts, Provisional | Limited access. Only the permissions and platforms that have it offer it. |
| Denied | The user said no. On Android the app can ask once more. On iOS it can't, so the app has to send the user to Settings. |
| Blocked | Android only: the user said no twice, so only Settings can change it. |
| Restricted | iOS only: Screen Time or device management turned it off. |

A simulated row shows its value in green, with the phone's real setting under it. Permissions your app can ask for but hasn't yet are under Other Permissions. Use Real Permissions puts every row back.

A small strip stays on screen while anything is simulated, so you don't forget. Tap it to open Permissions. Its X puts every permission back, and Undo brings them back for a few seconds after. Changes stay after a reload.

## What happens when the app asks

While a permission is simulated, the real system prompt never shows, because tapping it would change the device's real setting. When your app asks from Not Asked, Buoy shows its own prompt with the same buttons, including Allow Once. On iOS it shows your purpose string when your Expo config sets it (`ios.infoPlist` or a config plugin option like expo-camera's `cameraPermission`). Pick an answer and the permission moves to that state, the way the phone would. Allow Once goes back to Not Asked when the app restarts.

When your app opens its Settings page (`Linking.openSettings()` or an `app-settings:` link), Buoy shows a small Settings stand-in with the options the Settings app uses, like Never, Ask Next Time and While Using the App. When you pick one, your app gets the same "back from the background" event it would get from the real Settings app.

Changing a row sends that event too, so screens that check again when the app comes back update right away. If a screen read the permission before the change and hasn't read it since, the row says so and offers Reload App.

## What stays real

Native code that checks the device itself still sees the real setting. While a permission is refused, calls like `getCurrentPositionAsync`, `launchCameraAsync` and media library reads fail with the same error the real module throws, and Approximate location rounds positions to about 2 km. The camera preview itself can't be faked, so `CameraView` still follows the device.

When you pick Allowed but the phone hasn't allowed the permission yet, the permission's page offers Show Real Prompt. It shows the real prompt once so the device can grant it. On a simulator, the [MCP server](../mcp) can also grant, revoke or reset the real setting for you.

## FAQ

### Does this change my phone's settings?

No. Only your app sees the new state. Use Show Real Prompt, or the MCP server's `system_permission` on a simulator, to change the real setting.

### Why does my screen still show the old state?

It hasn't checked again yet. Most screens check when they open or when the app comes back to the front. Go back and open the screen again, or tap Reload App under the row.

### Can AI agents use it?

Yes. The [MCP server](../mcp) has `get_permissions`, `permission_action` and `system_permission`, and [Ask Buoy](./ask-buoy) can do it when you ask, like "show me the store finder when location is denied". Buoy Desktop has a Permissions panel too.
