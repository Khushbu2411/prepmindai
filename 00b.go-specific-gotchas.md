# Go-Specific Interview Tips & Gotchas

## Things that trip up Go developers in interviews
> These don't exist in Python/Java — pure Go quirks

---

## 1. := vs = (Most Common Interview Bug)

```go
// := declares NEW variable
// =  assigns to EXISTING variable

// BUG in loops — creates new variable, outer unchanged
queue := []int{1, 2, 3}
for len(queue) > 0 {
    queue := queue[1:]  // ❌ new local queue, outer unchanged
                        // INFINITE LOOP!
}

// FIX
for len(queue) > 0 {
    queue = queue[1:]   // ✅ updates outer queue
}

// This exact bug caused infinite loop in Right Side View problem!
```

---

## 2. Goroutine Closure Captures Variable by Reference

```go
// BUG — all goroutines print 6 (loop finishes first)
for i := 0; i < 5; i++ {
    go func() {
        fmt.Println(i)  // ❌ captures i by reference
    }()
}
// Output: 6 6 6 6 6 (or similar)

// FIX — pass as argument
for i := 0; i < 5; i++ {
    go func(n int) {
        fmt.Println(n)  // ✅ own copy of n
    }(i)
}
// Output: 0 1 2 3 4 (any order)

// Note: Go 1.22+ fixes this automatically
// But interviewers still ask about it!
```

---

## 3. Nil Pointer Dereference

```go
// BUG — common in linked list problems
func reverseList(head *ListNode) *ListNode {
    var prev *ListNode  // nil
    curr := head
    for curr != nil {
        next := curr.Next
        curr.Next = prev
        prev = curr
        curr = next
    }
    return prev
}

// Always check nil before accessing .Next or .Val
if node != nil && node.Next != nil { }

// Common places:
// - Linked list traversal
// - Tree node access
// - Map values that are pointers
```

---

## 4. Slice is a Reference (Not a Copy)

```go
// BUG — both slices share same underlying array
a := []int{1, 2, 3}
b := a              // b points to SAME array
b[0] = 99
fmt.Println(a[0])   // 99! a changed too ❌

// FIX — use copy()
b := make([]int, len(a))
copy(b, a)          // true independent copy
b[0] = 99
fmt.Println(a[0])   // 1 ✅ a unchanged

// Common in:
// - DP problems passing slices
// - Backtracking (must copy path)
// - LRU Cache implementation
```

---

## 5. Append May or May Not Create New Slice

```go
a := make([]int, 3, 5)  // len=3, cap=5
b := a

// Append within capacity — shares array
a = append(a, 4)  // cap not exceeded
a[0] = 99
fmt.Println(b[0]) // 1 (b still points to old slice view)

// Append beyond capacity — new array created
a = make([]int, 5, 5)   // len=5, cap=5 (full)
b = a
a = append(a, 6)        // cap exceeded → new array
a[0] = 99
fmt.Println(b[0])        // 1 (b points to old array)

// Rule: after append, always use the returned slice
a = append(a, val)  // ✅ always reassign
```

---

## 6. Map Keys Must Be Comparable

```go
// ✅ Valid map keys
map[int]int{}
map[string]int{}
map[[26]int][]string{}   // array keys work!
map[bool]int{}

// ❌ Invalid map keys
map[[]int]int{}          // slice — NOT comparable
map[map[int]int]int{}    // map — NOT comparable

// This is why we use [26]int instead of []int as map key
// in anagram problems!
groups := map[[26]int][]string{} // ✅
```

---

## 7. Range Returns Copy of Value

```go
type Point struct{ X, Y int }
points := []Point{{1, 2}, {3, 4}}

// BUG — modifying copy, original unchanged
for _, p := range points {
    p.X = 99  // ❌ p is a copy
}
fmt.Println(points[0].X) // 1, not 99!

// FIX — use index
for i := range points {
    points[i].X = 99  // ✅ modifies original
}

// OR use pointer slice
points2 := []*Point{{1, 2}, {3, 4}}
for _, p := range points2 {
    p.X = 99  // ✅ p is pointer, modifies original
}
```

---

## 8. Integer Division Truncates (Doesn't Round)

```go
7 / 2   // 3 (not 3.5!)
-7 / 2  // -3 (truncates toward zero)

// For rounding:
int(math.Round(float64(a) / float64(b)))

// Mid point in binary search (overflow safe):
mid := left + (right-left)/2   // ✅ safe
mid := (left + right) / 2      // ❌ can overflow int32
```

---

## 9. Channel Direction Types

