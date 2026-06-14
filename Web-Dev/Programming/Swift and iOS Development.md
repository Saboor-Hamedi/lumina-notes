---
id: a1b2c3d4-1082-4000-8000-000000000082
title: Swift and iOS Development
language: markdown
tags: [web-dev, programming, swift, ios]
selection: null
isPinned: false
timestamp: 1781500001082
---
# Swift and iOS Development

Swift is a compiled language for Apple platforms, designed for safety, performance, and expressive syntax. iOS development uses Swift with the Cocoa Touch frameworks.

## Swift Features

| Feature | Example |
|---------|---------|
| Optionals | `var name: String?` |
| Pattern matching | `switch value { case .some: }` |
| Protocol-oriented | Protocols with default implementations |
| Value semantics | Structs are preferred over classes |
| Closures | First-class functions with capture |
| Result builders | Declarative UI (SwiftUI) |
| Structured concurrency | async/await, actors |

## Value vs Reference Types

```swift
struct Point { var x, y: Double }  // Value type (struct)
class Car { var model: String }    // Reference type (class)

let p1 = Point(x: 1, y: 2)
var p2 = p1
p2.x = 5  // p1.x is still 1 (copy)

// Prefer structs for data, classes for identity
```

## SwiftUI

```swift
import SwiftUI

struct ContentView: View {
    @State private var count = 0

    var body: some View {
        VStack(spacing: 20) {
            Text("Count: \(count)")
                .font(.title)
            Button("Increment") {
                count += 1
            }
            .padding()
            .background(.blue)
            .foregroundColor(.white)
            .clipShape(RoundedRectangle(cornerRadius: 8))
        }
    }
}
```

## Concurrency (async/await)

```swift
func fetchUser(id: String) async throws -> User {
    let url = URL(string: "https://api.example.com/users/\(id)")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode(User.self, from: data)
}

// Parallel
async let user1 = fetchUser(id: "1")
async let user2 = fetchUser(id: "2")
let users = try await [user1, user2]

// Actor (protect shared mutable state)
actor Counter {
    private var value = 0
    func increment() { value += 1 }
    func getValue() -> Int { value }
}
```

## iOS Architecture Patterns

| Pattern | Description |
|---------|-------------|
| MVC | Model-View-Controller (Apple default) |
| MVVM | Model-View-ViewModel (SwiftUI native) |
| VIPER | View-Interactor-Presenter-Entity-Router |
| Clean Swift | Uncle Bob's clean architecture for iOS |

## Key Frameworks

| Framework | Purpose |
|-----------|---------|
| SwiftUI | Declarative UI |
| UIKit | Imperative UI (legacy) |
| Combine | Reactive streams (Apple's RxSwift) |
| Core Data | Object graph + persistence |
| CloudKit | iCloud backend |
| CoreML | On-device ML |
| ARKit | Augmented reality |
| MapKit | Maps and location |
| WidgetKit | Home screen widgets |

## App Lifecycle

```swift
@main
struct MyApp: App {
    @Environment(\.scenePhase) var scenePhase

    var body: some Scene {
        WindowGroup {
            ContentView()
        }
        .onChange(of: scenePhase) { phase in
            switch phase {
            case .active: print("App is active")
            case .inactive: print("App inactive")
            case .background: print("App in background")
            @unknown default: break
            }
        }
    }
}
```

**Links**: [[Memory Management]] | [[Concurrency Models]] | [[Rust]] | [[Object-Oriented Programming]] | [[Functional Programming]]
