# BUOY Devtools

> **⚠️ ACTIVE DEVELOPMENT**
> This project is under active development. If you encounter any issues, please [open an issue on GitHub](https://github.com/Buoy-gg/buoy/issues).

[![npm version](https://img.shields.io/npm/v/@react-buoy/core?color=brightgreen)](https://www.npmjs.com/package/@react-buoy/core)
[![npm downloads](https://img.shields.io/npm/dm/@react-buoy/core?color=blue&label=downloads%2Fmonth)](https://www.npmjs.com/package/@react-buoy/core)
[![Legacy Package](https://img.shields.io/npm/dt/react-native-react-query-devtools?color=9cf&label=legacy%20downloads)](https://www.npmjs.com/package/react-native-react-query-devtools)
[![Dependents](https://img.shields.io/librariesio/dependents/npm/@react-buoy/core?color=success)](https://www.npmjs.com/package/@react-buoy/core)
[![TypeScript](https://img.shields.io/badge/TypeScript-100%25-blue)](https://www.typescriptlang.org/)

**See the bug. Reproduce it. Fix it in seconds.**

On-device devtools for React Native — network, storage, env, routes, query cache — available to your whole team in dev, staging, and production.

- **Zero config**: install → appears
- **QA/support can reproduce & export**
- **Modular**: use only what you need

📚 **[Full Documentation](https://buoy.gg/buoy/latest/docs/overview)** · 🚀 **[Quick Start Guide](https://buoy.gg/buoy/latest/docs/quick-start)**

![BUOY Demo](https://github.com/user-attachments/assets/a732d6a3-9963-49e3-b0f1-0d974a0a74d7)

---

## Quick Start

### 1. Install

```bash
npm install @react-buoy/core
# or: yarn add / pnpm add / bun add
```

### 2. Add to Your App

```tsx
import { FloatingDevTools } from "@react-buoy/core";

export default function App() {
  return (
    <>
      {/* Your app */}
      <FloatingDevTools environment="production" />
    </>
  );
}
```

That's it — installed tools automatically appear in the floating menu.

---

## Available Tools

| Tool        | Package                         | What It Does                                      |
| ----------- | ------------------------------- | ------------------------------------------------- |
| Network     | `@react-buoy/network`           | Monitor API requests, responses, timing, errors   |
| Storage     | `@react-buoy/storage`           | Browse and edit AsyncStorage & MMKV              |
| Environment | `@react-buoy/env`               | View and validate env variables with type checking |
| Query       | `@react-buoy/react-query`       | TanStack Query devtools with cache inspection     |
| Routes      | `@react-buoy/route-events`      | Track navigation changes and browse routes        |
| Profiler    | `@react-buoy/debug-borders`     | See re-renders, track counts, debug performance   |
| Highlights  | `@react-buoy/highlight-updates` | Visual component re-render tracking               |

Install any tool and it auto-appears in the menu:

```bash
npm i @react-buoy/{core,env,network,storage,react-query,route-events,debug-borders,highlight-updates}
```

> Full tool docs + screenshots: [buoy.gg/docs/tools](https://buoy.gg/buoy/latest/docs/overview)

---

## Why BUOY?

|                              | BUOY | Flipper | Reactotron | RN Debugger |
| ---------------------------- | :--: | :-----: | :--------: | :---------: |
| On-device (no desktop app)   | ✅   | ❌      | ❌         | ❌          |
| Works in production          | ✅   | ❌      | ❌         | ❌          |
| QA/support can use it        | ✅   | ❌      | ❌         | ❌          |
| Zero config setup            | ✅   | ❌      | ❌         | ❌          |

---

## Safe in Production

You control who sees it. BUOY runs entirely on-device — no data leaves the device, no remote connections.

```tsx
<>
  {/* Your app */}
  {isInternalUser && <FloatingDevTools />}
</>
```

---

## Early Access

Free during early access. Paid features coming (support tools, bug report workflows, and more).

---

## Contributing

- Bugs / feature requests: [GitHub Issues](https://github.com/Buoy-gg/buoy/issues)
- PRs welcome

---

## License

Proprietary software. © Buoy LLC. All rights reserved.

See [Terms of Service](https://buoy.gg/terms) for usage terms.

## 💙 Credits

Big thanks to [galaxies.dev](https://galaxies.dev) — their content helped me get up to speed with React Native early on, and I strongly recommend it as a resource for anyone making the jump from web to mobile.

<a href="https://galaxies.dev">
  <img src="https://github.com/Galaxies-dev/react-native-ecommerce/blob/main/banner.png?raw=true" width="100%" />
</a>