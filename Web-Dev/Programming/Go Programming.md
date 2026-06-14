---
id: a1b2c3d4-1023-4000-8000-000000000023
title: Go Programming
language: markdown
tags: [web-dev, programming, go, golang]
selection: null
isPinned: false
timestamp: 1781500001023
---
# Go Programming

Go (Golang) is a compiled, statically-typed language designed for simplicity, fast compilation, and scalable concurrency.

## Key Design Decisions

| Decision | Why |
|----------|-----|
| No inheritance | Composition over inheritance |
| No generics (initially) | Simpler compiler, added in Go 1.18 |
| No exceptions | Explicit error returns |
| Goroutines | Lightweight threads (cheap, thousands) |
| Fast compilation | No template expansion, fast linking |

## Variables & Types

```go
var name string = "Alice"
age := 30 // type inference

// Constants
const Pi = 3.14159

// Structs (no classes)
type User struct {
    Name string
    Age  int
}

// Methods on structs
func (u User) Greet() string {
    return "Hello, " + u.Name
}
```

## Interfaces

```go
type Writer interface {
    Write([]byte) (int, error)
}

// Implicit implementation (no "implements" keyword)
type ConsoleWriter struct{}
func (cw ConsoleWriter) Write(data []byte) (int, error) {
    return fmt.Println(string(data))
}
```

## Error Handling

```go
// No try/catch — errors are values
func divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    return a / b, nil
}

result, err := divide(10, 0)
if err != nil {
    log.Fatal(err)
}
```

## Concurrency (Goroutines & Channels)

```go
// Goroutine: lightweight thread
go func() {
    fmt.Println("running in parallel")
}()

// Channel: communicate between goroutines
ch := make(chan string)
go func() {
    ch <- "hello from goroutine"
}()
msg := <-ch

// Select: multiplex channels
select {
case msg1 := <-ch1:
    fmt.Println(msg1)
case msg2 := <-ch2:
    fmt.Println(msg2)
case <-time.After(1 * time.Second):
    fmt.Println("timeout")
}
```

## Standard Library Strengths

| Package | Purpose |
|---------|---------|
| `net/http` | HTTP server/client |
| `encoding/json` | JSON marshal/unmarshal |
| `database/sql` | SQL database interface |
| `sync` | Mutexes, WaitGroups |
| `context` | Cancellation, deadlines |
| `testing` | Built-in testing |
| `io/fs` | Filesystem abstraction |

## Struct Tags & JSON

```go
type User struct {
    ID    int    `json:"id"`
    Name  string `json:"name"`
    Email string `json:"email,omitempty"`
}
```

## Modules (Go 1.16+)

```
go mod init example.com/myproject
go get github.com/gorilla/mux
go mod tidy  // clean up dependencies
```

**Links**: [[Concurrency Models]] | [[REST API Design]] | [[Microservices Architecture]] | [[Docker Containers]] | [[Package Managers]]
