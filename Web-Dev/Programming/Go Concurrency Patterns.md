---
id: a1b2c3d4-1151-4000-8000-000000000151
title: Go Concurrency Patterns
language: markdown
tags: ''
selection: null
isPinned: false
timestamp: 1781500001151
---
# Go Concurrency Patterns

Go's concurrency model is based on goroutines (lightweight threads) and channels (typed communication pipes). The motto: "Do not communicate by sharing memory; instead, share memory by communicating."

## Goroutines

```go
func main() {
    go sayHello()
    go func() {
        fmt.Println("world")
    }()
    time.Sleep(time.Second)
}

func sayHello() {
    fmt.Println("hello")
}
```

## WaitGroup

```go
func main() {
    var wg sync.WaitGroup
    for i := 1; i <= 5; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            fmt.Printf("Worker %d done\n", id)
        }(i)
    }
    wg.Wait()
}
```

## Channels

```go
ch := make(chan int)      // Unbuffered (synchronous)
ch := make(chan int, 10)  // Buffered (async up to 10)

ch <- 42                  // Send
value := <-ch             // Receive
close(ch)                 // Close

for value := range ch {   // Range over channel
    fmt.Println(value)
}
```

### Unbuffered vs Buffered

```go
// Unbuffered: sender blocks until receiver is ready
// Buffered: sender blocks only when buffer full
```

## Select Statement

```go
select {
case msg1 := <-ch1:
    fmt.Println("Received from ch1:", msg1)
case msg2 := <-ch2:
    fmt.Println("Received from ch2:", msg2)
case <-time.After(1 * time.Second):
    fmt.Println("Timeout")
default:
    fmt.Println("No channel ready")
}
```

## Fan-Out Pattern

```go
func fanOut(input <-chan int, numWorkers int) []<-chan int {
    outputs := make([]<-chan int, numWorkers)
    for i := 0; i < numWorkers; i++ {
        outputs[i] = worker(input, i)
    }
    return outputs
}

func worker(input <-chan int, id int) <-chan int {
    output := make(chan int)
    go func() {
        for value := range input {
            output <- value * 2
        }
        close(output)
    }()
    return output
}
```

## Fan-In Pattern

```go
func fanIn(inputs ...<-chan int) <-chan int {
    output := make(chan int)
    var wg sync.WaitGroup
    for _, ch := range inputs {
        wg.Add(1)
        go func(c <-chan int) {
            defer wg.Done()
            for value := range c {
                output <- value
            }
        }(ch)
    }
    go func() {
        wg.Wait()
        close(output)
    }()
    return output
}
```

## Pipeline Pattern

```go
func main() {
    numbers := generate(2, 3, 4, 5)
    squares := square(numbers)
    for result := range squares {
        fmt.Println(result)
    }
}

func generate(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        for _, n := range nums {
            out <- n
        }
        close(out)
    }()
    return out
}

func square(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        for n := range in {
            out <- n * n
        }
        close(out)
    }()
    return out
}
```

## Worker Pool

```go
type Job struct {
    ID      int
    Payload string
}
type Result struct {
    JobID int
    Output string
}

func worker(jobs <-chan Job, results chan<- Result, id int) {
    for job := range jobs {
        results <- Result{JobID: job.ID, Output: fmt.Sprintf("Worker %d processed: %s", id, job.Payload)}
    }
}

func main() {
    const numJobs = 100
    const numWorkers = 10
    jobs := make(chan Job, numJobs)
    results := make(chan Result, numJobs)

    for w := 1; w <= numWorkers; w++ {
        go worker(jobs, results, w)
    }
    for j := 1; j <= numJobs; j++ {
        jobs <- Job{ID: j, Payload: fmt.Sprintf("task_%d", j)}
    }
    close(jobs)

    for a := 1; a <= numJobs; a++ {
        <-results
    }
}
```

## Context for Cancellation

```go
func main() {
    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel()

    result, err := doWork(ctx)
    if err != nil {
        fmt.Println("Work failed:", err)
    }
}

func doWork(ctx context.Context) (string, error) {
    result := make(chan string)
    go func() {
        result <- slowOperation()
    }()
    select {
    case r := <-result:
        return r, nil
    case <-ctx.Done():
        return "", ctx.Err()
    }
}
```

## Mutex (When Channels Don't Fit)

```go
type Counter struct {
    mu    sync.Mutex
    value int
}

func (c *Counter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.value++
}

func (c *Counter) Value() int {
    c.mu.Lock()
    defer c.mu.Unlock()
    return c.value
}
```

**Links**: [[Go Programming]] | [[Concurrency]] | [[Async Python]] | [[Rust Ownership and Borrowing]] | [[Design Patterns]]
