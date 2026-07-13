# Go Syntax Cheat Sheet — Interview Problem Solving

## The Most Useful Reference for Coding Interviews in Go
> "I know the algorithm but forget the syntax" — solved here forever

---

## 1. String ↔ Integer Conversions

```go
import "strconv"

// String to Integer
n, err := strconv.Atoi("123")     // "123" → 123
n, err := strconv.ParseInt("123", 10, 64)  // base 10, 64-bit

// Integer to String
s := strconv.Itoa(123)            // 123 → "123"
s := fmt.Sprintf("%d", 123)       // 123 → "123"

// String to Float
f, err := strconv.ParseFloat("3.14", 64)  // "3.14" → 3.14

// Float to String
s := fmt.Sprintf("%.2f", 3.14)   // "3.14"
s := strconv.FormatFloat(3.14, 'f', 2, 64)

// Quick conversion without error check (use in interviews)
n, _ := strconv.Atoi("123")      // ignore error
```

---

## 2. Character ↔ Integer Conversions

```go
// Character to ASCII value
var c byte = 'A'
ascii := int(c)          // 'A' → 65
ascii := int('A')        // 65

// ASCII to Character
char := byte(65)         // 65 → 'A'
char := rune(65)         // 65 → 'A' (unicode)

// Character to index (a=0, b=1, ... z=25)
idx := 'a' - 'a'        // 0
idx := 'z' - 'a'        // 25
idx := s[i] - 'a'       // for any lowercase letter

// Index to character (0→'a', 25→'z')
char := string(rune('a' + 0))   // "a"
char := string(rune('a' + 25))  // "z"
char := string(rune('a' + n))   // n must be int32

// Common mistake fix:
var n int32 = 2
char := string(rune('a' + n))   // ✅ works
// char := string('a' + n)      // ❌ type error

// Uppercase check
if s[i] >= 'A' && s[i] <= 'Z' { }

// Lowercase check  
if s[i] >= 'a' && s[i] <= 'z' { }

// Is digit check
if s[i] >= '0' && s[i] <= '9' { }

// Convert uppercase to lowercase (ASCII trick)
lower := s[i] + 32   // 'A'(65) + 32 = 'a'(97)
upper := s[i] - 32   // 'a'(97) - 32 = 'A'(65)

// Using unicode package
import "unicode"
unicode.IsLetter(ch)    // true for a-z, A-Z
unicode.IsDigit(ch)     // true for 0-9
unicode.ToLower(ch)     // 'A' → 'a'
unicode.ToUpper(ch)     // 'a' → 'A'
unicode.IsSpace(ch)     // true for spaces, tabs
```

---

## 3. String Operations

```go
import "strings"

s := "hello world"

// Length
len(s)                          // 11

// Access character
s[0]                            // 'h' (byte)
string(s[0])                    // "h" (string)

// Substring
s[0:5]                          // "hello"
s[6:]                           // "world"
s[:5]                           // "hello"

// Split
parts := strings.Split("a,b,c", ",")    // ["a","b","c"]
parts := strings.Fields("a b c")         // ["a","b","c"] splits on whitespace

// Join
strings.Join([]string{"a","b","c"}, ",") // "a,b,c"

// Contains
strings.Contains("hello", "ell")    // true

// Index
strings.Index("hello", "ll")        // 2, -1 if not found

// Replace
strings.Replace("hello", "l", "r", -1)  // "herro" (-1 = all)
strings.ReplaceAll("hello", "l", "r")   // "herro"

// Trim
strings.TrimSpace("  hello  ")          // "hello"
strings.Trim("##hello##", "#")          // "hello"
strings.TrimLeft("##hello", "#")        // "hello"
strings.TrimRight("hello##", "#")       // "hello"

// Case
strings.ToLower("HELLO")    // "hello"
strings.ToUpper("hello")    // "HELLO"

// HasPrefix/HasSuffix
strings.HasPrefix("hello", "he")   // true
strings.HasSuffix("hello", "lo")   // true

// Count occurrences
strings.Count("hello", "l")        // 2

// Repeat
strings.Repeat("ab", 3)            // "ababab"

// String to []byte (for mutation)
b := []byte("hello")
b[0] = 'H'
s = string(b)               // "Hello"

// String to []rune (for unicode)
r := []rune("hello")
r[0] = 'H'
s = string(r)               // "Hello"

// Iterate string
for i, ch := range s {     // ch is rune
    fmt.Println(i, ch)
}
for i := 0; i < len(s); i++ {  // s[i] is byte
    fmt.Println(s[i])
}

// Build string efficiently
var sb strings.Builder
sb.WriteString("hello")
sb.WriteByte(' ')
sb.WriteRune('w')
result := sb.String()       // "hello w"
```

---

## 4. Integer Conversions

