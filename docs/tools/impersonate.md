---
title: Impersonate Tool
seoTitle: "React Native User Impersonation — test as any user"
id: tools-impersonate
description: "Test your React Native app as any user by injecting impersonation headers into every network request — no logging out or switching accounts required."
---

<!-- ::platform-badge platform="both" -->

Test an authorized user's app experience by attaching an impersonation header to intercepted requests. Your backend must authenticate the operator, authorize impersonation, and interpret the header.

Configure user search, select a test user, verify the header on a request to your backend, then stop impersonation and confirm it is removed.

<!-- ::impersonate-live-demo -->

## Installation

<!-- ::PM npm="npm install @buoy-gg/impersonate" yarn="yarn add @buoy-gg/impersonate" pnpm="pnpm add @buoy-gg/impersonate" bun="bun add @buoy-gg/impersonate" -->

Unlike other Buoy tools, the Impersonate tool requires configuration because it needs to integrate with your user search API.

---

## Quick Start

The example uses your existing `api.searchUsers` client and `YourApp` component. Define those in your app, and keep the menu inside the providers needed for cache clearing.

```tsx
import { createImpersonateTool } from '@buoy-gg/impersonate';
import { FloatingDevTools } from '@buoy-gg/core';

const impersonateTool = createImpersonateTool({
  // Required: How to search for users
  onSearchUsers: async (query) => {
    const response = await api.searchUsers({ email: query });
    return response.users.map(user => ({
      id: user.id,
      displayName: user.name,
      email: user.email,
      avatarUrl: user.avatar,
      metadata: { role: user.role },
    }));
  },
});

function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools apps={[impersonateTool]} />
    </>
  );
}
```

---

## How It Works

When impersonation is active, the tool automatically injects a header into intercepted outgoing `fetch` and `XMLHttpRequest` calls:

```
x-impersonate-user-id: user_123
```

Your backend checks for this header and returns data for the specified user instead of the authenticated admin.

> **Security Note:** Your backend should validate that the authenticated user has admin/impersonation permissions before honoring this header.

---

## Features

### User Search
Search for users by email, name, or ID. Results display in a clean card format showing user details and metadata. When viewing an active impersonation, the card shows a power button to stop directly from the search results.

### Impersonation History
Quick-switch between recently impersonated users. History persists across app restarts (up to 10 users). Each history entry shows when the user was last impersonated (e.g., "5m ago", "2h ago").

### Data Clearing (Auto-Detected)
Automatically clear stale data when switching users:

| Option | Description | Auto-Detected | Default |
|--------|-------------|:-------------:|---------|
| React Query | Clear query cache and cancel pending queries | ✅ | On |
| Redux | Reset Redux store to initial state | ✅ | On |
| AsyncStorage | Clear app data (preserves `@buoy/*` keys) | ❌ | Off |
| MMKV | Clear MMKV storage (preserves `@buoy/*` keys) | ❌ | Off |

Place the menu inside your Query and Redux providers. Redux auto-clearing dispatches `@@RESET`; your reducer must handle it, or you must supply `onClearRedux`. Detecting a store does not guarantee that it can be reset.

### Floating Banner
A floating banner automatically appears when impersonation is active, showing which user is selected. The banner can be toggled on/off in Settings.

---

## Configuration

### Required: User Search

The `onSearchUsers` callback is required. It should return an array of `User` objects:

```tsx
interface User {
  id: string;              // Sent in the impersonation header
  displayName?: string;    // Shown in UI (falls back to email, then id)
  email?: string;          // User's email
  avatarUrl?: string;      // Avatar image URL
  metadata?: Record<string, unknown>;  // Extra info to display
}
```

### Data Clearing Callbacks

Within the corresponding providers, the tool can:
- Uses `useQueryClient()` to clear React Query cache
- Uses `useStore()` to dispatch a reset action to Redux

For AsyncStorage and MMKV, you can provide callbacks:

```tsx
const impersonateTool = createImpersonateTool({
  onSearchUsers: searchUsers,

  // Clear AsyncStorage (filter out keys you want to keep)
  onClearAsyncStorage: async () => {
    const keys = await AsyncStorage.getAllKeys();
    const appKeys = keys.filter(k => !k.startsWith('@buoy/'));
    await AsyncStorage.multiRemove(appKeys);
  },

  // Clear MMKV
  onClearMMKV: () => {
    const keys = storage.getAllKeys();
    keys.filter(k => !k.startsWith('@buoy/')).forEach(k => storage.delete(k));
  },
});
```

> **Override auto-detection** — If you provide a callback for React Query or Redux, your callback is used instead of auto-detection. This is useful if you need custom reset logic.

---

## Developer Defaults

Set default values for your team. These are used when there are no persisted user settings:

