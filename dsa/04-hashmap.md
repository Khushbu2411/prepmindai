# HashMap / HashSet Pattern

## Simple Explanation:
```
HashMap = key → value store
HashSet = just keys (no values)

Both give O(1) average for:
→ Insert
→ Delete  
→ Lookup

Trade-off: O(n) extra space
But eliminates nested loops → O(n²) becomes O(n)
```

---

## Visual:
```
Array: [2, 7, 11, 15], target = 9

Without HashMap (O(n²)):
Check every pair:
2+7=9 ✅ found!
2+11=13 ❌
2+15=17 ❌
...

With HashMap (O(n)):
i=0: seen={}, need 9-2=7, not in seen → add {2:0}
i=1: seen={2:0}, need 9-7=2, FOUND at index 0!
→ return [0,1] ✅

HashMap remembered what we saw → no need to look back!
```

---

## When to use HashMap:
```
✅ "Find if X exists" → O(1) lookup
✅ "Count frequency of elements"
✅ "Find pairs that sum to target"
✅ "Group elements by property"
✅ "Find duplicates"
✅ "Cache/memoize results"
✅ Anywhere you're doing O(n) search repeatedly
```

---

## Go HashMap Syntax:
```go
// Create
m := make(map[string]int)
m := map[string]int{}
m := map[string]int{"a": 1, "b": 2}

// Insert/Update
m["key"] = value

// Lookup (safe)
val, ok := m["key"]
if ok {
    // key exists, val has the value
}

// Lookup (unsafe - returns zero value if not found)
val := m["key"]  // returns 0 if key doesn't exist

// Delete
delete(m, "key")

// Check existence
if _, ok := m["key"]; ok {
    // exists
}

// Iterate
for key, val := range m {
    fmt.Println(key, val)
}

// Length
len(m)
```

## Go HashSet (using map[T]bool or map[T]struct{}):
```go
// Using map[int]bool (simpler)
seen := map[int]bool{}
seen[5] = true
if seen[5] { } // exists

// Using map[int]struct{} (memory efficient)
seen := map[int]struct{}{}
seen[5] = struct{}{}
if _, ok := seen[5]; ok { } // exists
```

---

## Core Patterns:

### Pattern 1: Frequency Count
```go
// Count occurrences of each element
freq := map[int]int{}
for _, v := range nums {
    freq[v]++
}

// Check if element appears k times
if freq[target] == k { }
```

### Pattern 2: Two Sum (complement lookup)
```go
// For each element, check if complement exists
seen := map[int]int{}  // value → index
for i, v := range nums {
    complement := target - v
    if idx, ok := seen[complement]; ok {
        return []int{idx, i}
    }
    seen[v] = i
}
```

### Pattern 3: Grouping
```go
// Group elements by a key
groups := map[string][]string{}
for _, word := range words {
    key := sortString(word)  // sorted word as key
    groups[key] = append(groups[key], word)
}
```

### Pattern 4: Sliding Window + HashMap
```go
// Track character frequency in window
window := map[byte]int{}
for right := 0; right < len(s); right++ {
    window[s[right]]++
    // shrink window if needed
    for window[s[right]] > 1 {
        window[s[left]]--
        left++
    }
}
```

### Pattern 5: Prefix Sum + HashMap
```go
// Count subarrays with sum = k
seen := map[int]int{0: 1}  // prefix_sum → count
psum := 0
for _, num := range nums {
    psum += num
    count += seen[psum-k]  // how many times seen psum-k
    seen[psum]++
}
```

---

## Must-Know HashMap Problems:

### Easy:
| # | Problem | Pattern | LeetCode | Asked In |
|---|---|---|---|---|
| 1 | Two Sum | Complement lookup | https://leetcode.com/problems/two-sum/ | Everywhere ✅ |
| 217 | Contains Duplicate | HashSet | https://leetcode.com/problems/contains-duplicate/ | Day 1 ✅ |
| 242 | Valid Anagram | Frequency count | https://leetcode.com/problems/valid-anagram/ | ✅ solved |
| 383 | Ransom Note | Frequency count | https://leetcode.com/problems/ransom-note/ | |
| 1 | Two Sum | HashMap | https://leetcode.com/problems/two-sum/ | |

### Medium:
| # | Problem | Pattern | LeetCode | Asked In |
|---|---|---|---|---|
| 49 | Group Anagrams | Group by sorted key | https://leetcode.com/problems/group-anagrams/ | ✅ solved |
| 438 | Find All Anagrams | Sliding window + [26]int | https://leetcode.com/problems/find-all-anagrams-in-a-string/ | ✅ solved |
| 560 | Subarray Sum Equals K | Prefix sum + HashMap | https://leetcode.com/problems/subarray-sum-equals-k/ | ✅ solved |
| 3 | Longest Substring No Repeat | Sliding window + HashMap | https://leetcode.com/problems/longest-substring-without-repeating-characters/ | ✅ solved |
| 347 | Top K Frequent Elements | Frequency + Heap | https://leetcode.com/problems/top-k-frequent-elements/ | |
| 146 | LRU Cache | HashMap + DLL | https://leetcode.com/problems/lru-cache/ | OmneNEST ✅ |

