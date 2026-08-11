---
title: Custom Tools
id: flutter-custom-tools
description: "Extend Buoy with your own debugging tools — register any Flutter widget in your app's floating devtools menu in just a few lines."
---

Buoy for Flutter is fully extensible. You can add any widget as a custom debugging tool via `BuoyTool` — the same registration path first-party tools use.

## Basic Custom Tool

```dart
import 'package:buoy_core/buoy_core.dart';
import 'package:flutter/material.dart';

class CacheDebugger extends StatelessWidget {
  const CacheDebugger({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            const Text('Cache Status: 42 items'),
            FilledButton(
              onPressed: () {
                // Your cache clearing logic
              },
              child: const Text('Clear Cache'),
            ),
          ],
        ),
      ),
    );
  }
}

// Option A — pass via BuoyDevTools
MaterialApp(
  builder: (context, child) => BuoyDevTools(
    tools: [
      BuoyTool(
        id: 'cache',
        name: 'Cache',
        description: 'Inspect app cache',
        color: const Color(0xFFF87171),
        icon: (size, color) => Icon(Icons.delete_outline, size: size, color: color),
        screenBuilder: (context) => const CacheDebugger(),
      ),
    ],
    child: child ?? const SizedBox.shrink(),
  ),
)
```

## Register Before Mount

Or register globally (useful when the tool also syncs to Desktop / MCP):

```dart
void main() {
  if (kDebugMode) {
    Buoy.registerTool(
      BuoyTool(
        id: 'counter',
        name: 'Counter',
        color: const Color(0xFF34D399),
        icon: (size, color) => Icon(Icons.plus_one, size: size, color: color),
        onPressed: (context) {
          // Toggle-style: no screen — just run an action
        },
      ),
      adapter: ToolSyncAdapter(
        version: 1,
        getSnapshot: () => {'count': _count},
        subscribe: (onChange) {
          _listeners.add(onChange);
          return () => _listeners.remove(onChange);
        },
        actions: {'reset': (_) => _count = 0},
      ),
    );
  }
  runApp(const MyApp());
}
```

## BuoyTool Schema

| Field | Type | Notes |
|-------|------|-------|
| `id` | `String` | Stable id (dedupes with first-party tools) |
| `name` | `String` | Dial label (rendered uppercase) |
| `color` | `Color` | Accent on the dial |
| `icon` | `(size, color) → Widget` | Prefer a Buoy icon or Material glyph |
| `description` | `String?` | Shown in settings tool cards |
| `screenBuilder` | `WidgetBuilder?` | Full-screen tool host |
| `modalBuilder` | `BuoyToolModalBuilder?` | Draggable/resizable modal (wins over screen) |
| `onPressed` | `void Function(BuildContext)?` | Toggle-style — no UI opens |

## Multiple Custom Tools

```dart
BuoyDevTools(
  tools: [
    BuoyTool(
      id: 'auth',
      name: 'Auth',
      description: 'View auth state',
      color: const Color(0xFF818CF8),
      icon: (size, color) => Icon(Icons.lock, size: size, color: color),
      screenBuilder: (context) => const AuthDebugger(),
    ),
    BuoyTool(
      id: 'flags',
      name: 'Flags',
      description: 'Toggle features',
      color: const Color(0xFFFBBF24),
      icon: (size, color) => Icon(Icons.flag, size: size, color: color),
      screenBuilder: (context) => const FeatureFlagViewer(),
    ),
  ],
  child: child ?? const SizedBox.shrink(),
)
```

## Accessing App State

Your custom tools are normal Flutter widgets — use whatever state you already have (`Provider`, Riverpod, `InheritedWidget`, singletons):

```dart
class AuthDebugger extends ConsumerWidget {
  const AuthDebugger({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final user = ref.watch(authProvider);
    return ListView(
      padding: const EdgeInsets.all(16),
      children: [
        Text('User: ${user?.email ?? 'Not logged in'}'),
        Text('Role: ${user?.role ?? '—'}'),
        FilledButton(
          onPressed: () => ref.read(authProvider.notifier).logout(),
          child: const Text('Force Logout'),
        ),
      ],
    );
  }
}
```

## Next Steps

- [BuoyDevTools](./buoy-devtools) — Core widget reference
- [Buoy Desktop](../desktop) — The full desktop dashboard
- [AI / MCP Server](../mcp) — Drive your app from your AI editor
- [Quick Start](./quick-start) — Full setup walkthrough
