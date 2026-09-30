---
title: Frameworks
seoTitle: "Buoy in Next.js, Vite, React Router and TanStack Start"
id: web-frameworks
description: "Where the early hook and the Buoy host go in Next.js (App and Pages Router), Vite, React Router framework mode and TanStack Start."
---

Buoy needs two things in any React web app:

1. The early hook, `import '@buoy-gg/core/web/register'`, before React DOM loads. It lets Highlight Updates and the other render tools see React, and it starts recording `fetch` and XHR calls so Network shows the requests the page made before Buoy loaded.
2. The host, `FloatingDevTools`, inside your providers, loaded only in the browser and only for the people who should see it.

The examples load the host in development only. To show Buoy to admins or QA in a production build, see [Production builds](#production-builds).

Where each one goes depends on the framework. Each setup below comes from a test app that Buoy's framework test suite installs the way you would, then checks tool by tool. The examples mount a `DevTools` component like the one in [Installation](./installation#mounting).

## Vite + React

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

## Production builds

Buoy also works in production builds, for example for admins, developers or QA testers using the live site. Two things change:

- **The key.** Outside development, `FloatingDevTools` renders only with a Pro key.
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

Pass the key through a variable your framework exposes to the browser: `VITE_BUOY_KEY` in Vite, React Router and TanStack Start, and `NEXT_PUBLIC_BUOY_KEY` in Next.js. `buoy login` picks the right one from your `package.json`.

## Other frameworks

Buoy's web build is React, so it runs in any React app whose bundler reads the `browser` export condition. In a framework not listed here, put the hook wherever code runs before React DOM loads. If nothing runs that early, put it as early as you can: Network then shows requests from that point on, and Highlight Updates won't see renders. The other tools don't depend on the hook.
