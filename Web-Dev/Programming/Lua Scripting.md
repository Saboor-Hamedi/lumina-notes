---
id: a1b2c3d4-1090-4000-8000-000000000090
title: Lua Scripting
language: markdown
tags: [web-dev, programming, lua, scripting]
selection: null
isPinned: false
timestamp: 1781500001090
---
# Lua Scripting

Lua is a lightweight, embeddable scripting language known for speed, simplicity, and portability. It's used in game engines (Roblox, WoW), Redis scripting, and Neovim config.

## Design Philosophy

| Principle | Implication |
|-----------|-------------|
| Minimalist | Small language (< 20K LOC), easy to embed |
| Portable | Written in ANSI C, runs anywhere |
| Fast | One of the fastest dynamic languages |
| Extensible | C API for deep integration |
| Multi-paradigm | Procedural, functional, OOP (via metatables) |

## Basic Syntax

```lua
-- Variables
local name = "Alice"
local age = 30
local is_active = true
local nothing = nil

-- Tables (the only data structure — used for arrays, dicts, objects)
local arr = {1, 2, 3, 4, 5}
local dict = {name = "Alice", age = 30}

-- Access
print(arr[1])       -- 1 (1-indexed!)
print(dict.name)    -- Alice

-- Functions
local function factorial(n)
    if n <= 1 then return 1 end
    return n * factorial(n - 1)
end

-- Multiple return values
local function divmod(a, b)
    return math.floor(a / b), a % b
end
local q, r = divmod(10, 3)  -- q=3, r=1
```

## Metatables & OOP

```lua
-- Metatables enable operator overloading, inheritance
local Point = {}
Point.__index = Point

function Point.new(x, y)
    return setmetatable({x = x, y = y}, Point)
end

function Point:length()
    return math.sqrt(self.x^2 + self.y^2)
end

-- Operator overloading
Point.__add = function(a, b)
    return Point.new(a.x + b.x, a.y + b.y)
end

local p1 = Point.new(3, 4)
local p2 = Point.new(1, 2)
print(p1:length())       -- 5
local p3 = p1 + p2       -- (4, 6)
```

## C API Embedding

```c
#include <lua.h>
#include <lauxlib.h>
#include <lualib.h>

int main() {
    lua_State *L = luaL_newstate();
    luaL_openlibs(L);

    // Run Lua code
    luaL_dostring(L, "print('Hello from Lua!')");

    // Call Lua function
    lua_getglobal(L, "factorial");
    lua_pushnumber(L, 5);
    lua_call(L, 1, 1);
    int result = lua_tonumber(L, -1);
    lua_pop(L, 1);

    lua_close(L);
    return 0;
}
```

## Use Cases

| Domain | Why Lua |
|--------|---------|
| Game scripting | Fast, embeddable, sandboxable |
| Redis scripting | Lua EVAL for atomic operations |
| Neovim config | `init.lua` replaces VimScript |
| Adobe Lightroom | Plugin scripting |
| NGINX + Lua | OpenResty for web scripting |
| Embedded/IoT | Small memory footprint |

## Redis Lua Scripting

```lua
-- Atomic rate limiter
local key = KEYS[1]
local limit = tonumber(ARGV[1])
local ttl = tonumber(ARGV[2])

local current = redis.call("INCR", key)
if current == 1 then
    redis.call("EXPIRE", key, ttl)
end

if current > limit then
    return 0  -- rate limited
end
return 1  -- allowed
```

## Lua vs JavaScript

| Aspect | Lua | JavaScript |
|--------|-----|------------|
| Indexing | 1-based | 0-based |
| Data types | Few (table = everything) | Many (Array, Object, Map, Set) |
| Concurrency | Coroutines | async/await, Promises |
| Ecosystem | Small | Huge |
| Embedding | First-class (C API) | More complex |

**Links**: [[C and C++]] | [[Redis Deep Dive]] | [[WebAssembly]] | [[Game Development]] | [[Embedded Systems]]
