---
id: a1b2c3d4-1088-4000-8000-000000000088
title: Scala
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001088
---
# Scala

Scala combines object-oriented and functional programming on the JVM. It's used for data engineering (Spark), web services (Play), and distributed systems (Akka).

## Language Highlights

| Feature | Description |
|---------|-------------|
| Hybrid OOP/FP | Everything is an object + first-class functions |
| Immutability | `val` vs `var`, immutable collections by default |
| Pattern matching | Powerful destructuring with guards |
| Case classes | Immutable data with auto-implemented methods |
| Implicits | Extension methods, type classes |
| For comprehensions | Monadic sequence operations |
| Akka actors | Actor model concurrency |

## Core Syntax

```scala
// Case class (immutable data)
case class User(name: String, age: Int)

// Pattern matching
val result = user match {
    case User("Alice", _) => "Found Alice"
    case User(name, age) if age > 18 => s"Adult: $name"
    case _ => "Unknown"
}

// For comprehension
val result = for {
    user <- users
    post <- user.posts
    if post.published
} yield (user.name, post.title)

// Higher-order functions
val nums = List(1, 2, 3, 4, 5)
nums.map(_ * 2)             // List(2, 4, 6, 8, 10)
nums.filter(_ % 2 == 0)     // List(2, 4)
nums.foldLeft(0)(_ + _)     // 15
```

## Implicits & Extension Methods

```scala
// Extension method
implicit class StringOps(s: String) {
    def isEmail: Boolean = s.contains("@")
    def truncate(n: Int): String = s.take(n) + "..."
}

"hello@example.com".isEmail  // true

// Type class pattern
trait JsonWriter[A] {
    def write(value: A): String
}

implicit val intWriter: JsonWriter[Int] = (value: Int) => value.toString
```

## Akka Actors

```scala
import akka.actor.{Actor, ActorSystem, Props}

class CounterActor extends Actor {
    var count = 0

    def receive = {
        case "increment" => count += 1
        case "get" => sender() ! count
    }
}

val system = ActorSystem("my-system")
val counter = system.actorOf(Props[CounterActor](), "counter")
counter ! "increment"
counter ! "get"  // reply sent to sender
```

## Functional Error Handling

```scala
// Option (nullable)
val name: Option[String] = Some("Alice")
val result = name.map(_.length).getOrElse(0)

// Either (error handling)
def divide(x: Int, y: Int): Either[String, Int] =
    if (y == 0) Left("Division by zero")
    else Right(x / y)

// Try (exception handling)
import scala.util.Try
val result = Try("42".toInt).toOption
```

## Spark with Scala

```scala
import org.apache.spark.sql.SparkSession

val spark = SparkSession.builder.appName("app").getOrCreate()
import spark.implicits._

case class Event(id: String, event_type: String, amount: Double)

val df = spark.read.parquet("events/")
val result = df
    .groupBy("event_type")
    .agg(sum("amount") as "total")
    .orderBy(desc("total"))
```

**Links**: [[Java]] | [[Kotlin]] | [[Functional Programming]] | [[Object-Oriented Programming]] | [[Apache Spark]]
