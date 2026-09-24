# @buoy-gg/storage

[![npm version](https://img.shields.io/npm/v/@buoy-gg/storage?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/storage) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/storage?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/storage)

Browse and edit supported AsyncStorage, registered MMKV instances, and registered SecureStore keys inside your app.

Part of [Buoy](https://github.com/Buoy-gg/buoy). Install the package, complete account setup, and follow the integration steps below.

![Storage Explorer demo](https://github.com/user-attachments/assets/80ef1c60-d20c-4d8b-97e6-f37b21b315ea)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/storage
```

## Before you start

Use a development build with `@buoy-gg/core` and a Free or Pro Buoy account key. From your app’s directory, sign in:

```bash
npx --package=@buoy-gg/core buoy login
```

For Expo, initialize Buoy before rendering the menu:

```tsx
import { Buoy } from "@buoy-gg/core";

Buoy.init({ licenseKey: process.env.EXPO_PUBLIC_BUOY_KEY });
```

The login command writes the Expo key to `.env.local`. For React Native CLI, pass the key from your app’s environment configuration; React Native does not load `.env.local` automatically. Mount `FloatingDevTools` inside the same providers as your screens and restart the development server after installation. The [Quick Start](https://buoy.gg/buoy/latest/docs/quick-start) shows the complete root component setup.

## Quick start

AsyncStorage works out of the box. Render the floating menu once — the Storage Explorer is auto-discovered as soon as this package is installed:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";

export default function App() {
  return (
    <>
      {/* your app */}
      <FloatingDevTools />
    </>
  );
}
```

**MMKV** — register each instance once so the explorer can see it (required for MMKV v4; Metro can't auto-detect instances):

```ts
import { createMMKV } from "react-native-mmkv";
import { registerMMKVInstance } from "@buoy-gg/storage";

export const storage = createMMKV({ id: "mmkv.default" });
registerMMKVInstance("mmkv.default", storage);
```

**SecureStore** — the iOS Keychain / Android KeyStore has no key-listing API, so register the keys you want visible:

```ts
import * as SecureStore from "expo-secure-store";
import { registerSecureStoreKeys } from "@buoy-gg/storage";

registerSecureStoreKeys(SecureStore, [
  "auth.accessToken",
  { key: "session", keychainService: "com.myapp.auth" },
  // Biometric-protected keys are listed but never auto-read (no surprise Face ID prompts)
  { key: "pin", requireAuthentication: true },
]);
```

## Check the integration

Use a disposable test key: read its value, edit it, read it from your app, then delete it. Register the MMKV instances your app actually uses and the SecureStore keys you want to inspect; those keys cannot be enumerated automatically.

## What you get

- **Three backends, one explorer** — AsyncStorage, MMKV (multiple instances, including encrypted), and Expo SecureStore, with per-instance key counts and an instance switcher.
- **Edit in place** — modify, delete, and add keys directly; bulk-select to delete or export many at once.
- **Live events** — watch storage reads and writes stream in as your app runs.
- **Pin and hide** — pin the keys you're watching to the top (persists across sessions); one tap hides noisy keys non-destructively.
- **Inline previews** — short values show on the card (`number · 42`, `string · "en"`), booleans get color-coded true/false badges, and valid JSON is auto pretty-printed.
- **Biometric keys stay safe** — SecureStore keys behind Face ID / fingerprint are listed but never auto-read.
- **Required-key validation** — declare keys with expected types or values via `createStorageTool({ requiredStorageKeys })` and see what's missing or wrong.

## Desktop & AI

To connect a React Native app to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) or MCP, install `@buoy-gg/external-sync` and follow the [Desktop connection guide](https://buoy.gg/buoy/latest/docs/desktop). Sign in to Desktop separately. [MCP setup](https://buoy.gg/buoy/latest/docs/mcp) also requires a process account and Pro access. Available remote actions depend on the tool and app integration.

## Account and plan requirements

Use a Free or Pro Buoy account. History limits and paid features vary by tool; see [pricing](https://buoy.gg/pricing). Production access requires Pro where supported. Development-only hooks and actions remain unavailable in release builds.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/storage) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)

## Web support (unreleased)

The browser uses localStorage and sessionStorage with the shared editor, event history, undo, and snapshots. Native secure storage is unavailable on web. The browser build is available in this checkout and has not been published yet. See the [web setup guide](https://buoy.gg/buoy/latest/docs/web-preview) for registration, dependencies, and browser boundaries.