```go
// int ↔ int32 ↔ int64
var n int = 42
var n32 int32 = int32(n)    // int → int32
var n64 int64 = int64(n)    // int → int64
var back int = int(n32)     // int32 → int

// int ↔ float
var f float64 = float64(n)  // int → float64
var i int = int(f)          // float64 → int (truncates)

// Absolute value (no built-in for int!)
func abs(x int) int {
    if x < 0 {
        return -x
    }
    return x
}

// Max/Min (Go 1.21+ has built-in)
max(a, b)    // built-in Go 1.21+
min(a, b)    // built-in Go 1.21+

// For older Go:
func max(a, b int) int {
    if a > b { return a }
    return b
}

// Integer overflow check
import "math"
math.MaxInt32   // 2147483647
math.MinInt32   // -2147483648
math.MaxInt64   // 9223372036854775807
```

---

## 5. Array/Slice Operations

```go
// Create
arr := []int{1, 2, 3}
arr := make([]int, 5)           // [0,0,0,0,0]
arr := make([]int, 5, 10)       // len=5, cap=10

// 2D array
matrix := [][]int{{1,2},{3,4}}
matrix := make([][]int, rows)
for i := range matrix {
    matrix[i] = make([]int, cols)
}

// Append
arr = append(arr, 4)            // add to end
arr = append(arr, 4, 5, 6)     // add multiple
arr = append(arr, arr2...)      // merge two slices

// Copy
dst := make([]int, len(src))
copy(dst, src)                  // true copy (not reference)

// Slice operations
arr[1:3]                        // [arr[1], arr[2]]
arr[:3]                         // first 3 elements
arr[2:]                         // from index 2 to end

// Remove element at index i
arr = append(arr[:i], arr[i+1:]...)

// Insert at index i
arr = append(arr[:i], append([]int{val}, arr[i:]...)...)

// Reverse
for i, j := 0, len(arr)-1; i < j; i, j = i+1, j-1 {
    arr[i], arr[j] = arr[j], arr[i]
}

// Contains (no built-in)
func contains(arr []int, val int) bool {
    for _, v := range arr {
        if v == val { return true }
    }
    return false
}

// Sort
import "sort"
sort.Ints(arr)                          // ascending
sort.Sort(sort.Reverse(sort.IntSlice(arr))) // descending
sort.Slice(arr, func(i, j int) bool {
    return arr[i] < arr[j]             // custom sort
})

// Sort strings
sort.Strings(strs)

// Sort by multiple criteria
sort.Slice(people, func(i, j int) bool {
    if people[i].Age != people[j].Age {
        return people[i].Age < people[j].Age
    }
    return people[i].Name < people[j].Name
})

// Binary search (on sorted array)
idx := sort.SearchInts(arr, target)    // returns insertion point
```

---

## 6. Map Operations

```go
// Create
m := make(map[string]int)
m := map[string]int{"a": 1}

// Safe lookup
val, ok := m["key"]
if ok { /* exists */ }

// Default value if not found
val := m["missing"]    // returns 0 for int, "" for string

// Delete
delete(m, "key")

// Iterate (random order!)
for k, v := range m { }

// Map of slices
m := map[string][]int{}
m["key"] = append(m["key"], value)

// Frequency count pattern
freq := map[int]int{}
for _, v := range arr {
    freq[v]++
}

// Check if key exists
if _, ok := m[key]; ok { }
```

---

## 7. Math Operations

```go
import "math"

math.Abs(-3.14)         // 3.14 (float only!)
math.Sqrt(16.0)         // 4.0
math.Pow(2, 10)         // 1024.0
math.Log(math.E)        // 1.0
math.Log2(8)            // 3.0
math.Floor(3.7)         // 3.0
math.Ceil(3.2)          // 4.0
math.Round(3.5)         // 4.0
math.Inf(1)             // +Infinity
math.IsInf(x, 1)        // check if +infinity

// Integer math
a / b                   // integer division (truncates)
a % b                   // remainder
a & b                   // AND
a | b                   // OR
a ^ b                   // XOR
a << 1                  // left shift (multiply by 2)
a >> 1                  // right shift (divide by 2)

// Check even/odd
n % 2 == 0              // even
n & 1 == 0              // even (faster)
n & 1 == 1              // odd
```

---

## 8. Type Conversions Quick Reference

```go
// All common conversions in one place:

int    → string:  strconv.Itoa(n)  OR  fmt.Sprintf("%d", n)
string → int:     n, _ := strconv.Atoi(s)
int    → float64: float64(n)
float64 → int:    int(f)          // truncates decimal
byte   → string:  string(b)
string → []byte:  []byte(s)
rune   → string:  string(r)
string → []rune:  []rune(s)
int32  → string:  string(rune(n)) // for character conversion
int    → int32:   int32(n)
int32  → int:     int(n)
int    → int64:   int64(n)

// Character conversions:
'a' + 0  = 'a' (97)
'a' + 25 = 'z' (122)
'A' + 0  = 'A' (65)
'0' + 0  = '0' (48)
'0' + 9  = '9' (57)

// 0-25 to a-z:
string(rune('a' + n))   // n is int/int32

// a-z to 0-25:
int(s[i] - 'a')         // s[i] is byte
```

---

## 9. Heap / Priority Queue in Go

