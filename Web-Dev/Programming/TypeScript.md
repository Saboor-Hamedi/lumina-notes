---
id: a1b2c3d4-1021-4000-8000-000000000021
title: TypeScript
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001021
---
# TypeScript

TypeScript is a typed superset of JavaScript that compiles to plain JS. It adds static typing, interfaces, generics, and modern language features.

## Core Types

| Type | Example | Description |
|------|---------|-------------|
| `boolean` | `true` | True/false |
| `number` | `42` | All numeric (int + float) |
| `string` | `"hello"` | Text |
| `array` | `number[]` | Homogeneous arrays |
| `tuple` | `[string, number]` | Fixed-length typed array |
| `enum` | `Color.Red` | Named constants |
| `unknown` | `any` type | Type-safe `any` (must narrow) |
| `never` | `throw` | Never returns |
| `void` | `undefined` | No return value |

## Interfaces vs Types

```typescript
// Interface (extendable, declaration merging)
interface User {
  name: string;
  age: number;
}

// Type alias (unions, intersections, primitives)
type Status = "active" | "inactive" | "pending";
type Admin = User & { role: "admin" };
```

## Generics

```typescript
function identity<T>(arg: T): T {
  return arg;
}

interface Repository<T> {
  get(id: string): T;
  save(item: T): void;
}

// Constraints
function getLength<T extends { length: number }>(arg: T): number {
  return arg.length;
}
```

## Utility Types

| Utility | Transforms |
|---------|------------|
| `Partial<T>` | All properties optional |
| `Required<T>` | All properties required |
| `Pick<T, K>` | Select specific keys |
| `Omit<T, K>` | Exclude specific keys |
| `Record<K, V>` | Object type with key/value types |
| `Exclude<T, U>` | Remove types from union |
| `ReturnType<T>` | Extract return type of function |
| `Awaited<T>` | Unwrap promise type |

## Advanced Patterns

```typescript
// Discriminated unions
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "rect"; width: number; height: number };

function area(s: Shape): number {
  switch (s.kind) {
    case "circle": return Math.PI * s.radius ** 2;
    case "rect": return s.width * s.height;
  }
}

// Branded types
type UserId = string & { __brand: "UserId" };
```

## tsconfig Key Options

```json
{
  "strict": true,
  "noImplicitAny": true,
  "strictNullChecks": true,
  "target": "ES2022",
  "module": "ESNext",
  "moduleResolution": "bundler"
}
```

**Links**: [[Object-Oriented Programming]] | [[Functional Programming]] | [[React]] | [[Python Type Hints]] | [[Async Python]]
