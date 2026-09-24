# Push Notifications

Send APNs payloads to an installed iOS simulator app from Buoy Desktop. Inspect notification callbacks, presentation decisions, responses and device tokens from an Expo app with capture installed.

The sender works without a connected Buoy app or an APNs token. Inspection requires an app integration. A successful simulator send confirms injection; the event list shows which app callbacks actually ran.

::notifications-demo

## Install capture

Install `@buoy-gg/notifications` alongside `@buoy-gg/core`. For Expo apps, use the app's SDK-compatible version of `expo-notifications`:

```sh
pnpm add @buoy-gg/notifications
npx expo install expo-notifications
```

Add the `expo-notifications` config plugin and rebuild an app-specific development build if the native module is new. Expo Go is not a substitute for that build when testing remote notifications.

Call capture setup in the app entry point, before importing or rendering the root component. Pass the SDK instance your app already uses:

```ts
// notificationCapture.ts
import * as Notifications from 'expo-notifications';
import { installExpoNotificationCapture } from '@buoy-gg/notifications';

export const notificationRecorder = installExpoNotificationCapture(Notifications, {
  appId: 'com.example.myapp',
  providerVersion: '56.0.25', // Use the version installed in your app.
  // projectId: 'your-eas-project-id', // Enables explicit Expo token registration.
});
```

```ts
// index.ts: this import precedes the app or expo-router entry point.
import './notificationCapture';
import 'expo-router/entry';
```

Core discovers the Notifications preset and sync adapter when the package is installed. Enable the project's existing Buoy Desktop connection to stream its snapshot. The recorder continues writing locally when Desktop disconnects or the panel closes.

Capture starts with a 30-minute development session. Arm it again from **Notification settings** when it expires. Stopping capture persists across app restarts. The default storage adapter uses Expo FileSystem or AsyncStorage when available and surfaces write failures. Use a persistent storage backend for cold-launch tests; the inspector reports **Memory only** when it cannot confirm one. You can supply an existing AsyncStorage-compatible object as `storage`, with `durable: true` when it persists across process restarts.

Foreground sends also work with memory-only capture. The **Arm persistent capture** control requires a successful persistence acknowledgement before cold-launch preparation.

## App branding in the preview

Pass an optional `preview` object when installing capture:

```ts
preview: {
  appName: 'My app',
  iconUri: 'data:image/png;base64,...',
  backgroundColor: '#F5EBDC',
  textColor: '#502314',
}
```

Both mobile and desktop use this appearance. Desktop applies it only when its selected simulator app matches the connected app. Icons must be embedded PNG, JPEG, or WebP data URIs no longer than 32 KB; colors use six-digit hex values. Unsupported values fall back to the default preview. Branding affects Buoy's preview, not the operating system's notification banner.

## Test on mobile

Desktop and mobile use the same Test screen, notification card, Send notification button, inline result, and Activity tab. Open Test on the device, edit the message, then tap **Send notification**. Buoy starts capture and schedules a local notification after one second. Tap the OS notification and check the inline result or Activity.

Mobile sends through the app's installed Expo notification SDK. This tests receipt and tap handling without a push provider; it does not test remote delivery. Allow notifications in the app first. Foreground banners still depend on the app's presentation handler.

Under **Options**, choose a ten-second delay to leave the app before delivery, or add custom JSON data. The draft and result remain available when switching between Test and Activity. Raw APNs payloads and simulator target selection are desktop controls.

## Send a simulator notification

1. Open **Notifications → Test** in Buoy Desktop.
2. Choose a simulator and its installed app. Select that same app in Buoy's device switcher to inspect its responses.
3. Edit the title and message in the notification card, then click **Send notification**. Keep the app open for this first test. Buoy starts capture automatically when the connected app matches.
4. Tap the notification in the simulator. The result below the card updates as Buoy observes receipt, a tap, or an app outcome. An app outcome is an app-reported record; inspect it to check the destination.

Open **Activity** for the full event history and payloads. Use the settings button for capture controls, permissions, and **Device tokens**.

**Options** contains background and launch-from-notification flows, custom data, raw APNs JSON, saved notifications, and send history. The flow selector provides instructions; it does not move the app to the background or close it. Composed sends get a unique `__buoyTestId`. Raw JSON is sent unchanged, and payloads without a test ID cannot be matched to the inline result.

The simulator payload limit is 4096 UTF-8 bytes. Validation runs in the renderer and again in Electron main. Every send rechecks the exact simulator and bundle ID; Buoy never substitutes another booted device.

In Activity, search by title, test ID, or payload value, then select a row to inspect it. The detail view shows the state observed at the callback, identifiers, and an expandable payload tree. **Copy event JSON** copies the complete captured record. The power button in Activity starts or stops capture; its status updates when the session expires.

Expo on iOS uses a top-level `body` object for custom data. The display message belongs in `aps.alert.body`:

```json
{
  "aps": {
    "alert": {
      "title": "Your order is ready",
      "body": "Tap to open order 123."
    },
    "sound": "default"
  },
  "body": {
    "orderId": "123",
    "url": "myapp://orders/123",
    "__buoyTestId": "order-test-123"
  }
}
```

