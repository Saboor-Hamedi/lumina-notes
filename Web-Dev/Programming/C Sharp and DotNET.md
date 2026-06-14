---
id: a1b2c3d4-1195-4000-8000-000000000195
title: C Sharp and DotNET
language: markdown
tags: [web-dev, programming, csharp, dotnet]
selection: null
isPinned: false
timestamp: 1781500001195
---
# C# and .NET

C# is a modern, object-oriented language running on the .NET runtime. .NET provides a cross-platform framework for building web, desktop, mobile, and cloud applications.

## .NET Ecosystem

```bash
# .NET versions
# .NET Framework 4.x  → Windows only (legacy)
# .NET Core 3.1       → Cross-platform (end of life)
# .NET 5+             → Cross-platform, unified
# .NET 8 (LTS)        → Current stable
# .NET 9              → Latest preview

# Create new project
dotnet new webapi -n MyApi
dotnet new console -n MyConsole
dotnet new mvc -n MyWeb
dotnet new blazor -n MyBlazor
```

## C# Basics

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace MyApp.Models;

// Record (immutable data object)
public record User(
    int Id,
    string Name,
    string Email,
    DateTime CreatedAt
);

// Class with properties
public class Order
{
    public int Id { get; init; }           // init-only setter
    public string CustomerId { get; set; }
    public decimal Total { get; private set; }
    public List<OrderItem> Items { get; } = new();
    public DateTime CreatedAt { get; } = DateTime.UtcNow;

    public void AddItem(OrderItem item)
    {
        Items.Add(item);
        Total += item.Price * item.Quantity;
    }
}

// Primary constructor (C# 12)
public class Product(string name, decimal price)
{
    public string Name { get; } = name;
    public decimal Price { get; } = price;
    public string Slug => Name.ToLower().Replace(" ", "-");
}
```

## LINQ (Language Integrated Query)

```csharp
using System.Linq;

var users = GetUsers();

// Query syntax
var adults = from u in users
             where u.Age >= 18
             orderby u.Name
             select u;

// Method syntax
var results = users
    .Where(u => u.Age >= 18)
    .OrderBy(u => u.Name)
    .Select(u => new { u.Name, u.Email });

// Aggregations
var count = users.Count();
var oldest = users.Max(u => u.Age);
var average = users.Average(u => u.Age);
var grouped = users.GroupBy(u => u.City);

// Set operations
var distinct = users.Select(u => u.City).Distinct();
var intersection = set1.Intersect(set2);
var union = set1.Union(set2);
```

## Async/Await

```csharp
public class UserService
{
    private readonly HttpClient _http;

    public UserService(HttpClient http)
    {
        _http = http;
    }

    public async Task<User?> GetUserAsync(int id)
    {
        var response = await _http.GetAsync($"/api/users/{id}");
        response.EnsureSuccessStatusCode();

        var json = await response.Content.ReadAsStringAsync();
        return JsonSerializer.Deserialize<User>(json);
    }

    public async IAsyncEnumerable<User> StreamUsersAsync()
    {
        var response = await _http.GetAsync("/api/users",
            HttpCompletionOption.ResponseHeadersRead);
        await using var stream = await response.Content.ReadAsStreamAsync();
        await foreach (var user in JsonSerializer.DeserializeAsyncEnumerable<User>(stream))
        {
            if (user is not null) yield return user;
        }
    }

    // Parallel operations
    public async Task ProcessUsersAsync(int[] ids)
    {
        var tasks = ids.Select(GetUserAsync);
        var users = await Task.WhenAll(tasks);
        // Process all users...
    }
}
```

## ASP.NET Core Web API

```csharp
var builder = WebApplication.CreateBuilder(args);

// Services
builder.Services.AddDbContext<AppDbContext>(opts =>
    opts.UseNpgsql(builder.Configuration.GetConnectionString("Default")));
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options => { /* config */ });
builder.Services.AddAuthorization();

var app = builder.Build();

// Middleware pipeline
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();

app.Run();


// Controller
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    private readonly IUserService _service;

    public UsersController(IUserService service) => _service = service;

    [HttpGet]
    public async Task<ActionResult<List<User>>> GetAll(
        [FromQuery] int page = 1,
        [FromQuery] int limit = 20)
    {
        var users = await _service.GetAllAsync(page, limit);
        return Ok(users);
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<User>> GetById(int id)
    {
        var user = await _service.GetByIdAsync(id);
        if (user is null) return NotFound();
        return Ok(user);
    }

    [HttpPost]
    public async Task<ActionResult<User>> Create(CreateUserRequest request)
    {
        var user = await _service.CreateAsync(request);
        return CreatedAtAction(nameof(GetById), new { id = user.Id }, user);
    }
}
```

## Dependency Injection

```csharp
// Service registration
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddSingleton<ICacheService, RedisCacheService>();
builder.Services.AddTransient<IEmailService, EmailService>();

// Registration with factory
builder.Services.AddScoped<IService>(sp =>
{
    var dep = sp.GetRequiredService<IDependency>();
    return new ConcreteService(dep, "config");
});

// Options pattern
builder.Services.Configure<DatabaseOptions>(
    builder.Configuration.GetSection("Database"));
```

## Entity Framework Core

```csharp
public class AppDbContext : DbContext
{
    public DbSet<User> Users => Set<User>();
    public DbSet<Order> Orders => Set<Order>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>(entity =>
        {
            entity.HasKey(u => u.Id);
            entity.HasIndex(u => u.Email).IsUnique();
            entity.Property(u => u.Name).HasMaxLength(100).IsRequired();
        });

        modelBuilder.Entity<Order>(entity =>
        {
            entity.HasOne(o => o.User)
                  .WithMany(u => u.Orders)
                  .HasForeignKey(o => o.UserId);
        });
    }
}

// Usage
public class UserRepository
{
    private readonly AppDbContext _db;

    public async Task<User?> GetByEmailAsync(string email)
    {
        return await _db.Users
            .Include(u => u.Orders)
            .FirstOrDefaultAsync(u => u.Email == email);
    }

    public async Task<User> CreateAsync(User user)
    {
        _db.Users.Add(user);
        await _db.SaveChangesAsync();
        return user;
    }
}
```

**Links**: [[Async Python]] | [[C and C++]] | [[Compiler Design]] | [[Dart and Flutter]] | [[Elixir and Erlang]] | [[Finite Automata and Formal Languages]] | [[Flutter Deep Dive]] | [[Functional Programming Concepts]] | [[Functional Programming]] | [[Go Concurrency Patterns]] | [[Go Programming]] | [[Haskell]] | [[Java]] | [[Julia]] | [[Kotlin]] | [[Lua Scripting]] | [[Object-Oriented Programming]] | [[Pandas for Data Analysis]] | [[PHP]] | [[Programming Language Paradigms]] | [[Python Deep Dive]] | [[Python Imports and Modules]] | [[Python Type Hints]] | [[Python Virtual Environments]] | [[PyTorch Deep Dive]] | [[R for Data Science]] | [[Ruby]] | [[Rust Ownership and Borrowing]] | [[Rust]] | [[Scala]] | [[scikit-learn Deep Dive]] | [[Swift and iOS Development]] | [[TypeScript]]
