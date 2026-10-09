---
title: Frameworks
seoTitle: "Buoy in Next.js, Vite, React Router, TanStack Start and Capacitor"
id: web-frameworks
description: "Where the early hook and the Buoy host go in Next.js (App and Pages Router), Vite, React Router framework mode, TanStack Start and Capacitor."
---

Buoy needs two things in any React web app:

1. The early hook, `import '@buoy-gg/core/web/register'`, before React DOM loads. It lets Highlight Updates and the other render tools see React, and it starts recording `fetch` and XHR calls so Network shows the requests the page made before Buoy loaded.
2. The host, `FloatingDevTools`, inside your providers, loaded only in the browser and only for the people who should see it.

The examples load the host in development only. To show Buoy to admins or QA in a production build, see [Production builds](#production-builds).

Where each one goes depends on the framework. Each setup below comes from a test app that Buoy's framework test suite installs the way you would, then checks tool by tool. The examples mount a `DevTools` component like the one in [Installation](./installation#mounting).

## Angular (Beta)

Use the [Angular guide](./angular) and its [AI install prompt](./angular#start-here).
Run `ng add @buoy-gg/angular`, then `npx buoy login`.
Start your app with `ng serve`.
The guide covers NgRx, NGXS, QA, and custom builders.

## Vite + React

The `buoy()` plugin does both for you:

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { buoy } from '@buoy-gg/core/vite';

export default defineConfig({
  plugins: [react(), buoy()],
});
```

```tsx
import { BuoyDevTools } from '@buoy-gg/core/web/auto';

export function App() {
  return (
    <Providers>
      {/* your app */}
      <BuoyDevTools licenseKey={import.meta.env.VITE_BUOY_KEY} />
    </Providers>
  );
}
```

The plugin adds the hook to `index.html` and loads every installed tool.
`BuoyDevTools` loads Buoy only in dev builds.
Its files still sit in a release build, but no page loads them.
The early hook still loads, and it stays off for most visitors.
To keep the host and tools out of the build, set it up by hand.

Put the hook on the first line of `src/main.tsx`:

```tsx
import '@buoy-gg/core/web/register';
import { createRoot } from 'react-dom/client';
import { App } from './App';

createRoot(document.getElementById('root')!).render(<App />);
```

Load the host lazily behind `import.meta.env.DEV`, so it stays out of the production bundle:

```tsx
import { lazy, Suspense } from 'react';

const DevTools = import.meta.env.DEV ? lazy(() => import('./DevTools')) : () => null;

export function App() {
  return (
    <Providers>
      {/* your app */}
      <Suspense fallback={null}>
        <DevTools licenseKey={import.meta.env.VITE_BUOY_KEY} />
      </Suspense>
    </Providers>
  );
}
```

## Next.js App Router

Next runs `instrumentation-client.ts` in the project root before the app hydrates. Put the hook there:

```ts
// instrumentation-client.ts
import '@buoy-gg/core/web/register';
```

Load the host from a client component with `next/dynamic` and `ssr: false`, and render it in the root layout inside your providers:

```tsx
// app/BuoyDevTools.tsx
'use client';

import dynamic from 'next/dynamic';

const DevTools =
  process.env.NODE_ENV === 'development'
    ? dynamic(() => import('./DevTools'), { ssr: false })
    : () => null;

export function BuoyDevTools() {
  return <DevTools licenseKey={process.env.NEXT_PUBLIC_BUOY_KEY} />;
}
```

This works with Turbopack and with webpack (`next dev --webpack`).

## Next.js Pages Router

Put the hook in `instrumentation-client.ts`, as for the App Router, and mount the host from `pages/_app.tsx` with the same `next/dynamic` pattern.

The Pages Router loads React DOM before `instrumentation-client.ts` runs. Network and the other tools work without any extra step. Highlight Updates also needs a small inline script in `pages/_document.tsx`, which runs before any of Next's bundles:

```tsx
// pages/_document.tsx
import { Head, Html, Main, NextScript } from 'next/document';
import { earlyHookScript } from '@buoy-gg/core/web/register';

export default function Document() {
  return (
    <Html lang="en">
      <Head>
        <script dangerouslySetInnerHTML={{ __html: earlyHookScript }} />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  );
}
```

The script keeps a reference to React DOM, and the hook in `instrumentation-client.ts` passes it to Buoy. When the hook doesn't run, the script only leaves an unused placeholder, so it can stay in production builds.

## React Router framework mode

Put the hook on the first line of `app/root.tsx`. `entry.client.tsx` is too late, because React Router loads the route modules, and React DOM with them, before it.

```tsx
// app/root.tsx
import '@buoy-gg/core/web/register';
import { lazy, Suspense, useEffect, useState } from 'react';

const DevTools = import.meta.env.DEV ? lazy(() => import('./DevTools')) : () => null;

// Buoy is browser-only, so it renders after hydration.
function ClientDevTools() {
  const [mounted, setMounted] = useState(false);
  useEffect(() => setMounted(true), []);
  if (!mounted) return null;
  return (
    <Suspense fallback={null}>
      <DevTools licenseKey={import.meta.env.VITE_BUOY_KEY} />
    </Suspense>
  );
}
```

Render `<ClientDevTools />` in the root route's component, inside your providers.

## TanStack Start

Start's default client entry is generated for you. Add `src/client.tsx` with the same code plus the hook on the first line:

```tsx
// src/client.tsx
import '@buoy-gg/core/web/register';
import { StartClient } from '@tanstack/react-start/client';
import { StrictMode } from 'react';
import { hydrateRoot } from 'react-dom/client';

hydrateRoot(
  document,
  <StrictMode>
    <StartClient />
  </StrictMode>,
);
```

Mount the host from the root route's `component` with the same `ClientDevTools` wrapper as React Router, inside your providers.

## Capacitor and Ionic

Beta. Start with the [phone app guide](../capacitor).
It covers install, sign-in, tools, and Desktop links.
See its test notes and limits before you ship.

A Capacitor app runs your web app inside a phone app. Set up Buoy the same way as [Vite + React](#vite--react), with two changes.

**Use the app's debug flag.** `cap run` ships a production `vite build`, even to a debug run. So `import.meta.env.DEV` is false there, and Buoy would never load. `BuoyDevTools` checks `Capacitor.DEBUG` instead. The phone app sets it, and it is true in debug builds. On iOS, `CAPACITOR_DEBUG` in `Info.plist` can also turn it on. If you load Buoy by hand, check the flag yourself:

```tsx
import { Capacitor } from '@capacitor/core';

const showBuoy = Capacitor.isNativePlatform() ? Capacitor.DEBUG : import.meta.env.DEV;
const DevTools = showBuoy ? lazy(() => import('./DevTools')) : () => null;
```

**Mount it outside the router.** In Ionic, put `BuoyDevTools` inside `IonApp` but outside `IonRouterOutlet`. Ionic's page changes move the page, and that would move Buoy with it.

Buoy reads the rest from the phone app on its own:

- It uses the same debug flag, so dev tokens and Desktop work in debug builds.
- In Desktop and MCP, the app shows as `ios` or `android`, not as a browser. It keeps the same device ID after a restart.
- On Android, the back button closes the open Buoy tool first. In Ionic, Ionic's own popups still close before Buoy's tools. Without Ionic, this needs `@capacitor/app`. If your app has its own `backButton` listener, skip your action while `isBuoyHoldingBackButton()` from `@buoy-gg/core/web` returns true.

### Connect to Desktop

- **iOS Simulator:** works as is.
- **Android emulator:** works once you add the settings below. Buoy reaches your Mac at `10.0.2.2`.
- **Android phone over USB:** run `adb reverse tcp:42831 tcp:42831`.
- **Live reload (`server.url`):** Buoy uses your dev server's host.
- **Anything else,** such as an iPhone without live reload: pass `externalSync={{ socketURL: 'http://192.168.1.20:42831' }}` with your Mac's address.
- **Android with a custom `server.hostname`:** pass `socketURL` too. Buoy can't tell that page from live reload.

Android blocks plain `http` and `ws` from the app by default. Allow them in debug builds only:

```ts
// capacitor.config.ts
const config: CapacitorConfig = {
  // ...
  server: { cleartext: true },
  android: { allowMixedContent: true },
};
```

Run `npx cap sync` after you change it. If Android still blocks the connection, Buoy logs a warning that names these settings.

### Tools that use Capacitor plugins

In debug builds, Buoy sees each call your app makes to a plugin. You add no code for this. The early hook (`@buoy-gg/core/web/register`) starts it first. So Buoy also sees the listeners your app adds when it starts.

With the `buoy()` plugin, install each tool and it shows up.
Without the plugin, add each tool to `modules`, like the rest:

```ts
import * as clock from '@buoy-gg/clock/web';
import * as lifecycle from '@buoy-gg/lifecycle/web';
import * as location from '@buoy-gg/location/web';
import * as permissions from '@buoy-gg/permissions/web';
import * as notifications from '@buoy-gg/notifications/web';

export const modules = {
  // ...your other tools
  clock,
  lifecycle,
  location,
  permissions,
  'push-notifications': notifications,
};
```

Here is what each one does:

- **Storage** shows your `@capacitor/preferences` keys. It shows `localStorage` and `sessionStorage` too. You can edit keys and undo.
- **Clock** works just like it does in React Native.
- **Lifecycle** sends `appStateChange`, `pause`, `resume` and `appUrlOpen` to your `@capacitor/app` listeners. On Android it sends `backButton` too. It also hides the page, so `visibilitychange` fires. It sets the battery that `@capacitor/device` reports. Its dark mode reaches `matchMedia('(prefers-color-scheme: dark)')` listeners, like React Native's `js` mode. CSS keeps the real theme. Capacitor has no low memory event, so that one is missing.
- **Location** answers `@capacitor/geolocation` and `navigator.geolocation`.
- **Permissions** answers `checkPermissions` and `requestPermissions` from any plugin. It knows these names: `location`, `coarseLocation`, `camera`, `photos`, `receive`, `display`, `notifications`, `microphone`, `contacts`, `calendar`, `reminders`, `bluetooth`, `motion` and `tracking`. Other names go to the phone as usual. When your app asks for one Buoy has set, Buoy shows its own test prompt. The real one stays hidden. With `enforce` on, approximate location gives a rough fix, about 2 km off, like iOS does.
- **Events** lists plugin calls under Capacitor. It hides values with names like `token` or `password`.
- **Impersonate** clears your `@capacitor/preferences` keys when it switches users. Buoy's own keys stay.
- **Images** finds images inside web components too, like Ionic's `ion-img`.
- **Assets** lists the files your page loads. To list the rest too, add `buoyAssets()` from `@buoy-gg/assets/vite`. With `buoy()` too, Buoy loads its list for you. See [Assets that haven't loaded](./installation#assets-that-havent-loaded).
- **Push notifications** saves what reaches your `@capacitor/push-notifications` and `@capacitor/local-notifications` listeners. Buoy adds no push listener of its own. So it can't take the tap that opened your app.

Buoy only sees calls that go to the phone's own code. A plugin that runs only in JavaScript won't show up.

Add `@capacitor/app` and `@capacitor/device` if you can. Buoy reads your app's bundle id and the simulator from them. MCP needs those to send real events to a simulator, and Desktop needs them to send a test push.

### Sign in and hosted Ask Buoy

Hosted Ask Buoy needs a real Buoy sign-in, not a key. Pass `signIn` to `BuoyDevTools`. In a Capacitor app, Buoy then shows a QR code and a short code. Open buoy.gg/activate on any device and allow it. Buoy uses your bundle id. To pick a different id, pass `signIn={{ appId: 'com.example.app' }}`.

```tsx
<BuoyDevTools signIn askBuoy="hosted" />
```

Install `@buoy-gg/ask-buoy` first.

Your app id shows up as `app:` plus the id in your Sites list at buoy.gg/dashboard/sites.

### Keep component names

Vite's build makes names short. Then tools like Highlight show a letter, not `<HomePage>`. This setting keeps the names:

```ts
// vite.config.ts
export default defineConfig({
  plugins: [react()],
  esbuild: { keepNames: true },
});
```

### File names in JS Top

JS Top shows the file and line behind each timer when your build has source maps. Turn them on for debug builds:

```ts
// vite.config.ts
export default defineConfig({
  build: { sourcemap: true },
});
```

### What's different

- Release builds follow the [Production builds](#production-builds) rules. A plain `http` address on your network also needs `allowInsecureNetwork`.
- We have tested Capacitor 7 on the iOS Simulator and the Android emulator.

## Three.js

Three.js support is in Beta. Start with the [three.js guide](./three).
It has full setup for R3F and plain apps.

Use the same web tools with Three.js. Buoy draws on the page, above the canvas.

### React Three Fiber

Place `<FloatingDevTools>` next to `<Canvas>`. Do not put it inside `<Canvas>`. Keep Buoy in your app's Query and Redux providers. Pass your Zustand stores and Jotai atoms as usual. Buoy cannot read R3F's private store.

Add the [Scene tool](../tools/three) to see the tree. It can pick, hide, move, turn, and tint nodes. Its guide shows how to link your scene. Add Perf Monitor for counts. Add Assets and Events for file loads.

### Plain Three.js

Add `react`, `react-dom`, and `react-native-web` as dev deps. The web tools need all three to run.

Load the boot file before your app:

```ts
// boot.ts
import '@buoy-gg/core/web/register';
void import('./app');
```

Then mount Buoy in your app:

```ts
// app.ts
import { mountBuoy } from '@buoy-gg/core/web';
import * as network from '@buoy-gg/network/web';
import * as storage from '@buoy-gg/storage/web';

const unmount = mountBuoy({
  modules: { network, storage },
  licenseKey: import.meta.env.VITE_BUOY_KEY,
});

// Call when the app shuts down.
// unmount();
```

`mountBuoy` takes the same props as `<FloatingDevTools>`. Call its return value to remove Buoy. It uses its own React root. It cannot read providers from another root. Query, Redux, and Highlight Updates have no app to watch here. Pass a vanilla store for Zustand to watch.

The boot import must run before Three.js loads. The hook keeps a short list of past events. Weak refs let the app free old scenes. It keeps a hook that was already there. A late boot import cannot catch past Three.js events. The hook uses the same dev and release rules.

### Mouse, keys, and full screen

Put the canvas in a wrapper for full screen:

```ts
await document.querySelector('#scene-wrap')?.requestFullscreen();
```

Buoy moves into that wrapper. It moves back on exit. A bare canvas in full screen cannot show Buoy. Its child DOM is fallback content.

Press Esc to free a mouse held by pointer lock. Then click Buoy. Text fields stop keys sent to game handlers. When focus enters Buoy, it sends key-up events. Games must accept these events to clear held keys. Wheel events in Buoy stay in Buoy. Drag and Esc still work in its panels. These guards cannot stop page handlers in the capture phase.

Buoy's DOM tools cannot read objects drawn in WebGL. DOM overlays do not show in a WebXR headset. A scene in a worker is outside the hook's reach.

## Production builds

Buoy also works in production builds, for example for admins, developers or QA testers using the live site. Two things change:

- **Access.** Outside development, `FloatingDevTools` needs Pro or Business. Use a Pro key, or [Sign in with Buoy](../sign-in#your-live-site) with no key in the build.
- **Who sees it.** Load the host for the users who should have it, instead of behind a development check. Buoy doesn't know your roles, so the check is yours:

  ```tsx
  const DevTools = lazy(() => import('./DevTools'));

  function MaybeDevTools() {
    const user = useCurrentUser();
    if (!user?.isAdmin) return null;
    return (
      <Suspense fallback={null}>
        <DevTools licenseKey={import.meta.env.VITE_BUOY_KEY} />
      </Suspense>
    );
  }
  ```

Keep the early hook import where it is. In a production build it does nothing for most visitors: it runs only in a browser where `FloatingDevTools` has rendered in the past week. So the first page an admin opens after signing in lists requests from when Buoy loaded, and later page loads include the requests made while the page loads. Highlight Updates works the same way. When someone signs out on a shared browser, call `clearReleaseOptIn()` from `@buoy-gg/core/web` to stop it there.

The Desktop connection has its own production switch: pass `externalSync={{ enableInRelease: true }}` to connect from a production build. It connects to Buoy Desktop on the admin's own machine, and Desktop asks once whether to allow your site. See [Desktop and MCP](./installation#desktop-and-mcp) for the other production rules.

## Account key variable

Pass the dev token or key through a variable your framework exposes to the browser: `VITE_BUOY_KEY` in Vite, React Router and TanStack Start, and `NEXT_PUBLIC_BUOY_KEY` in Next.js. `buoy login` picks the right one from your `package.json`.

## Other frameworks

Buoy's web build is React, so it runs in any React app whose bundler reads the `browser` export condition. In a framework not listed here, put the hook wherever code runs before React DOM loads. If nothing runs that early, put it as early as you can: Network then shows requests from that point on, and Highlight Updates won't see renders. The other tools don't depend on the hook.
