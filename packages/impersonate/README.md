# @buoy-gg/impersonate

[![npm version](https://img.shields.io/npm/v/@buoy-gg/impersonate?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/impersonate) [![npm downloads](https://img.shields.io/npm/dm/@buoy-gg/impersonate?style=flat-square&labelColor=1c1c1c&color=10B981)](https://www.npmjs.com/package/@buoy-gg/impersonate)

**Test your app as any user, role, or flag combination — without logging out, switching accounts, or rebuilding.**

Part of [Buoy](https://github.com/Buoy-gg/buoy) — devtools that live inside your React Native app. Install it and it auto-appears in the floating menu from [`@buoy-gg/core`](https://www.npmjs.com/package/@buoy-gg/core).

## Install

```bash
npm install @buoy-gg/core @buoy-gg/impersonate
```

## Quick start

Unlike most Buoy tools, this one needs a little config — it has to know how to search *your* users:

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { createImpersonateTool } from "@buoy-gg/impersonate";

const impersonateTool = createImpersonateTool({
  onSearchUsers: async (query) => {
    const { users } = await api.searchUsers({ email: query });
    return users.map((u) => ({
      id: u.id, // sent in the impersonation header
      displayName: u.name,
      email: u.email,
      metadata: { role: u.role },
    }));
  },
});

export default function App() {
  return (
    <>
      <YourApp />
      <FloatingDevTools apps={[impersonateTool]} />
    </>
  );
}
```

While impersonating, every outgoing `fetch` and `XMLHttpRequest` carries `x-impersonate-user-id: <id>` (header name configurable via `defaults.headerKey`). Your backend checks the header — after verifying the caller is an admin — and returns data for that user instead.

## What you get

- **Header injection everywhere** — patches `fetch` and XHR at startup, so even requests fired during app boot carry the impersonation header
- **Auto data clearing** — detects `@tanstack/react-query` and `react-redux` and clears the query cache / resets the store on every user switch, no callbacks needed (AsyncStorage/MMKV clearing via optional callbacks)
- **Floating banner** — appears automatically whenever impersonation is active, so you can never forget whose eyes you're looking through
- **Pause vs stop** — the banner's power button pauses header injection for instant A/B against your real account; X stops the session and triggers data clearing
- **History** — quick-switch between your last 10 impersonated users, persisted across app restarts
- **Developer defaults** — ship team-wide defaults for header name, banner, and clearing behavior; the Settings tab exports your current config as paste-ready code
- **`showSettingsTab: false`** — lock it down to just Search and History for simpler setups

## Desktop & AI

The same live session streams to [Buoy Desktop](https://github.com/Buoy-gg/Buoy-Desktop) (free, macOS/Windows/Linux) and to Claude Code or Cursor via the [Buoy MCP server](https://buoy.gg/buoy/latest/docs/mcp).

## Free vs Pro

Every tool is free. [Pro](https://buoy.gg/pricing) unlocks production builds, the MCP server, and unlimited capture. Every weekend, Pro features unlock free for everyone.

---

📚 [Full docs](https://buoy.gg/buoy/latest/docs/tools/impersonate) · [All Buoy tools](https://github.com/Buoy-gg/buoy)

Proprietary software. © Buoy LLC. [Terms](https://buoy.gg/terms)