```go
// Bidirectional (default)
ch := make(chan int)

// Send-only channel
func producer(ch chan<- int) {
    ch <- 42  // ✅ can send
    // <-ch   // ❌ compile error, can't receive
}

// Receive-only channel
func consumer(ch <-chan int) {
    val := <-ch  // ✅ can receive
    // ch <- 42  // ❌ compile error, can't send
}

// Why use direction types?
// → Prevents bugs at compile time
// → Documents intent clearly
// → Common in worker pool patterns
```

---

## 10. Defer Executes LIFO (Last In First Out)

```go
func main() {
    defer fmt.Println("1")
    defer fmt.Println("2")
    defer fmt.Println("3")
}
// Output:
// 3
// 2
// 1

// Defer captures VALUE at time of defer call:
x := 1
defer fmt.Println(x)  // prints 1, not 2
x = 2

// Common use in interviews:
func (l *LRUCache) Get(key int) int {
    l.mu.Lock()
    defer l.mu.Unlock()  // always unlock, even if panic
    // ...
}
```

---

## 11. Type Assertion (Interface to Concrete Type)

```go
var i interface{} = "hello"

// Safe assertion
s, ok := i.(string)
if ok {
    fmt.Println(s)  // "hello"
}

// Unsafe assertion (panics if wrong type)
s := i.(string)  // panics if i is not string

// Type switch
switch v := i.(type) {
case string:
    fmt.Println("string:", v)
case int:
    fmt.Println("int:", v)
default:
    fmt.Println("unknown")
}

// Common in heap implementation:
val := heap.Pop(h).(int)  // interface{} → int
```

---

## 12. Nil Interface vs Nil Pointer

```go
// Tricky! A nil pointer in an interface is NOT nil interface
var p *int = nil
var i interface{} = p

fmt.Println(p == nil)  // true
fmt.Println(i == nil)  // false! ← surprising

// Rule: interface is nil only if BOTH type and value are nil
// Common source of bugs in error handling:
func getError() error {
    var p *MyError = nil
    return p   // ❌ returns non-nil error! (has type info)
}
```

---

## 13. String is Immutable — Use []byte for Mutation

```go
s := "hello"
// s[0] = 'H'  // ❌ compile error, strings are immutable

// FIX
b := []byte(s)
b[0] = 'H'
s = string(b)  // ✅ "Hello"

// For unicode characters, use []rune
r := []rune(s)
r[0] = 'H'
s = string(r)  // ✅ handles multi-byte characters
```

---

## 14. Zero Values (Important for Initialization)

```go
// Go initializes all variables to zero value
var i int        // 0
var f float64    // 0.0
var b bool       // false
var s string     // ""
var p *int       // nil
var sl []int     // nil (not empty!)
var m map[int]int // nil (writing to nil map PANICS!)

// Nil slice vs empty slice:
var s1 []int     // nil, len=0
s2 := []int{}    // empty, len=0, NOT nil

s1 == nil        // true
s2 == nil        // false

// JSON encoding difference:
// nil slice   → null
// empty slice → []

// Always initialize maps before writing:
m := make(map[int]int)  // ✅
m[1] = 1               // ✅

var m map[int]int       // nil map
m[1] = 1               // ❌ PANIC: assignment to nil map
```

---

## 15. Multiple Return Values

```go
// Go idiom — return value + error
func divide(a, b int) (int, error) {
    if b == 0 {
        return 0, fmt.Errorf("division by zero")
    }
    return a / b, nil
}

// Named return values
func minMax(arr []int) (min, max int) {
    min, max = arr[0], arr[0]
    for _, v := range arr[1:] {
        if v < min { min = v }
        if v > max { max = v }
    }
    return  // naked return
}

// Ignore return value
result, _ := divide(10, 2)  // ignore error
_, max := minMax(arr)       // ignore min
```

---

## Quick Gotcha Reference:

| Gotcha | Bug | Fix |
|---|---|---|
| := in loop | New variable, outer unchanged | Use = |
| Closure in goroutine | Captures reference | Pass as argument |
| Slice assignment | Shares underlying array | Use copy() |
| Nil map write | Panic | Initialize with make() |
| String mutation | Compile error | Convert to []byte |
| Range value | Copy, not original | Use index i |
| Integer overflow | Wrong mid in binary search | left + (right-left)/2 |
| Slice as map key | Compile error | Use array [N]T instead |
| Interface nil check | Unexpected non-nil | Check concrete type |
| Defer order | Runs in reverse | Remember LIFO |

---

## One-liner for interview:
"Go's common gotchas: := creates new variables (use = to update),
 goroutine closures capture by reference (pass as args),
 slices share underlying arrays (use copy()), and nil maps panic on write"

## My Notes:
_Add your Go-specific discoveries here_

---
Last updated: July 2026
Real bugs made: := vs = (infinite loop in Right Side View) ✅
               string(rune('a'+n)) type conversion (OmneNEST) ✅
