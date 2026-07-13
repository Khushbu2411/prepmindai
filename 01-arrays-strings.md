# Arrays & Strings — The Foundation of DSA

## Why Arrays First?
```
Arrays are the building block of almost every
other data structure and algorithm:

Sliding Window  → runs ON arrays
Two Pointers    → runs ON arrays
Binary Search   → runs ON sorted arrays
Dynamic Prog    → usually uses arrays
Hash Maps       → values stored like arrays
Trees/Graphs    → represented using arrays

Master arrays → everything else clicks faster
```

---

## What is an Array?
```
Contiguous block of memory
Each element accessed by index in O(1)

arr = [1, 2, 3, 4, 5]
       ↑  ↑  ↑  ↑  ↑
       0  1  2  3  4  ← indices

arr[0] = 1  → O(1)
arr[4] = 5  → O(1)
```

## Array vs String:
```
Array:  [1, 2, 3, 4, 5]     → mutable
String: "hello"              → immutable in most languages
         h e l l o
         0 1 2 3 4

In Go:
arr := []int{1, 2, 3}      // slice (dynamic array)
s := "hello"               // string (immutable)
b := []byte("hello")       // mutable string as bytes
```

---

## Core Operations & Complexity:
```
Access:   arr[i]           → O(1)
Search:   find value       → O(n)
Insert:   at end           → O(1) amortized
          at index         → O(n) shift elements
Delete:   at end           → O(1)
          at index         → O(n) shift elements
```

---

## 5 Core Patterns Built on Arrays:

### Pattern 1: Two Pointers
```
Use: finding pairs, palindromes, container problems
Visual:
[1, 2, 3, 4, 5]
 ↑           ↑
left        right
→ move based on condition
```

### Pattern 2: Sliding Window
```
Use: subarrays, substrings, consecutive elements
Visual:
[1, 2, 3, 4, 5]
[←——→]           fixed or variable window
   [←——→]        slides right
```

### Pattern 3: Prefix Sum
```
Use: range sum queries, subarray sum problems
Visual:
arr    = [1,  2,  3,  4,  5]
prefix = [0,  1,  3,  6,  10, 15]
sum(i,j) = prefix[j+1] - prefix[i]
```

### Pattern 4: Kadane's Algorithm
```
Use: maximum subarray sum
Key: at each position, extend or restart
curr = max(nums[i], curr + nums[i])
```

### Pattern 5: Sorting + Two Pointers
```
Use: 3Sum, grouping, finding pairs
Sort first → enables two pointer approach
```

---

## Must-Know Array Problems:

### Easy (Build foundation):
| # | Problem | Pattern | LeetCode | Asked In |
|---|---|---|---|---|
| 1 | Two Sum | HashMap | https://leetcode.com/problems/two-sum/ | Google, everywhere |
| 121 | Best Time Buy/Sell Stock | Kadane's variant | https://leetcode.com/problems/best-time-to-buy-and-sell-stock/ | Circles ✅ |
| 217 | Contains Duplicate | HashMap | https://leetcode.com/problems/contains-duplicate/ | |
| 53 | Maximum Subarray | Kadane's | https://leetcode.com/problems/maximum-subarray/ | Circles ✅ |
| 283 | Move Zeroes | Two Pointers | https://leetcode.com/problems/move-zeroes/ | |
| 344 | Reverse String | Two Pointers | https://leetcode.com/problems/reverse-string/ | |

### Medium (Interview level):
| # | Problem | Pattern | LeetCode | Asked In |
|---|---|---|---|---|
| 3 | Longest Substring No Repeat | Sliding Window | https://leetcode.com/problems/longest-substring-without-repeating-characters/ | Multiple ✅ |
| 15 | 3Sum | Sort + Two Pointers | https://leetcode.com/problems/3sum/ | |
| 11 | Container With Most Water | Two Pointers | https://leetcode.com/problems/container-with-most-water/ | ✅ solved |
| 560 | Subarray Sum Equals K | Prefix Sum | https://leetcode.com/problems/subarray-sum-equals-k/ | McKinsey level ✅ |
| 438 | Find All Anagrams | Sliding Window | https://leetcode.com/problems/find-all-anagrams-in-a-string/ | ✅ solved |
| 49 | Group Anagrams | HashMap | https://leetcode.com/problems/group-anagrams/ | ✅ solved |
| 238 | Product Except Self | Prefix Sum | https://leetcode.com/problems/product-of-array-except-itself/ | |
| 33 | Search Rotated Array | Binary Search | https://leetcode.com/problems/search-in-rotated-sorted-array/ | |