This is an APNs payload for simulator injection. Expo Push Service uses a different request schema. Raw mode preserves the supplied payload, including its existing test ID or lack of one.

## Test each app state

| Flow | Preparation | Evidence to inspect |
| --- | --- | --- |
| Foreground | Keep the target app open and send | Receipt callback, optional presentation decision, actual app behavior |
| Background alert | Arm persistent capture while connected, then go to the simulator Home Screen and send | OS alert, actual tap, response callback and expected app screen |
| Launch from notification | Arm persistent capture, stop that app, send, then tap the OS notification | New process, recovered history, response and expected app screen |
| Silent background | Register the app's background task and background mode, then send a silent payload | Actual task start/completion records, if the OS schedules it |

For a cold launch, do not open the app between sending and tapping the notification. The Send panel shows a command targeting the selected app for stopping it. Pressing Home and terminating the process exercise different states.

A recovered SDK response is labeled **Response recovered at startup**. SDKs can cache an older response, so that label alone does not prove the notification caused the launch. The recorder deduplicates a cached response against its live callback and retained history.

The recorded app state is the state when the observer ran. A response delivered after resuming can say `active` even though the original alert arrived in the background.

## Observe the app's presentation policy

Capture does not install a presentation policy. Wrap the handler your app already passes to Expo if you want its returned behavior and errors in the event list:

```ts
import * as Notifications from 'expo-notifications';
import { instrumentExpoNotificationHandler } from '@buoy-gg/notifications';
import { notificationRecorder } from './notificationCapture';

const handler = {
  handleNotification: async () => ({
    shouldShowBanner: true,
    shouldShowList: true,
    shouldPlaySound: false,
    shouldSetBadge: false,
  }),
};

Notifications.setNotificationHandler(
  notificationRecorder
    ? instrumentExpoNotificationHandler(notificationRecorder, handler)
    : handler
);
```

The wrapper preserves the handler's result, errors and success/error callbacks. A recorded decision is the app's requested behavior. It does not prove that the OS displayed a banner.

## Observe an existing background task

Use `expo-task-manager`, define the task at module scope, and enable background remote notifications in the Expo plugin:

```json
{
  "expo": {
    "plugins": [
      ["expo-notifications", { "enableBackgroundRemoteNotifications": true }]
    ]
  }
}
```

Wrap the task your app already defines. Keep its registration and business logic in your app:

```ts
import * as TaskManager from 'expo-task-manager';
import * as Notifications from 'expo-notifications';
import { instrumentNotificationBackgroundTask } from '@buoy-gg/notifications';
import { notificationRecorder } from './notificationCapture';

const handlePush = async ({ data, error }: TaskManager.TaskManagerTaskBody) => {
  if (error) throw error;
  await updateAppData(data);
};

TaskManager.defineTask(
  'MY_PUSH_TASK',
  notificationRecorder
    ? instrumentNotificationBackgroundTask(notificationRecorder, handlePush)
    : handlePush
);
await Notifications.registerTaskAsync('MY_PUSH_TASK');
```

Task capture waits briefly for local persistence and never waits for Desktop. Silent delivery is subject to OS scheduling and app configuration. A stopped or force-quit app may not run background code. Inspect an actual task result before calling the background test successful.

## Tokens, permissions and history

**Device tokens** in notification settings distinguishes APNs, FCM and Expo tokens. Registration is explicit; opening the panel does not register a token or contact Expo Push Service. An Expo token requires the configured project ID. The app can also call `notificationRecorder.observeToken(type, value)` when it already obtains a token.

**Notification settings** reads permission settings and the current system tray through Expo. It does not request permission or dismiss notifications. A valid token does not guarantee permission to display an alert.

The app journal keeps up to 200 records within a 512 KiB budget. Each record is capped at 16 KiB. Truncation, evictions and persistence failures are visible. Clearing captured events leaves OS notifications, tokens and the capture session intact. Desktop send history keeps the latest 100 attempts for the current desktop process; saved payloads persist locally across desktop restarts.

## Support and limits

The tested native path is Expo Notifications 56 on iOS, including a release fixture, simulator injection, response recovery and APNs-token display. The public Expo APIs also expose Android callbacks and FCM tokens, but Android delivery requires its own device validation. The local-scheduling sync action is separate from remote push delivery.

This version has no direct APNs, FCM or Expo Push Service sender, Firebase/Notifee capture adapter, notification-service-extension test runner, or physical-device injection. Use the project's provider or backend for those delivery paths. Provider acceptance, app receipt, OS presentation and correct routing require separate evidence.

Capture is disabled by default in release builds. Internal release tests can explicitly set `enableInRelease: true` in capture setup. Desktop sync retains its separate `externalSync.enableInRelease` and Pro requirements. Keep these options confined to the builds where notification inspection is intended.

See [Expo notification setup](https://docs.expo.dev/push-notifications/push-notifications-setup/) and [Expo Notifications](https://docs.expo.dev/versions/v56.0.0/sdk/notifications/) for native configuration and platform limits.
