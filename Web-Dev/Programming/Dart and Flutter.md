---
id: a1b2c3d4-1084-4000-8000-000000000084
title: Dart and Flutter
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001084
---
# Dart and Flutter

Dart is a client-optimized language for fast apps on any platform. Flutter is Google's UI toolkit for building natively compiled apps from a single codebase.

## Dart Language

| Feature | Description |
|---------|-------------|
| Sound null safety | Non-nullable by default |
| AOT + JIT | AOT for release, JIT for hot reload |
| Isolates | Thread-based concurrency (no shared memory) |
| Pattern matching | Destructuring, switch expressions |
| Records | Anonymous immutable aggregates |
| Sealed classes | Exhaustive type hierarchies |

```dart
// Records (Dart 3)
(String name, int age) user = ('Alice', 30);

// Sealed class
sealed class Shape {}
class Circle extends Shape { final double radius; }
class Rect extends Shape { final double w, h; }

double area(Shape s) => switch (s) {
    Circle(r: var r) => 3.14 * r * r,
    Rect(w: var w, h: var h) => w * h,
};

// Null safety
String? name;
print(name ?? 'default');
```

## Flutter Widget Tree

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: const Text('Hello')),
        body: const CounterWidget(),
      ),
    );
  }
}

class CounterWidget extends StatefulWidget {
  const CounterWidget({super.key});
  @override
  State<CounterWidget> createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _count = 0;

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Text('Count: $_count', style: Theme.of(context).textTheme.headlineMedium),
          ElevatedButton(
            onPressed: () => setState(() => _count++),
            child: const Text('Increment'),
          ),
        ],
      ),
    );
  }
}
```

## State Management

| Approach | When to Use |
|----------|-------------|
| setState | Simple local state |
| Provider | DI + simple state sharing |
| Riverpod | Provider 2.0 (compile-safe, testable) |
| Bloc | Complex event-driven state |
| Redux | Predictable state container |
| GetX | Lightweight, all-in-one |

## Key Differences from React Native

| Aspect | Flutter | React Native |
|--------|---------|--------------|
| Rendering | Skia engine (own canvas) | Native components via bridge |
| Language | Dart | JavaScript/TypeScript |
| Performance | Better (no bridge) | Slightly slower |
| UI | Everything is a widget | Platform native components |
| Hot reload | Yes | Yes |

## Platform Channels

```dart
// Call native code
class BatteryLevel {
  static const channel = MethodChannel('samples.flutter.dev/battery');
  static Future<int> get() async {
    return await channel.invokeMethod('getBatteryLevel');
  }
}
```

**Links**: [[Web Development Fundamentals]] | [[Object-Oriented Programming]] | [[React]] | [[Swift and iOS Development]] | [[TypeScript]]
