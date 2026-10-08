---
title: Quick Start
seoTitle: "React Web DevTools Setup — install Buoy in a React web app"
id: web-quick-start
description: "Get Buoy running in a React web app: install the web entries, register your tools, and open the floating menu in the browser."
---

Add Buoy to a React web app and open the floating menu. The examples use Vite; any bundler that reads the `browser` export condition works. [Frameworks](./frameworks) has the setup for Next.js, React Router and TanStack Start.

## Let your agent do it

Your coding agent can set up Buoy for you.
Use Claude Code, Cursor or Codex. Copy the prompt and paste it into your agent. Then review its changes.

<!-- ::agent-install platform="web" where="docs-web-quick-start" -->

To install by hand, follow the steps below.

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

## 3. Add the Vite plugin

```ts
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { buoy } from '@buoy-gg/core/vite';

export default defineConfig({
  plugins: [react(), buoy()],
});
```

The plugin finds the Buoy tools you installed and loads them.
It also adds Buoy's early hook to `index.html`.
The hook lets render tools see React.
It also lets Network show calls from page load.
Not using Vite? See [Installation](./installation#without-the-vite-plugin).

## 4. Mount Buoy

```tsx
import { BuoyDevTools } from '@buoy-gg/core/web/auto';

export function App() {
  return (
    <>
      {/* your app */}
      <BuoyDevTools licenseKey={import.meta.env.VITE_BUOY_KEY} />
    </>
  );
}
```

`BuoyDevTools` shows Buoy in dev builds.
Release builds don't load Buoy's tools or menu.
They still load the early hook.
It stays off unless Buoy ran in that browser in the last week.
Mount it inside the providers your tools read.
Examples are `QueryClientProvider` and the Redux `Provider`.

Reload the page. The floating menu appears, and Network lists the page's requests, including the ones made before Buoy loaded.

## 5. Connect Buoy Desktop (optional)

```bash
npm install @buoy-gg/external-sync
```

The plugin adds it like any other tool.
Open [Buoy Desktop](../desktop) and the tab appears in the device switcher.

More detail, including the setup for each tool, is in [Installation](./installation).
