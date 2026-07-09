# @buoy-gg/storage

[![npm version](https://img.shields.io/npm/v/@buoy-gg/storage?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/storage) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/storage?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/storage)

**Browse and edit every key-value pair your React Native app persists — AsyncStorage, MMKV, and SecureStore in one explorer with real-time updates.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

![Storage Explorer demo](https://github.com/user-attachments/assets/80ef1c60-d20c-4d8b-97e6-f37b21b315ea)

## Install

```bash
npm install @buoy-gg/core @buoy-gg/storage
```

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

## What you get

- **Three backends, one explorer** — AsyncStorage, MMKV (multiple instances, including encrypted), and Expo SecureStore, with per-instance key counts and an instance switcher.
- **Edit in place** — modify, delete, and add keys directly; bulk-select to delete or export many at once.
- **Live events** — watch storage reads and writes stream in as your app runs.
- **Pin and hide** — pin the keys you're watching to the top (persists across sessions); one tap hides noisy keys non-destructively.
- **Inline previews** — short values show on the card (`number · 42`, `string · "en"`), booleans get color-coded true/false badges, and valid JSON is auto pretty-printed.
- **Biometric keys stay safe** — SecureStore keys behind Face ID / fingerprint are listed but never auto-read.
- **Required-key validation** — declare keys with expected types or values via `createStorageTool({ requiredStorageKeys })` and see what's missing or wrong.

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/storage) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
