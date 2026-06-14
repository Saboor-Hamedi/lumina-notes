---
id: a1b2c3d4-1197-4000-8000-000000000197
title: Flutter Deep Dive
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001197
---
# Flutter Deep Dive

Flutter is Google's UI toolkit for building compiled applications for mobile, web, and desktop from a single codebase using the Dart language.

## Architecture

```
Framework (Dart)
    Material / Cupertino (design language)
    Widgets (composition over inheritance)
    Rendering (RenderObjects)
    Animation, Painting, Gestures

Engine (C/C++)
    Skia (graphics library)
    Dart runtime
    Text layout (HarfBuzz)
    Platform channels

Embedder (Platform-specific)
    Android (Java/Kotlin)
    iOS (ObjC/Swift)
    Web (Canvas/HTML)
    Desktop (Win32, macOS, Linux)
```

## Key Concepts

| Concept | Description |
|---------|-------------|
| Widget | Everything is a widget (UI element) |
| State | Immutable configuration + mutable state |
| Build | Widget tree recreated on state change |
| RenderObject | Lower-level rendering control |
| InheritedWidget | Pass data down the tree |
| Streams | Async data sequences |

## Basic App

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
    const MyApp({super.key});

    @override
    Widget build(BuildContext context) {
        return MaterialApp(
            title: 'Flutter Demo',
            theme: ThemeData(
                colorSchemeSeed: Colors.blue,
                useMaterial3: true,
            ),
            home: const HomePage(),
        );
    }
}

class HomePage extends StatefulWidget {
    const HomePage({super.key});

    @override
    State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
    int _count = 0;
    final _items = <String>[];
    final _controller = TextEditingController();

    void _addItem() {
        if (_controller.text.trim().isEmpty) return;
        setState(() {
            _items.add(_controller.text.trim());
            _controller.clear();
        });
    }

    @override
    void dispose() {
        _controller.dispose();
        super.dispose();
    }

    @override
    Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(title: const Text('My List')),
            body: Column(
                children: [
                    Padding(
                        padding: const EdgeInsets.all(16),
                        child: Row(
                            children: [
                                Expanded(
                                    child: TextField(
                                        controller: _controller,
                                        decoration: const InputDecoration(
                                            hintText: 'Add item...',
                                            border: OutlineInputBorder(),
                                        ),
                                    ),
                                ),
                                const SizedBox(width: 8),
                                ElevatedButton(
                                    onPressed: _addItem,
                                    child: const Text('Add'),
                                ),
                            ],
                        ),
                    ),
                    Expanded(
                        child: ListView.builder(
                            itemCount: _items.length,
                            itemBuilder: (context, index) {
                                return ListTile(
                                    title: Text(_items[index]),
                                    trailing: IconButton(
                                        icon: const Icon(Icons.delete),
                                        onPressed: () {
                                            setState(() => _items.removeAt(index));
                                        },
                                    ),
                                );
                            },
                        ),
                    ),
                    Text('Count: ${_items.length}',
                         style: Theme.of(context).textTheme.titleMedium),
                ],
            ),
        );
    }
}
```

## State Management (Riverpod)

```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

// Providers
final counterProvider = StateProvider<int>((ref) => 0);
final todoListProvider = StateNotifierProvider<TodoNotifier, List<Todo>>((ref) {
    return TodoNotifier();
});
final filteredTodosProvider = Provider.family<List<Todo>, String>((ref, filter) {
    final todos = ref.watch(todoListProvider);
    if (filter == 'active') return todos.where((t) => !t.done).toList();
    if (filter == 'completed') return todos.where((t) => t.done).toList();
    return todos;
});

// Model
class Todo {
    final int id;
    final String text;
    final bool done;
    Todo({required this.id, required this.text, this.done = false});
    Todo copyWith({int? id, String? text, bool? done}) {
        return Todo(
            id: id ?? this.id,
            text: text ?? this.text,
            done: done ?? this.done,
        );
    }
}

// Notifier
class TodoNotifier extends StateNotifier<List<Todo>> {
    TodoNotifier() : super([]);

    void add(String text) {
        state = [...state, Todo(id: DateTime.now().millisecondsSinceEpoch, text: text)];
    }

    void toggle(int id) {
        state = state.map((t) => t.id == id ? t.copyWith(done: !t.done) : t).toList();
    }

    void remove(int id) {
        state = state.where((t) => t.id != id).toList();
    }
}

// Usage in widget
class TodoList extends ConsumerWidget {
    @override
    Widget build(BuildContext context, WidgetRef ref) {
        final todos = ref.watch(todoListProvider);
        return ListView.builder(
            itemCount: todos.length,
            itemBuilder: (context, index) {
                final todo = todos[index];
                return CheckboxListTile(
                    value: todo.done,
                    title: Text(todo.text),
                    onChanged: (_) => ref.read(todoListProvider.notifier).toggle(todo.id),
                );
            },
        );
    }
}
```

## Layout

```dart
// Common layout widgets
Container(
    padding: const EdgeInsets.all(16),
    margin: const EdgeInsets.symmetric(vertical: 8),
    decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(8),
        boxShadow: [BoxShadow(color: Colors.black12, blurRadius: 4)],
    ),
    child: const Text('Hello'),
);

// Flex layouts
Row(
    mainAxisAlignment: MainAxisAlignment.spaceBetween,
    crossAxisAlignment: CrossAxisAlignment.center,
    children: [
        Expanded(child: Widget1()),
        SizedBox(width: 16),
        Widget2(),
    ],
);

Column(
    mainAxisSize: MainAxisSize.min,
    children: [
        Flexible(child: Widget1()),  // Takes available space
        Spacer(),                     // Pushes to bottom
        Widget2(),
    ],
);

// Stack (overlay)
Stack(
    children: [
        Image.network('https://example.com/photo.jpg'),
        Positioned(
            bottom: 16,
            left: 16,
            child: Text('Caption', style: TextStyle(color: Colors.white)),
        ),
    ],
);
```

## Networking

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;

class ApiService {
    static const _baseUrl = 'https://api.example.com';

    Future<List<User>> getUsers() async {
        final response = await http.get(Uri.parse('$_baseUrl/users'));

        if (response.statusCode == 200) {
            final List<dynamic> json = jsonDecode(response.body);
            return json.map((j) => User.fromJson(j)).toList();
        }
        throw Exception('Failed to load users: ${response.statusCode}');
    }

    Future<User> createUser(String name, String email) async {
        final response = await http.post(
            Uri.parse('$_baseUrl/users'),
            headers: {'Content-Type': 'application/json'},
            body: jsonEncode({'name': name, 'email': email}),
        );

        if (response.statusCode == 201) {
            return User.fromJson(jsonDecode(response.body));
        }
        throw Exception('Failed to create user');
    }
}
```
