---
id: a1b2c3d4-1063-4000-8000-000000000063
title: Ruby
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001063
---
# Ruby

Ruby is a dynamic, interpreted language focused on developer happiness and elegant syntax. It powers Ruby on Rails, one of the most influential web frameworks.

## Design Philosophy

| Principle | Manifestation |
|-----------|---------------|
| Principle of Least Surprise | Code reads like natural language |
| Everything is an object | Even integers and nil |
| Convention over Configuration | Sensible defaults, minimal config |
| Don't Repeat Yourself (DRY) | Metaprogramming, generators |
| Matz is Nice And So We Are Nice (MINASWAN) | Friendly community |

## Key Language Features

```ruby
# Everything is an object
5.times { |i| puts "Count: #{i}" }
nil.class  # => NilClass

# Blocks (closures)
[1, 2, 3].map { |n| n * 2 }  # => [2, 4, 6]

# Symbols (immutable identifiers)
:user  # reusable, no garbage

# Metaprogramming
class User
  attr_accessor :name, :email  # generates getters/setters

  def self.attributes(*names)
    names.each do |name|
      define_method(name) { instance_variable_get("@#{name}") }
    end
  end

  attributes :name, :email
end
```

## Ruby on Rails

| Component | Purpose |
|-----------|---------|
| ActiveRecord | ORM (Object-Relational Mapping) |
| ActionPack | MVC routing + controllers |
| ActionView | Template rendering (ERB) |
| ActionCable | WebSockets integration |
| ActiveJob | Background job framework |
| ActionMailer | Email delivery |

## Convention Over Configuration

```ruby
# No XML config — naming conventions do the work
class UsersController < ApplicationController
  # GET /users maps to index action automatically
  def index
    @users = User.all
  end

  # POST /users maps to create
  def create
    @user = User.create(user_params)
    redirect_to @user
  end

  private

  def user_params
    params.require(:user).permit(:name, :email)
  end
end
```

## Gems (Package Management)

```
# Gemfile
source "https://rubygems.org"

gem "rails", "~> 7.2"
gem "pg"           # PostgreSQL adapter
gem "puma"         # Web server
gem "devise"       # Authentication
gem "rspec-rails"  # Testing
gem "rubocop"      # Linting
```

## Metaprogramming Power

```ruby
# method_missing — catch-all for undefined methods
class DynamicAPI
  def method_missing(name, *args)
    endpoint = name.to_s
    if api_responds_to?(endpoint)
      call_api(endpoint, *args)
    else
      super  # raise NoMethodError
    end
  end
end
```

## Testing

```ruby
# RSpec
RSpec.describe User, type: :model do
  subject { build(:user) }

  it { is_expected.to validate_presence_of(:email) }
  it { is_expected.to have_many(:posts) }

  describe "#full_name" do
    it "combines first and last name" do
      user = User.new(first_name: "John", last_name: "Doe")
      expect(user.full_name).to eq("John Doe")
    end
  end
end
```

## Performance Profile

| Aspect | Ruby | Comparison |
|--------|------|------------|
| Speed | Interpreted, slower | 5-20x slower than Go/Rust |
| Concurrency | GIL (Global Interpreter Lock) | One thread running Ruby at a time |
| Scaling | Horizontal (more processes) | Trade simplicity for throughput |

**Links**: [[Web Development Fundamentals]] | [[REST API Design]] | [[Object-Oriented Programming]] | [[Package Managers]] | [[API Testing]]