### Hard (FAANG level):
| # | Problem | Pattern | LeetCode | Asked In |
|---|---|---|---|---|
| 76 | Minimum Window Substring | Sliding Window | https://leetcode.com/problems/minimum-window-substring/ | |
| 4 | Median Two Sorted Arrays | Binary Search | https://leetcode.com/problems/median-of-two-sorted-arrays/ | ONDC ✅ |
| 42 | Trapping Rain Water | Two Pointers/Stack | https://leetcode.com/problems/trapping-rain-water/ | |

---

## Key Algorithms on Arrays:

### 1. Kadane's Algorithm (Maximum Subarray):
```go
// Asked in: Circles interview
func maxSubArray(nums []int) int {
    curr := nums[0]
    maxSum := nums[0]
    for i := 1; i < len(nums); i++ {
        curr = max(nums[i], curr+nums[i])
        maxSum = max(maxSum, curr)
    }
    return maxSum
}
// Time: O(n) | Space: O(1)
// Key: initialize with nums[0] not 0 (handles all-negative)
```

### 2. Prefix Sum:
```go
func buildPrefix(nums []int) []int {
    prefix := make([]int, len(nums)+1)
    for i, v := range nums {
        prefix[i+1] = prefix[i] + v
    }
    return prefix
}
// Sum from index i to j = prefix[j+1] - prefix[i]
```

### 3. Subarray Sum Equals K:
```go
// Asked in: McKinsey prep
func subarraySum(nums []int, k int) int {
    seen := map[int]int{0: 1}
    psum, count := 0, 0
    for _, num := range nums {
        psum += num
        count += seen[psum-k]
        seen[psum]++
    }
    return count
}
// Time: O(n) | Space: O(n)
```

### 4. Best Time to Buy & Sell Stock:
```go
func maxProfit(prices []int) int {
    minPrice := prices[0]
    maxProfit := 0
    for i := 1; i < len(prices); i++ {
        maxProfit = max(maxProfit, prices[i]-minPrice)
        if prices[i] < minPrice {
            minPrice = prices[i]
        }
    }
    return maxProfit
}
// Time: O(n) | Space: O(1)
```

---

## Real Interview Questions:

### Binge Watching (McKinsey):
```go
// Min days to watch all movies, limit = 3 hrs/day
func findMinimumDays(durations []float32) int32 {
    sort.Slice(durations, func(i, j int) bool {
        return durations[i] < durations[j]
    })
    var days int32 = 0
    i := 0
    for i < len(durations) {
        var daySum float32 = 0
        for i < len(durations) && daySum+durations[i] <= 3.0 {
            daySum += durations[i]
            i++
        }
        days++
    }
    return days
}
// Pattern: Greedy + Sorting | Time: O(n log n)
```

### Longest Work Slot (McKinsey):
```go
func findLongestSingleSlot(leaveTimes [][]int32) string {
    var maxDiff int32 = 0
    var employee int32 = 0
    var prevTime int32 = 0
    for i := 0; i < len(leaveTimes); i++ {
        diff := leaveTimes[i][1] - prevTime
        if diff > maxDiff {
            maxDiff = diff
            employee = leaveTimes[i][0]
        }
        prevTime = leaveTimes[i][1]
    }
    return string(rune('a' + employee))
}
// Pattern: Array traversal | Time: O(n)
```

---

## String-Specific Tricks:

```go
// ASCII trick — lowercase letters
idx := s[i] - 'a'    // 'a'=0, 'b'=1, 'z'=25

// Use [26]int instead of map (faster + comparable!)
freq := [26]int{}
freq[s[i]-'a']++
var a, b [26]int
if a == b { }  // ✅ arrays comparable, maps are NOT

// String to bytes (mutation)
b := []byte(s)
b[0] = 'H'
s = string(b)

// int to character
string(rune('a' + n))  // 0→'a', 1→'b', 25→'z'
```

---

## Common Gotchas:
```
❌ maxSum = 0 → fails for all-negative arrays
   ✅ maxSum = nums[0]

❌ i < len vs i <= len → off by one
   ✅ trace through edge cases

❌ map[byte]bool → misses frequency
   ✅ map[byte]int or [26]int

❌ := inside loop → creates new variable
   ✅ = updates outer variable

❌ Not handling empty array
   ✅ if len(nums) == 0 { return }
```

---

## One-liner for interview:
"Arrays give O(1) random access — most optimizations use
 sorting, hashmaps, or prefix sums to avoid nested loops
 and reduce O(n²) brute force to O(n)"

## My Notes:
_Add your personal notes here_

---
Last updated: July 2026
Real interviews: Binge Watching (McKinsey) ✅, Maximum Subarray (Circles) ✅
