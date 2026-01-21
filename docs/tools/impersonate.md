---
title: Impersonate Tool
id: tools-impersonate
---

<!-- ::platform-badge platform="both" -->

Admin user impersonation for React Native. Test your app as any user by injecting impersonation headers into all network requests — without logging out or switching accounts.

## Installation

<!-- ::PM npm="npm install @buoy-gg/impersonate" yarn="yarn add @buoy-gg/impersonate" pnpm="pnpm add @buoy-gg/impersonate" bun="bun add @buoy-gg/impersonate" -->

Unlike other Buoy tools, the Impersonate tool requires configuration because it needs to integrate with your user search API.

---

## Quick Start

```tsx
import { createImpersonateTool } from '@buoy-gg/impersonate';
import { FloatingDevTools } from '@buoy-gg/dev-tools';

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
      <FloatingDevTools tools={[impersonateTool]} />
    </>
  );
}
```

---

## How It Works

When impersonation is active, the tool automatically injects a header into **every** outgoing `fetch` and `XMLHttpRequest`:

```
x-impersonate-user-id: user_123
```

Your backend checks for this header and returns data for the specified user instead of the authenticated admin.

> **Security Note:** Your backend should validate that the authenticated user has admin/impersonation permissions before honoring this header.

---

## Features

### User Search
Search for users by email, name, or ID. Results display in a clean card format showing user details and metadata.

### Impersonation History
Quick-switch between recently impersonated users. History persists across app restarts (up to 10 users).

### Data Clearing
Automatically clear stale data when switching users:

| Option | Description | Default |
|--------|-------------|---------|
| React Query | Clear query cache and cancel pending queries | ✅ On |
| Redux | Reset Redux store to initial state | ✅ On |
| AsyncStorage | Clear app data (preserves `@buoy/*` keys) | ❌ Off |
| MMKV | Clear MMKV storage (preserves `@buoy/*` keys) | ❌ Off |

### Floating Banner
A floating banner automatically appears when impersonation is active, making it impossible to forget you're viewing as another user. The banner can be toggled on/off in Settings.

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

### Optional: Data Clearing Callbacks

Integrate with your app's state management:

```tsx
const impersonateTool = createImpersonateTool({
  onSearchUsers: searchUsers,

  // Clear React Query cache
  onClearReactQuery: () => queryClient.clear(),

  // Reset Redux store
  onClearRedux: () => store.dispatch({ type: 'RESET' }),

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

- The impersonated user's name/email
- A quick "Stop" button to end impersonation

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

  // Data clearing callbacks (optional)
  onClearReactQuery?: () => void | Promise<void>;
  onClearRedux?: () => void | Promise<void>;
  onClearAsyncStorage?: () => void | Promise<void>;
  onClearMMKV?: () => void | Promise<void>;

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

Your backend needs to check for the impersonation header and verify permissions:

```typescript
// Express middleware example
function impersonateMiddleware(req, res, next) {
  const impersonateUserId = req.headers['x-impersonate-user-id'];

  if (impersonateUserId) {
    // Verify the authenticated user has permission to impersonate
    if (!req.user.isAdmin) {
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