### Hard:
| # | Problem | Pattern | LeetCode | Asked In |
|---|---|---|---|---|
| 76 | Minimum Window Substring | Sliding window + HashMap | https://leetcode.com/problems/minimum-window-substring/ | |
| 295 | Find Median Data Stream | Two heaps | https://leetcode.com/problems/find-median-from-data-stream/ | OmneNEST ✅ |

---

## Key Implementations:

### Two Sum — O(n):
```go
func twoSum(nums []int, target int) []int {
    seen := map[int]int{} // value → index
    for i, v := range nums {
        if idx, ok := seen[target-v]; ok {
            return []int{idx, i}
        }
        seen[v] = i
    }
    return []int{}
}
// Time: O(n) | Space: O(n)
// Key: store complement BEFORE current element
```

### Group Anagrams — O(n*k):
```go
func groupAnagrams(strs []string) [][]string {
    groups := map[[26]int][]string{}
    for _, word := range strs {
        freq := [26]int{}
        for i := 0; i < len(word); i++ {
            freq[word[i]-'a']++
        }
        groups[freq] = append(groups[freq], word)
    }
    result := [][]string{}
    for _, v := range groups {
        result = append(result, v)
    }
    return result
}
// Key trick: [26]int as map key (arrays comparable, slices not!)
```

### Contains Duplicate — O(n):
```go
func containsDuplicate(nums []int) bool {
    seen := map[int]bool{}
    for _, num := range nums {
        if seen[num] {
            return true
        }
        seen[num] = true
    }
    return false
}
```

### Valid Anagram — O(n):
```go
func isAnagram(s string, t string) bool {
    if len(s) != len(t) {
        return false
    }
    freq := [26]int{}
    for i := 0; i < len(s); i++ {
        freq[s[i]-'a']++
        freq[t[i]-'a']--
    }
    return freq == [26]int{}
}
// One pass! Increment for s, decrement for t
// If anagram → all zeros at end
```

---

## HashMap Internals (for interviews):
```
How HashMap works:
1. key → hash function → bucket index
2. Store (key, value) in bucket
3. Collision → chaining or open addressing

Why O(1) average?
→ Hash function distributes keys evenly
→ Most buckets have 0-1 elements
→ Worst case O(n) if all keys hash to same bucket

Why use [26]int instead of map for string problems?
→ Fixed size array → no hash collisions
→ Directly comparable with ==
→ Faster than map operations
→ O(1) space (only 26 elements)
```

---

## Common Gotchas:
```
❌ map[byte]bool → can't detect frequency > 1
   ✅ map[byte]int

❌ Comparing maps with == → doesn't work
   ✅ Use [26]int array instead (comparable!)

❌ Accessing missing key panics? NO!
   → Go returns zero value for missing keys
   → int: 0, bool: false, string: ""
   → But always check with _, ok := m[key]

❌ Modifying map while ranging over it
   ✅ Collect keys first, then modify

❌ Nil map → panic on write
   ✅ Always initialize: make(map[K]V)

❌ forgot {0:1} initialization in prefix sum
   ✅ seen := map[int]int{0: 1}
      (handles subarray starting from index 0)
```

---

## [26]int Trick — Remember This Forever:
```go
// For lowercase letter frequency problems:
// Use [26]int instead of map[byte]int

freq := [26]int{}
for _, c := range s {
    freq[c-'a']++
}

// WHY THIS IS BETTER:
// 1. Arrays comparable: freq1 == freq2 ✅
// 2. Maps NOT comparable: map1 == map2 ❌ compile error
// 3. Fixed O(1) space vs O(n) for map
// 4. Faster — no hash function overhead

// USED IN:
// - Valid Anagram ✅
// - Find All Anagrams ✅
// - Group Anagrams ✅
// - Permutation in String
```

---

## One-liner for interview:
"HashMap trades O(n) space for O(1) lookup time —
 replacing repeated O(n) searches with single O(1) checks,
 turning O(n²) brute force into O(n) solutions"

## My Notes:
_Add your personal notes here_

---
Last updated: July 2026
Real interviews: Two Sum (everywhere) ✅, LRU Cache (OmneNEST) ✅,
Group Anagrams ✅, Subarray Sum (McKinsey prep) ✅