```go
import "container/heap"

// Min Heap
type MinHeap []int

func (h MinHeap) Len() int           { return len(h) }
func (h MinHeap) Less(i, j int) bool { return h[i] < h[j] }
func (h MinHeap) Swap(i, j int)      { h[i], h[j] = h[j], h[i] }
func (h *MinHeap) Push(x interface{}) {
    *h = append(*h, x.(int))
}
func (h *MinHeap) Pop() interface{} {
    old := *h
    n := len(old)
    x := old[n-1]
    *h = old[:n-1]
    return x
}

// Usage:
h := &MinHeap{3, 1, 4, 1, 5}
heap.Init(h)
heap.Push(h, 2)
min := heap.Pop(h).(int)

// Max Heap — just flip Less:
func (h MaxHeap) Less(i, j int) bool { return h[i] > h[j] }
```

---

## 10. Common Interview Patterns — Quick Reference

```go
// Swap two variables
a, b = b, a

// Check if number is power of 2
n & (n-1) == 0

// Count set bits
bits.OnesCount(uint(n))

// Reverse integer digits
for n > 0 {
    digit := n % 10
    result = result*10 + digit
    n /= 10
}

// Check palindrome number
// (convert to string, then check)

// GCD
func gcd(a, b int) int {
    for b != 0 {
        a, b = b, a%b
    }
    return a
}

// LCM
func lcm(a, b int) int {
    return a / gcd(a, b) * b
}

// Generate all subsets (bit manipulation)
for mask := 0; mask < (1 << n); mask++ {
    subset := []int{}
    for i := 0; i < n; i++ {
        if mask & (1 << i) != 0 {
            subset = append(subset, nums[i])
        }
    }
}
```

---

## 11. Stack & Queue using Slices

```go
// Stack (LIFO)
stack := []int{}
stack = append(stack, val)       // push
top := stack[len(stack)-1]       // peek
stack = stack[:len(stack)-1]     // pop
len(stack) == 0                  // isEmpty

// Queue (FIFO)
queue := []int{}
queue = append(queue, val)       // enqueue
front := queue[0]                // peek
queue = queue[1:]                // dequeue
len(queue) == 0                  // isEmpty
```

---

## 12. Infinity Values

```go
// For DP/graph problems needing infinity
const INF = int(1e18)           // large number
const INF = math.MaxInt64       // max int64
const NEG_INF = math.MinInt64   // min int64

// Float infinity
posInf := math.Inf(1)           // +∞
negInf := math.Inf(-1)          // -∞
```

---

## 13. String Builder (efficient string concatenation)

```go
// WRONG — O(n²) due to string immutability
result := ""
for _, s := range words {
    result += s   // creates new string each time!
}

// RIGHT — O(n) with strings.Builder
var sb strings.Builder
for _, s := range words {
    sb.WriteString(s)
}
result := sb.String()
```

---

## 14. Sorting Strings (for anagram problems)

```go
import "sort"

func sortString(s string) string {
    b := []byte(s)
    sort.Slice(b, func(i, j int) bool {
        return b[i] < b[j]
    })
    return string(b)
}

// Usage:
sortString("cab")   // "abc"
sortString("eat")   // "aet"
```

---

## Quick Lookup Table:

| Need | Use |
|---|---|
| String → Int | `strconv.Atoi(s)` |
| Int → String | `strconv.Itoa(n)` |
| Char → Index | `s[i] - 'a'` |
| Index → Char | `string(rune('a' + n))` |
| Sort ints | `sort.Ints(arr)` |
| Sort strings | `sort.Strings(arr)` |
| Custom sort | `sort.Slice(arr, func)` |
| Abs value | custom `abs()` function |
| Max/Min | `max(a,b)` / `min(a,b)` |
| String split | `strings.Split(s, sep)` |
| String join | `strings.Join(arr, sep)` |
| String trim | `strings.TrimSpace(s)` |
| Build string | `strings.Builder` |
| Stack | `[]int{}` + append/slice |
| Queue | `[]int{}` + append/slice[1:] |
| Min heap | `container/heap` |
| Infinity | `int(1e18)` or `math.MaxInt64` |

---

## Common Mistakes to Avoid:

```go
❌ string(65)           // "A" — works but confusing
✅ string(rune(65))     // "A" — explicit

❌ result += s          // O(n²) string concat
✅ strings.Builder      // O(n)

❌ m["key"]             // might be zero value, not missing
✅ val, ok := m["key"]  // explicit check

❌ sort.Ints([]int32{}) // type mismatch
✅ convert to []int first

❌ int32 + 'a'          // type mismatch sometimes
✅ rune('a' + n)        // explicit rune conversion

❌ var n int = math.Inf // Inf is float, not int
✅ const INF = int(1e18)
```

---

## One-liner for interview:
"In Go, always use strconv for string/int conversion,
 strings.Builder for concatenation, [26]int for character
 frequency, and explicit type casting for all conversions"

## My Notes:
_Add your personal syntax tricks here_

---
Last updated: July 2026
Most useful: char↔int conversions, [26]int trick, sort.Slice
Real mistake made: string(rune('a'+n)) vs string('a'+n) (OmneNEST) ✅
