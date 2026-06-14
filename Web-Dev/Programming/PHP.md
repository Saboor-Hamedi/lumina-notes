---
id: a1b2c3d4-1083-4000-8000-000000000083
title: PHP
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001083
---
# PHP

PHP is a server-side scripting language that powers a significant portion of the web, including WordPress, Laravel, and Facebook (originally).

## Modern PHP (8.x)

| Feature | Description |
|---------|-------------|
| JIT compilation | Performance improvements (8.0) |
| Named arguments | `func(name: "Alice", age: 30)` |
| Attributes | Native annotations, like Java annotations |
| Union types | `int\|string` parameter types |
| Match expression | Type-safe switch with return |
| Enums | First-class enumerated types |
| Readonly properties | Immutable object properties |

## Core Syntax

```php
<?php

declare(strict_types=1);

// Typed properties (PHP 7.4+)
class User {
    public function __construct(
        readonly public string $name,
        readonly public int $age,
    ) {}
}

// Enum (PHP 8.1+)
enum Status: string {
    case Active = 'active';
    case Inactive = 'inactive';
}

// Match expression (PHP 8.0+)
$result = match ($status) {
    Status::Active => 'User is active',
    Status::Inactive => 'User is inactive',
};

// Named arguments (PHP 8.0+)
$user = new User(name: 'Alice', age: 30);
```

## Laravel Framework

| Component | Purpose |
|-----------|---------|
| Eloquent ORM | Active Record ORM |
| Blade | Template engine |
| Artisan | CLI tool for generators, migrations |
| Queues | Redis/DB-backed job queues |
| Events | Pub/sub event system |
| Sanctum | API token authentication |
| Horizon | Redis queue dashboard |
| Livewire | Full-stack reactive components |

```php
// Eloquent ORM
$users = User::where('active', true)
    ->whereHas('posts', fn($q) => $q->where('published', true))
    ->orderBy('name')
    ->paginate(20);

// Blade template
{{-- resources/views/users/index.blade.php --}}
@foreach($users as $user)
    <div>{{ $user->name }} ({{ $user->posts_count }} posts)</div>
@endforelse
```

## WordPress Development

```php
// Custom post type
function register_book_post_type() {
    register_post_type('book', [
        'labels' => ['name' => 'Books'],
        'public' => true,
        'supports' => ['title', 'editor', 'thumbnail'],
        'has_archive' => true,
    ]);
}
add_action('init', 'register_book_post_type');

// Shortcode
function greeting_shortcode($atts) {
    $name = $atts['name'] ?? 'World';
    return "<h2>Hello, $name!</h2>";
}
add_shortcode('greeting', 'greeting_shortcode');
```

## Composer

```json
{
    "require": {
        "laravel/framework": "^11.0",
        "spatie/laravel-permission": "^6.0"
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/"
        }
    }
}
```

## Common Criticisms & Mitigations

| Criticism | Mitigation |
|-----------|------------|
| Inconsistent function naming | Use framework conventions |
| Loose typing | `declare(strict_types=1)` |
| Legacy code | Modern PHP 8+ is vastly improved |
| Security | Parameterized queries, input validation |

**Links**: [[Web Development Fundamentals]] | [[REST API Design]] | [[Laravel]] | [[Ruby]] | [[Package Managers]]
