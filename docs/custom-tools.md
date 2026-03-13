---
title: Custom Tools
id: custom-tools
---

React Buoy is fully extensible. You can add any React component as a custom debugging tool.

## Basic Custom Tool

```tsx
import { FloatingDevTools } from "@buoy-gg/core";
import { View, Text, Button } from "react-native";

const CacheDebugger = () => {
  const clearCache = () => {
    // Your cache clearing logic
  };

  return (
    <View>
      <Text>Cache Status: 42 items</Text>
      <Button title="Clear Cache" onPress={clearCache} />
    </View>
  );
};

function App() {
  return (
    <FloatingDevTools
      environment="local"
      customTools={[
        {
          name: "Cache",
          component: CacheDebugger,
          icon: "🗑️",
        },
      ]}
    />
  );
}
```

## Custom Tool Schema

```typescript
interface CustomTool {
  /** Display name in the menu */
  name: string;

  /** The React component to render */
  component: React.ComponentType;

  /** Emoji or icon to display */
  icon?: string;

  /** Optional description shown in menu */
  description?: string;

  /** Enable in dial/floating row by default (before user configures preferences) */
  enabledByDefault?: boolean;
}
```

## Multiple Custom Tools

```tsx
<FloatingDevTools
  environment="local"
  customTools={[
    {
      name: "Auth",
      component: AuthDebugger,
      icon: "🔐",
      description: "View auth state",
    },
    {
      name: "Feature Flags",
      component: FeatureFlagViewer,
      icon: "🚩",
      description: "Toggle features",
    },
    {
      name: "Analytics",
      component: AnalyticsDebugger,
      icon: "📊",
      description: "Event tracking",
    },
  ]}
/>
```

## Default-Enabled Tools

By default, all tools start disabled in the dial and floating row until the user
enables them via the settings modal. Use `enabledByDefault: true` to have a tool
appear in the dial and floating row immediately, without requiring manual setup.

Once the user has toggled a tool in the settings modal, their preference is
persisted and always takes precedence over `enabledByDefault`.

```tsx
<FloatingDevTools
  environment="local"
  customTools={[
    {
      name: "Auth",
      component: AuthDebugger,
      icon: "🔐",
      description: "View auth state",
      enabledByDefault: true, // Appears in the dial immediately
    },
    {
      name: "Feature Flags",
      component: FeatureFlagViewer,
      icon: "🚩",
      description: "Toggle features",
      // Not enabled by default — user must opt in via settings
    },
  ]}
/>
```

## Accessing App State

Your custom tools can use any React hooks or context:

```tsx
import { useAuth } from "./hooks/useAuth";
import { useQueryClient } from "@tanstack/react-query";

const AuthDebugger = () => {
  const { user, logout } = useAuth();
  const queryClient = useQueryClient();

  const forceLogout = () => {
    logout();
    queryClient.clear();
  };

  return (
    <View>
      <Text>User: {user?.email ?? "Not logged in"}</Text>
      <Text>Role: {user?.role}</Text>
      <Button title="Force Logout" onPress={forceLogout} />
    </View>
  );
};
```

## Next Steps

- [FloatingDevTools](./floating-devtools) — Core component reference
- [Quick Start](./quick-start) — Full setup walkthrough
