---
title: Storage Explorer
seoTitle: "Storage Explorer — setup, config & API"
id: tools-storage
description: "Browse and edit every key-value pair your React Native app persists — AsyncStorage, MMKV, and SecureStore in one explorer with real-time updates."
---

<!-- ::platform-badge platform="both" -->

Browse, edit, and manage all your app's persisted data. See every key-value pair across all storage backends in real-time.

<!-- ::storage-demo -->

## Supported Backends

<!-- ::storage-backends-grid -->

> **Multi-instance MMKV support** — If you use multiple MMKV instances, they're all detected automatically. Switch between instances and see key counts per instance.

---

## Installation

<!-- ::PM npm="npm install @buoy-gg/storage" yarn="yarn add @buoy-gg/storage" pnpm="pnpm add @buoy-gg/storage" bun="bun add @buoy-gg/storage" -->

That's it. The Storage Explorer auto-detects installed backends and appears in your FloatingDevTools menu.

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

> MMKV instances need the same one-time registration — see the [package README](https://github.com/LovesWorking/react-native-buoy/tree/main/packages/storage#mmkv-setup) for details.

Registered values are re-read every few seconds while the browser is open, so a secure write shows up without reopening the tool. AsyncStorage and MMKV writes are picked up the moment they happen; the keychain gets a poll instead because it has no change notification of any kind.

---

## What You Can Do

<!-- ::storage-actions-grid -->

---

## Smart Features

**Edit values in place** — Expand a key and hit *Edit value* to write a new one straight to the device. Types are preserved: an MMKV number stays a number, a boolean only accepts `true`/`false`, and a key holding JSON has to stay valid JSON — so you can't silently turn an object into a quoted string. Buffers, read-only MMKV instances, and biometric-protected SecureStore keys say why they can't be edited instead of offering a broken field.

**Edit arrays and objects without typing JSON** — Tap any node in the value tree and its actions appear alongside the key's other buttons: arrays get append, duplicate, reorder and remove; objects get add-key, duplicate and remove; scalars get a text field, with booleans as a two-way toggle. Editing a fifty-item array by hand-writing the whole blob is exactly the thing this avoids — the raw text editor is still there when you'd rather paste.

**Inline value previews** — Short values show right on the card (`number · 42`, `string · "en"`), and booleans get a color-coded true/false badge. No need to expand to see simple values.

**Pin to top** — Pin the keys you're watching so they stay at the top of the list. Pins persist across sessions.

**Hide from list** — One tap filters noisy keys out of the browser (from the expanded card or bulk selection). Non-destructive — unhide any time from the filters panel.

**JSON formatting** — Values that are valid JSON are automatically pretty-printed for readability.

**Live events** — Watch storage changes happen in real-time as your app reads and writes data.

**Bulk selection** — Select multiple keys to delete or export them all at once.

---

## What's Next

- [Network Monitor](./network) — See every API call your app makes
- [Environment Inspector](./env) — Validate env vars with type checking
- [React Query](./react-query) — Inspect query cache and simulate states

---

*Looking for an overview with screenshots and FAQs? See the [Storage Explorer page on buoy.gg](https://buoy.gg/tools/storage).*
