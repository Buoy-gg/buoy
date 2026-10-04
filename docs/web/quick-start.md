---
title: Quick Start
seoTitle: "React Web DevTools Setup — install Buoy in a React web app"
id: web-quick-start
description: "Get Buoy running in a React web app: install the web entries, register your tools, and open the floating menu in the browser."
---

Add Buoy to a React web app and open the floating menu. The examples use Vite; any bundler that reads the `browser` export condition works. [Frameworks](./frameworks) has the setup for Next.js, React Router and TanStack Start.

## 1. Install

<!-- ::PM npm="npm install @buoy-gg/core @buoy-gg/network @buoy-gg/storage react-native-web" yarn="yarn add @buoy-gg/core @buoy-gg/network @buoy-gg/storage react-native-web" pnpm="pnpm add @buoy-gg/core @buoy-gg/network @buoy-gg/storage react-native-web" bun="bun add @buoy-gg/core @buoy-gg/network @buoy-gg/storage react-native-web" -->

For plain Markdown readers, the command is:

```bash
npm install @buoy-gg/core @buoy-gg/network @buoy-gg/storage react-native-web
```

Add other tools the same way. Your app doesn't need React Native or Expo.

## 2. Sign in

From the app directory:

```bash
npx --package=@buoy-gg/core buoy login
```

In a Vite app, the CLI writes a dev token as `VITE_BUOY_KEY` in `.env.development.local`. Restart the dev server afterward. The token works in dev builds for 30 days. Keys still work too: see [Sign in with Buoy](../sign-in#keys-still-work).

## 3. Register the early hook

Put this import at the top of your entry file, before React DOM loads:

```ts
// main.tsx
import '@buoy-gg/core/web/register';
import ReactDOM from 'react-dom/client';
```

It lets the render, layout and element tools see React, and it starts recording `fetch` and XHR calls so Network can show the requests your page makes while it loads. Other tools work without it.

## 4. Mount the host

```tsx
import { FloatingDevTools } from '@buoy-gg/core/web';
import * as network from '@buoy-gg/network/web';
import * as storage from '@buoy-gg/storage/web';

// Keep this outside the component so it stays stable.
const modules = { network, storage };

export function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools modules={modules} licenseKey={import.meta.env.VITE_BUOY_KEY} />
    </>
  );
}
```

Import from the `/web` entries and pass each namespace in `modules`, keyed by package name (`'react-query'`, `'route-events'`, `'time-machine'`). Mount the host inside the providers your tools inspect, such as `QueryClientProvider` or the Redux `Provider`.

Reload the page. The floating menu appears, and Network lists the page's requests, including the ones made before Buoy loaded.

## 5. Connect Buoy Desktop (optional)

```bash
npm install @buoy-gg/external-sync
```

```tsx
import * as externalSync from '@buoy-gg/external-sync/web';

const modules = { network, storage, 'external-sync': externalSync };
```

Open [Buoy Desktop](../desktop) and the tab appears in the device switcher.

More detail, including the setup for each tool, is in [Installation](./installation).
