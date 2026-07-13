# Golang Concurrency

## Simple Explanation:
Go achieves concurrency through goroutines — lightweight
threads managed by Go runtime, not the OS.
Thousands can run simultaneously using minimal memory (~2KB each)

## Visual — M:N Scheduling:
```
Goroutines (M):          OS Threads (N):      CPU Cores:
G1 ─┐                    
G2 ─┤─→ Thread 1 ──────────────────────────→ Core 1
G3 ─┘                    
                          
G4 ─┐                    
G5 ─┤─→ Thread 2 ──────────────────────────→ Core 2
G6 ─┘                    

Many goroutines → Few threads → Multiple cores
Concurrency within thread, Parallelism across cores
```

## Concurrency vs Parallelism:
```
Concurrency  = dealing with multiple tasks (structure)
               One chef juggling multiple dishes

Parallelism  = doing multiple tasks simultaneously (execution)  
               Multiple chefs each cooking one dish

Go gives you BOTH via goroutines + GOMAXPROCS
```

## 1. Goroutines:
```go
// create goroutine
go func() {
    doWork()
}()

// common gotcha — closure captures variable by reference
for i := 0; i < 5; i++ {
    go func(n int) {  // ✅ pass as argument
        fmt.Println(n)
    }(i)
}
```

## 2. WaitGroup — wait for goroutines:
```go
var wg sync.WaitGroup

for i := 0; i < 5; i++ {
    wg.Add(1)           // increment counter
    go func(n int) {
        defer wg.Done() // decrement when done (always defer!)
        fmt.Println(n)
    }(i)
}

wg.Wait() // block until counter = 0
```

## 3. Channels — goroutine communication:
```go
// unbuffered — sender blocks until receiver ready
ch := make(chan int)
go func() { ch <- 42 }()
val := <-ch  // receives 42

// buffered — sender blocks only when buffer full
ch := make(chan int, 3)
ch <- 1  // doesn't block
ch <- 2  // doesn't block
ch <- 3  // doesn't block
ch <- 4  // BLOCKS — buffer full
```

## 4. Worker Pool (most common interview pattern):
```go
func main() {
    jobs := make(chan int, 5)
    results := make(chan int, 5)
    var wg sync.WaitGroup

    // launch 3 workers
    for i := 1; i <= 3; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            for job := range jobs {  // range closes when channel closed
                results <- job * 2
            }
        }(i)
    }

    // send 5 jobs
    for j := 1; j <= 5; j++ {
        jobs <- j
    }
    close(jobs)  // signal no more jobs → workers exit range loop

    // wait then close results
    go func() {
        wg.Wait()
        close(results)
    }()

    // collect results
    for r := range results {
        fmt.Println(r)
    }
}
```

## 5. Mutex — protect shared data:
```go
type Counter struct {
    mu    sync.Mutex
    value int
}

func (c *Counter) Increment() {
    c.mu.Lock()
    defer c.mu.Unlock()  // always defer!
    c.value++
}
```

## 6. Goroutine Leak:
```go
// LEAK — goroutine blocks forever
func leak() {
    ch := make(chan int)
    go func() {
        val := <-ch  // nobody sends → stuck forever
        fmt.Println(val)
    }()
}

// FIX — context cancellation
func noLeak(ctx context.Context) {
    ch := make(chan int, 1)  // buffered!
    go func() {
        select {
        case ch <- compute():
        case <-ctx.Done():
            return  // exits cleanly
        }
    }()
}
```

## 7. Deadlock:
```go
// DEADLOCK — all goroutines blocked
ch := make(chan int)
ch <- 1   // blocks — nobody receiving
<-ch      // never reached
// fatal error: all goroutines are asleep - deadlock!

// FIX
go func() { ch <- 1 }()  // send in goroutine
val := <-ch               // receive in main
```

## Goroutine Leak vs Deadlock:
```
Goroutine Leak:
→ ONE goroutine stuck
→ Program keeps running
→ Silent — Go doesn't detect
→ Memory grows slowly

Deadlock:
→ ALL goroutines stuck
→ Program freezes
→ Loud — Go detects immediately
→ fatal error message
```

## Common Gotchas:
```
❌ Closure captures loop variable → pass as argument
❌ Forget close(jobs) → workers block forever (leak)
❌ close(results) before wg.Wait() → panic
❌ Forget defer mu.Unlock() → deadlock
✅ Always defer wg.Done() at TOP of goroutine function
✅ Buffered channel in goroutine leak fix → goroutine exits cleanly
✅ GOMAXPROCS = NumCPU() by default → parallelism automatic
```

## One-liner for interview:
"Goroutines are lightweight (~2KB) Go-runtime-managed threads,
 coordinated via channels for communication and WaitGroups for
 synchronization. M:N scheduling maps many goroutines to few OS
 threads, achieving both concurrency and parallelism."

## My Notes:
_Add your own notes here_

---
Last updated: July 2026
Asked in: McKinsey SE2, Pocketful, Kyndryl interviews
