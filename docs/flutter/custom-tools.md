---
title: Custom Tools
seoTitle: "Build Custom Flutter DevTools — your own Buoy tool"
id: flutter-custom-tools
description: "Extend Buoy with your own debugging tools — register any Flutter widget in your app's floating devtools menu in just a few lines."
---

Add a custom widget with `BuoyTool`. The basic example mounts a counter so you can verify the tool opens and responds to input. Complete [Installation](./installation) first, including your account key and debug-mode widget setup.

## Basic Custom Tool

```dart
import 'package:buoy_core/buoy_core.dart';
import 'package:flutter/material.dart';

void main() {
  runApp(MaterialApp(
    builder: (context, child) => BuoyDevTools(
      licenseKey: const String.fromEnvironment('BUOY_KEY'),
      tools: [
        BuoyTool(
          id: 'counter',
          name: 'Counter',
          color: const Color(0xFFF87171),
          icon: (size, color) => Icon(Icons.plus_one, size: size, color: color),
          screenBuilder: (context) => const CounterDebugger(),
        ),
      ],
      child: child ?? const SizedBox.shrink(),
    ),
    home: const Scaffold(body: Center(child: Text('Open the Counter tool'))),
  ));
}

class CounterDebugger extends StatefulWidget {
  const CounterDebugger({super.key});

  @override
  State<CounterDebugger> createState() => _CounterDebuggerState();
}

class _CounterDebuggerState extends State<CounterDebugger> {
  int count = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Text('Count: $count'),
            FilledButton(
              onPressed: () => setState(() => count += 1),
              child: const Text('Increment'),
            ),
            FilledButton(
              onPressed: () => setState(() => count = 0),
              child: const Text('Reset'),
            ),
          ],
        ),
      ),
    );
  }
}
```

Run with `flutter run --dart-define=BUOY_KEY=YOUR_LICENSE_KEY`. Open Counter, increment it, then reset it to zero. Later sections are integration sketches for your own stores and widgets.

## Register Before Mount

Register before mounting when the tool also needs a Desktop or MCP adapter. This integration sketch assumes your app defines `MyApp`, `_count`, and `_listeners`; actions must notify those listeners after changes:

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
        actions: {
          'reset': (_) {
            _count = 0;
            for (final listener in List.of(_listeners)) {
              listener();
            }
          },
        },
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

Keep the tool inside the providers it needs. This Riverpod example assumes your app defines `authProvider`; import ConsumerWidget and WidgetRef from flutter_riverpod and the UI widgets from flutter/material.dart:

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
