---
id: a1b2c3d4-1086-4000-8000-000000000086
title: Elixir and Erlang
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001086
---
# Elixir and Erlang

Elixir runs on the Erlang VM (BEAM), designed for fault-tolerant, concurrent, distributed systems with hot code swapping.

## The BEAM Superpower

| Feature | How | Benefit |
|---------|-----|---------|
| Processes | Lightweight (microseconds to spawn) | Millions of concurrent actors |
| Isolation | Each process has its own heap | Crash doesn't affect others |
| Supervision | Tree of process monitors | Self-healing systems |
| Hot code swap | Load new code without restart | Zero-downtime deploys |
| Distribution | Transparent across nodes | Built-in clustering |

## Elixir Syntax

```elixir
# Pattern matching
{:ok, result} = {:ok, 42}
[head | tail] = [1, 2, 3]  # head = 1, tail = [2, 3]

# Pipe operator
"hello"
|> String.upcase()
|> String.graphemes()
|> Enum.sort()

# Functions with guards
def factorial(0), do: 1
def factorial(n) when n > 0, do: n * factorial(n - 1)
```

## Phoenix Framework

```elixir
# Router
defmodule MyApp.Router do
  use Phoenix.Router

  get "/users", UserController, :index
  post "/users", UserController, :create

  # LiveView (real-time UI)
  live "/dashboard", DashboardLive
end

# Context (business logic)
defmodule MyApp.Accounts do
  def list_users do
    Repo.all(User)
  end

  def get_user!(id), do: Repo.get!(User, id)
end

# Ecto (DB layer)
schema "users" do
  field :name, :string
  field :email, :string
  has_many :posts, Post
  timestamps()
end
```

## GenServer (Stateful Process)

```elixir
defmodule Counter do
  use GenServer

  def start_link(initial_count) do
    GenServer.start_link(__MODULE__, initial_count, name: __MODULE__)
  end

  def increment do
    GenServer.call(__MODULE__, :increment)
  end

  def get_count do
    GenServer.call(__MODULE__, :get_count)
  end

  @impl true
  def init(initial_count), do: {:ok, initial_count}

  @impl true
  def handle_call(:increment, _from, count) do
    {:reply, count + 1, count + 1}
  end

  @impl true
  def handle_call(:get_count, _from, count) do
    {:reply, count, count}
  end
end
```

## Supervision Tree

```elixir
children = [
  {Counter, 0},
  {DynamicSupervisor, strategy: :one_for_one},
  MyApp.Endpoint,
]

opts = [strategy: :one_for_one, name: MyApp.Supervisor]
Supervisor.start_link(children, opts)
```

| Strategy | Behavior |
|----------|----------|
| :one_for_one | Restart only the crashed child |
| :one_for_all | Restart all children |
| :rest_for_one | Restart crashed + later children |

## OTP (Open Telecom Platform)

| Behavior | Purpose |
|----------|---------|
| GenServer | Stateful server process |
| Supervisor | Process lifecycle management |
| Application | Application packaging |
| GenStage | Backpressure-aware pipelines |
| Task | Async computation |
| Agent | Simple state wrapper |

**Links**: [[Concurrency Models]] | [[Functional Programming]] | [[Message Queues]] | [[Microservices Architecture]] | [[Go Programming]]