```tsx
const impersonateTool = createImpersonateTool({
  onSearchUsers: searchUsers,

  defaults: {
    headerKey: 'x-admin-impersonate',  // Custom header name
    showBanner: true,                   // Show floating banner (default: true)
    dataNukeSettings: {
      reactQuery: true,
      redux: false,      // Don't clear Redux by default
      asyncStorage: false,
      mmkv: false,
    },
  },
});
```

**Priority order:** User's persisted settings > Developer defaults > Built-in defaults

### Export Configuration

The Settings tab includes an "Export Configuration" section. Click **Copy Config** to copy your current settings as code that can be pasted directly into `createImpersonateTool()`.

---

## Hide Settings Tab

For simple testing scenarios where you don't want users changing settings:

```tsx
const impersonateTool = createImpersonateTool({
  onSearchUsers: searchUsers,
  showSettingsTab: false,  // Only show Search and History tabs
});
```

---

## Floating Banner

The impersonate tool automatically shows a floating banner at the top of the screen when impersonation is active. The banner displays:

- The impersonated user's avatar and name
- A **power toggle button** to pause/resume impersonation
- An **X button** to stop impersonation completely

### Banner Controls

| Control | Action |
|---------|--------|
| Tap user area | Opens the impersonate modal |
| Power button (green) | Pause impersonation — headers stop being injected |
| Power button (red) | Resume impersonation — headers start being injected again |
| X button | Stop impersonation completely |

### Pause vs Stop

- **Pause**: Temporarily stops injecting headers. The session remains active and can be resumed instantly. Useful for quick A/B testing between impersonated and normal views.
- **Stop**: Ends the impersonation session completely. Triggers data clearing based on your settings.

**No setup required** — the banner appears automatically. Users can toggle it off in the Settings tab, or you can set the default via `defaults.showBanner`:

```tsx
const impersonateTool = createImpersonateTool({
  onSearchUsers: searchUsers,
  defaults: {
    showBanner: false,  // Disable banner by default
  },
});
```

---

## Full Configuration Reference

```tsx
interface ImpersonateToolConfig {
  // Tool identity (optional)
  id?: string;                    // Default: 'impersonate'
  name?: string;                  // Default: 'IMPERSONATE'
  description?: string;           // Shown in tool settings

  // Required
  onSearchUsers: (query: string) => Promise<User[]>;

  // Data clearing callbacks
  // React Query & Redux are auto-detected — only provide if you need custom logic
  onClearReactQuery?: () => void | Promise<void>;
  onClearRedux?: () => void | Promise<void>;
  onClearAsyncStorage?: () => void | Promise<void>;  // Required for AsyncStorage
  onClearMMKV?: () => void | Promise<void>;          // Required for MMKV

  // Developer defaults (optional)
  defaults?: {
    headerKey?: string;           // Default: 'x-impersonate-user-id'
    showBanner?: boolean;         // Default: true
    dataNukeSettings?: {
      reactQuery?: boolean;       // Default: true
      redux?: boolean;            // Default: true
      asyncStorage?: boolean;     // Default: false
      mmkv?: boolean;             // Default: false
    };
  };

  // UI options
  showSettingsTab?: boolean;      // Default: true
}
```

---

## Backend Integration

Your backend must authenticate the request before this middleware, verify impersonation permission, and check whether the target user is in the operator's allowed scope. This sketch only illustrates the header and admin check; it is not a complete authorization implementation:

```typescript
// Integration sketch: run after your authentication middleware.
function impersonateMiddleware(req, res, next) {
  const impersonateUserId = req.headers['x-impersonate-user-id'];

  if (impersonateUserId) {
    // Verify the authenticated user has permission to impersonate
    if (!req.user?.isAdmin) {
      return res.status(403).json({ error: 'Impersonation not allowed' });
    }

    // Switch context to impersonated user
    req.effectiveUserId = impersonateUserId;
  } else {
    req.effectiveUserId = req.user.id;
  }

  next();
}
```

---

## What's Next

- [Network Monitor](./network) — See the impersonation headers in your requests
- [Redux DevTools](./redux) — Watch state changes when switching users
- [Storage Explorer](./storage) — Inspect persisted user data

---

## FAQ

### How does impersonation work — does it bypass my auth?

No. Buoy only attaches the headers you configure to outgoing requests. Your backend implements what impersonation means and enforces who may use it — Buoy is the on-device switch.

### Can I test feature flags for different user cohorts?

Yes — if your flag service keys off user identity or headers, switching the impersonated user flips the flags the app receives.

## Web support (unreleased)

Register this package’s /web namespace in FloatingDevTools modules to use its shared panels and actions in a browser app. The browser build is available in this checkout and has not been published yet. See the [web setup guide](../web-preview.md) for registration, dependencies, and browser boundaries.
