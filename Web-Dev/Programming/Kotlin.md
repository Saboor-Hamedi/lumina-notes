---
id: a1b2c3d4-1081-4000-8000-000000000081
title: Kotlin
language: markdown
tags: [web-dev, programming, kotlin]
selection: null
isPinned: false
timestamp: 1781500001081
---
# Kotlin

Kotlin is a statically-typed JVM language designed for conciseness, safety, and Java interop. It's the primary language for Android development.

## Key Features

| Feature | Description |
|---------|-------------|
| Null safety | `?` nullable types eliminate NPEs |
| Data classes | Auto-generate equals/hashCode/toString |
| Coroutines | Structured concurrency |
| Extension functions | Add methods to existing types |
| Smart casts | Auto-cast after type check |
| When expression | Powerful pattern matching |

## Core Syntax

```kotlin
// Data class (one-liner POJO)
data class User(val name: String, val age: Int)

// Null safety
fun length(s: String?): Int = s?.length ?: 0

// Extension function
fun String.isEmail(): Boolean = this.contains("@")

// Sealed class (algebraic data types)
sealed class Result {
    data class Success(val data: Any) : Result()
    data class Error(val message: String) : Result()
}

// When expression
fun describe(obj: Any) = when (obj) {
    is String -> "String of length ${obj.length}"
    is Int -> if (obj > 0) "Positive" else "Non-positive"
    else -> "Unknown"
}
```

## Coroutines

```kotlin
suspend fun fetchUser(id: String): User {
    delay(100)  // non-blocking
    return api.getUser(id)
}

fun main() = runBlocking {
    // Sequential
    val user = fetchUser("123")

    // Concurrent
    val (user1, user2) = async { fetchUser("1") } to async { fetchUser("2") }
    println("${user1.await()} ${user2.await()}")

    // Parallel collection
    users.map { async { process(it) } }.awaitAll()
}
```

## Flow (Reactive Streams)

```kotlin
fun observeMessages(): Flow<Message> = flow {
    while (true) {
        val msg = kafka.poll()
        emit(msg)
    }
}.flowOn(Dispatchers.IO)
  .catch { e -> println("Error: $e") }

// Collect
observeMessages().collect { println(it) }
```

## Android Development

```kotlin
// Jetpack Compose UI
@Composable
fun Greeting(name: String) {
    var count by remember { mutableStateOf(0) }
    Column {
        Text("Hello, $name! Count: $count")
        Button(onClick = { count++ }) { Text("Increment") }
    }
}

// ViewModel
class UserViewModel : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users

    init { viewModelScope.launch { loadUsers() } }
}
```

**Links**: [[Java]] | [[Scala]] | [[Object-Oriented Programming]] | [[Concurrency Models]] | [[Functional Programming]]
