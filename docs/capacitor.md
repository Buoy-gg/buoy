---
title: Capacitor and Ionic (Beta)
seoTitle: "Buoy for Capacitor and Ionic (Beta)"
id: capacitor
description: "Set up Buoy in your app. Use web tools, phone plugins, Desktop and MCP."
---

Beta. Use Buoy in your app on iOS and Android.
This guide is for React apps built with Capacitor.
Ionic React uses the same steps and web tools.
We have tested Capacitor 7. Other versions need more tests.

## Start here

Let your coding agent install Buoy in your app:

<!-- ::agent-install platform="capacitor" where="docs-capacitor" -->

To set it up by hand, follow the steps below.

## Install

Keep React and React DOM in your app.
Add `react-native-web` 0.21 and the tools you need.
Keep all Buoy packages on the same release.

```bash
npm install @buoy-gg/core react-native-web @buoy-gg/network @buoy-gg/storage @buoy-gg/external-sync
```

Each tool uses its `/web` entry in this guide.
You do not need React Native or Expo.
See [web setup](./web/installation#tool-setup) for state tools and routes.
Keep Buoy in the providers you use now.

## Load the hook first

Put this first in your entry file, before React DOM:

```ts
// src/main.tsx
import '@buoy-gg/core/web/register';
```

The hook sees calls and listeners from app boot.
It also helps track renders and early web calls.
Phone plugin capture runs in debug builds.
It sees calls that go to the phone's code.
Plugins made of JavaScript alone do not show there.

## Mount Buoy

Keep the module map outside your component.
Add tools by name. Use `/web` for each import.

```tsx
// DevTools.tsx
import { FloatingDevTools } from '@buoy-gg/core/web';
import * as network from '@buoy-gg/network/web';
import * as storage from '@buoy-gg/storage/web';
import * as externalSync from '@buoy-gg/external-sync/web';

const modules = { network, storage, 'external-sync': externalSync };

export default function DevTools() {
  return <FloatingDevTools modules={modules} signIn />;
}
```

Load this file only when your app should show Buoy.
Use the phone's debug flag for a native app:

```tsx
import { lazy, Suspense } from 'react';
import { Capacitor } from '@capacitor/core';

const showBuoy = Capacitor.isNativePlatform()
  ? (window as Window & { Capacitor?: { DEBUG?: boolean } }).Capacitor?.DEBUG === true
  : import.meta.env.DEV;
const DevTools = showBuoy ? lazy(() => import('./DevTools')) : () => null;

// Inside your app's existing providers:
<IonApp>
  <IonReactRouter>
    <IonRouterOutlet>{/* your routes */}</IonRouterOutlet>
  </IonReactRouter>
  <Suspense fallback={null}><DevTools /></Suspense>
</IonApp>
```

Set the viewport so Buoy can avoid the notch:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover" />
```

In Ionic, put Buoy in `IonApp`. Keep it out of `IonRouterOutlet`.
This keeps page moves from moving Buoy too.
Plain React apps can mount beside their router.

A phone debug run may use a Vite production bundle.
So `import.meta.env.DEV` alone can hide Buoy there.
Buoy reads `Capacitor.DEBUG` from the phone app too.
On iOS, `CAPACITOR_DEBUG` in `Info.plist` can turn it on.
Check that flag when you test a release build.

Android back closes the open Buoy tool first.
Ionic's own popups still close before Buoy tools.
Plain Capacitor needs `@capacitor/app` for the back hook.
If your app has its own back listener, check
`isBuoyHoldingBackButton()` from `@buoy-gg/core/web`.
Skip your back action while it returns true.

## Phone plugin tools

Install each tool you want, then add its module:

```bash
npm install @buoy-gg/clock @buoy-gg/lifecycle @buoy-gg/location @buoy-gg/permissions @buoy-gg/notifications
```

```ts
import * as clock from '@buoy-gg/clock/web';
import * as lifecycle from '@buoy-gg/lifecycle/web';
import * as location from '@buoy-gg/location/web';
import * as permissions from '@buoy-gg/permissions/web';
import * as notifications from '@buoy-gg/notifications/web';

// Add these to the module map above:
const modules = {
  network, storage, 'external-sync': externalSync,
  clock, lifecycle, location, permissions,
  'push-notifications': notifications,
};
```

Clock changes JavaScript dates and timers in your app.
It does not change the phone's clock.

Use Lifecycle to send App events to your app.
These include `appStateChange`, `pause`, `resume` and `appUrlOpen`.
Android also gets `backButton`. Relaunch reloads the page.
It sends `visibilitychange` and can set Device plugin battery data.

Location answers `@capacitor/geolocation` and `navigator.geolocation` calls.
Permissions can answer plugin checks and requests with test states.
Known names and test prompts are in the
[plugin tools guide](./web/frameworks#tools-that-use-capacitor-plugins).
Names Buoy does not know still reach the phone.

Push saves events your push and local push listeners get.
It works with `@capacitor/push-notifications` and `@capacitor/local-notifications`.
Buoy adds no push listener of its own.
It cannot take the tap that opened your app.

Add `@capacitor/app` and `@capacitor/device` to read app details.
They help Desktop and MCP find the right simulator.
Your app still needs the plugins its own code uses.

## Sign in with a code

With `signIn`, Buoy shows a QR code and short code.
Open [the code page](https://buoy.gg/activate). Allow the sign-in there.
You can do this on any device with a browser.
Buoy uses your bundle id to name the app.
Set `signIn={{ appId: 'com.example.app' }}` to choose one.
Find it as `app:` plus that id in your
[Sites list](https://buoy.gg/dashboard/sites).

For hosted Ask Buoy, add its package and module.
It needs Pro and a real Buoy sign-in.
A key alone does not grant hosted chat access.

```bash
npm install @buoy-gg/ask-buoy
```

```tsx
import * as askBuoy from '@buoy-gg/ask-buoy/web';
import { hostedAskBuoy } from '@buoy-gg/ask-buoy/web';

// Add 'ask-buoy': askBuoy to your module map.
<FloatingDevTools modules={modules} signIn askBuoy={{ ...hostedAskBuoy() }} />
```

You can also use a dev key for other tools.
Run `npx --package=@buoy-gg/core buoy login` in your app folder.
Pass `licenseKey={import.meta.env.VITE_BUOY_KEY}` to the host.
Plan limits still apply to each tool and action.

## Desktop and MCP

Keep the `external-sync` module from the mount example.
Open [Buoy Desktop](./desktop) and sign in there too.
Set up [MCP](./mcp) for your editor if you need it.
MCP needs its own account. Data and actions need Pro.
Run `list_devices` and pick your app before using tools.
It shows as `ios` or `android` with a saved device ID.

| Where your app runs | Desktop address |
| --- | --- |
| iOS Simulator | The default works: `http://localhost:42831`. |
| Android emulator | Buoy uses `http://10.0.2.2:42831`. Allow web calls below. |
| Android phone on USB | Run `adb reverse tcp:42831 tcp:42831`. Use localhost. |
| Live reload with `server.url` | Buoy uses the dev server's host. |
| Phone on Wi-Fi | Set `socketURL` to your Mac's LAN address. |
| Android with a custom `server.hostname` | Set `socketURL` yourself. |

For a phone on Wi-Fi, use your own Mac's address:

```tsx
<FloatingDevTools modules={modules} signIn
  externalSync={{ socketURL: 'http://192.168.1.20:42831' }} />
```

The phone must be able to reach that Mac.
Android can block plain HTTP and WebSocket calls.
Use these settings for debug builds only:

```ts
// capacitor.config.ts
const config: CapacitorConfig = {
  // ...your app settings
  server: { cleartext: true },
  android: { allowMixedContent: true },
};
```

Run `npx cap sync` after you change the settings.
iOS LAN calls may need `NSAllowsLocalNetworking` in `Info.plist`.
Keep that app network setting scoped to debug use too.

Release sync needs Pro and `enableInRelease: true`.
A plain HTTP LAN address also needs `allowInsecureNetwork: true`.
See [release rules](./desktop#release-builds) before you enable it.
The mount guard above shows Buoy in debug runs only.

## Names, source maps and assets

Keep names so render tools can show your component names.
Use source maps so JS Top can show files and lines.
Add the asset plugin to list files before they load.

```bash
npm install @buoy-gg/assets
```

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { buoyAssets } from '@buoy-gg/assets/vite';

export default defineConfig({
  plugins: [react(), buoyAssets()],
  esbuild: { keepNames: true },
  build: { sourcemap: true },
});
```

Use source maps in debug builds for this setup.
Load the asset list in your dev tools file:

```ts
import * as assets from '@buoy-gg/assets/web';

// Add assets to modules. Respect your app's base path.
await assets.loadBrowserAssetManifest('/buoy-assets.json');
```

Vite builds list emitted files and media from `public`.
In Vite dev, source files show up as they load.
See [asset setup](./web/installation#assets-that-havent-loaded) for more build options.

## What works in Beta

The menu can move, hide, and bring tools back.
Web tools use the same panels as the native tools.
Network sees page fetch and XHR calls.
Storage reads `Preferences`, `localStorage` and `sessionStorage`.
Events can list phone plugin calls with secret values hidden.
State tools, Routes, Time Machine and Highlight have web builds.
Assets and Ask Buoy are supported in Beta too.
See the [web tool list](./web/installation#tool-setup) for each setup.
Sentry's code was tested, but its package is held back.
It is not part of this npm release.

The same Detox suites ran on Chrome and Android.
Env and Minimize passed all checks. So did Network.
React Query, Redux and Zustand passed all checks too.
Floating and Sentry passed too.
Ask Buoy passed all checks on both targets.
Other suites have fails or blocked checks.
New runs use a fake Pro token from license tests.
Storage, Assets and Ask Buoy can now run Pro checks.
Account passed all three paid-cache checks with a real key.
Two old Account checks still fail on both targets.
Some tests also ask for native data that Vite lacks.
Events leaves Buoy's own clicks, web calls and logs out.
App plugin calls still show.
Ask Buoy saves a waiting card right away.
Highlight filters use their own scroll view on web.
The iPhone checks were by hand and MCP.
They did not use the Detox runner.

## What does not apply

- The phone bridge has no memory-warning event to send.
- Dark mode works for JavaScript `matchMedia` listeners only. CSS and the phone's theme keep their real mode.
- Buoy Camera does not work in these apps.
- Perf has no native CPU or memory numbers here. Web heap data depends on what the WebView can expose.
- Native secure stores and MMKV use their own storage. These are not Preferences.
- Metro's image counts and scale files do not match Vite.
- Native view names such as `RCTView` do not name web nodes.
- Web Bench has no RN worklet frames. FPS need not rise after a cold run.
- App popups can cover Buoy. Native modals can too.
- Buoy saves its settings in localStorage. Clearing it clears those settings too.

Real phones over LAN still need more checks.
Capacitor 6 and 8 also need more tests.

## Report a problem

Open an [issue](https://github.com/Buoy-gg/buoy/issues) with steps to try.
Name the tool and share what you saw.
Share the Buoy and Capacitor versions you used.
Name the phone and its OS too.
Say if you used a debug or release build.
For sync, say if you used USB or Wi-Fi.
Share a small test app if you can.
Remove keys, tokens, and private app data first.
