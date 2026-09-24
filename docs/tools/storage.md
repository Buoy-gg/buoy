---
title: Storage Explorer
seoTitle: "React Native AsyncStorage & MMKV Viewer — browse on-device"
id: tools-storage
description: "Browse and edit supported storage in your React Native app — AsyncStorage, MMKV, and SecureStore in one explorer with real-time updates."
---

<!-- ::platform-badge platform="both" -->

Inspect and edit AsyncStorage, registered MMKV instances, and registered SecureStore keys. Use the backend filter to choose the data you want to inspect.

<!-- ::storage-live-demo -->

## Supported Backends

<!-- ::storage-backends-grid -->

> **Multi-instance MMKV support** — If you use multiple MMKV instances, register each instance explicitly. Switch between instances and see key counts per instance.

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/storage" yarn="yarn add @buoy-gg/storage" pnpm="pnpm add @buoy-gg/storage" bun="bun add @buoy-gg/storage" -->

After setting up [Buoy core](../quick-start), install this package and restart Metro. AsyncStorage can be discovered automatically; register MMKV instances and SecureStore keys below.

### SecureStore setup

Expo SecureStore (iOS Keychain / Android KeyStore) has no key-listing API, so register the keys you want visible — pass the module in, no extra dependency needed:

```typescript
import * as SecureStore from "expo-secure-store";
import { registerSecureStoreKeys } from "@buoy-gg/storage";

registerSecureStoreKeys(SecureStore, [
  "auth.accessToken",
  { key: "session", keychainService: "com.myapp.auth" },
  // Biometric-protected keys are listed but never auto-read (no surprise Face ID prompts)
  { key: "pin", requireAuthentication: true },
]);
```

### MMKV setup

Register the same instance your app reads and writes. For MMKV v4:

```typescript
import { createMMKV } from "react-native-mmkv";
import { registerMMKVInstance } from "@buoy-gg/storage";

export const storage = createMMKV({ id: "mmkv.default" });
registerMMKVInstance("mmkv.default", storage);
```

If your app already creates this instance, add the registration beside that code instead of creating another instance. Give each registered instance a distinct name.

To verify setup, write a disposable test key through your app, find it in Storage, edit it, and read it back through your app. Delete the test key when finished.

Registered values are re-read every few seconds while the browser is open, so a secure write shows up without reopening the tool. AsyncStorage and MMKV writes are picked up the moment they happen; the keychain gets a poll instead because it has no change notification of any kind.

---

## What You Can Do

<!-- ::storage-actions-grid -->

---

## Smart Features

**Edit values in place** — Expand a key and hit *Edit value* to write a new one straight to the device. Types are preserved: an MMKV number stays a number, a boolean only accepts `true`/`false`, and a key holding JSON has to stay valid JSON — so you can't silently turn an object into a quoted string. Buffers, read-only MMKV instances, and biometric-protected SecureStore keys say why they can't be edited instead of offering a broken field.

**Edit arrays and objects without typing JSON** — Tap any node in the value tree and its actions appear alongside the key's other buttons: arrays get append, duplicate, reorder and remove; objects get add-key, duplicate and remove; scalars get a text field, with booleans as a two-way toggle. Use the raw text editor when you want to paste a complete value.

**Inline value previews** — Short values show right on the card (`number · 42`, `string · "en"`), and booleans get a color-coded true/false badge. No need to expand to see simple values.

**Pin to top** — Pin the keys you're watching so they stay at the top of the list. Pins persist across sessions.

**Hide from list** — One tap filters noisy keys out of the browser (from the expanded card or bulk selection). Non-destructive — unhide any time from the filters panel.

**JSON formatting** — Values that are valid JSON are automatically pretty-printed for readability.

**Live events** — Watch storage changes happen in real-time as your app reads and writes data.

**Bulk selection** — Select multiple keys to delete or export them all at once.

---

## What's Next

- [Network Monitor](./network) — Inspect supported HTTP requests
- [Environment Inspector](./env) — Validate env vars with type checking
- [React Query](./react-query) — Inspect query cache and simulate states

---

## FAQ

### How do I view AsyncStorage contents in Expo?

Install `@buoy-gg/storage` and open the Storage tool from the floating menu — all AsyncStorage keys are browsable and editable on the device, including in Expo Go.

### Does it support MMKV?

Yes. Register each MMKV instance, then select it with the backend filter. SecureStore requires explicit key registration too.

## Web support (unreleased)

The browser uses localStorage and sessionStorage with the shared editor, event history, undo, and snapshots. Native secure storage is unavailable on web. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
