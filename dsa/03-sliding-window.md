# Sliding Window Pattern

## Simple Explanation:
Instead of recalculating the entire subarray each time,
maintain a "window" that slides right — adding new elements
and removing old ones → O(n) instead of O(n²)

## Visual:
```
Fixed window (size k=3):
[1, 2, 3, 4, 5]
[←——→]              sum=6
   [←——→]           sum=9 (add 4, remove 1)
      [←——→]        sum=12 (add 5, remove 2)

Variable window:
[a, b, c, a, b, c, b, b]
[←→]                    expand right
[←——→]                  expand right
    [←→]                shrink left (duplicate found)
```

## Two types:
```
Fixed window:   size is given (k elements)
Variable window: size changes based on condition
                 (longest/shortest subarray meeting condition)
```

## Template — Fixed Window (Go):
```go
// build first window
windowSum := 0
for i := 0; i < k; i++ {
    windowSum += arr[i]
}
maxSum := windowSum

// slide window
for i := k; i < len(arr); i++ {
    windowSum += arr[i]        // add new right
    windowSum -= arr[i-k]      // remove old left
    maxSum = max(maxSum, windowSum)
}
```

## Template — Variable Window (Go):
```go
seen := map[byte]int{}
left := 0
longest := 0

for right := 0; right < len(s); right++ {
    // add s[right] to window
    seen[s[right]]++
    
    // shrink window while invalid
    for window_is_invalid {
        seen[s[left]]--
        left++
    }
    
    // update answer
    longest = max(longest, right-left+1)
}
```

## Problems:
| # | Problem | Difficulty | Key trick |
|---|---|---|---|
| 3 | Longest Substring No Repeat | Medium | Variable window + hashmap |
| 438 | Find All Anagrams | Medium | Fixed window + [26]int array |
| 76 | Minimum Window Substring | Hard | Variable window + need map |
| 567 | Permutation in String | Medium | Fixed window + frequency |
| 121 | Best Time to Buy Stock | Easy | Track min price so far |

## Code — Longest Substring (most asked):
```go
func lengthOfLongestSubstring(s string) int {
    seen := map[byte]int{}
    left, longest := 0, 0
    for i := 0; i < len(s); i++ {
        if idx, ok := seen[s[i]]; ok && idx >= left {
            left = idx + 1  // move past duplicate
        }
        seen[s[i]] = i
        if i-left+1 > longest {
            longest = i - left + 1
        }
    }
    return longest
}
```

## Code — Find All Anagrams (O(1) space trick):
```go
func findAnagrams(s string, p string) []int {
    if len(s) < len(p) {
        return []int{}
    }
    pFreq := [26]int{}
    wFreq := [26]int{}
    for i := 0; i < len(p); i++ {
        pFreq[p[i]-'a']++
        wFreq[s[i]-'a']++
    }
    result := []int{}
    for i := len(p); i < len(s); i++ {
        if pFreq == wFreq {
            result = append(result, i-len(p))
        }
        wFreq[s[i]-'a']++
        wFreq[s[i-len(p)]-'a']--
    }
    if pFreq == wFreq {
        result = append(result, len(s)-len(p))
    }
    return result
}
```

## Common Gotchas:
```
❌ Using map[byte]bool instead of map[byte]int → misses duplicates
❌ Not checking idx >= left → old positions count as duplicates
❌ For anagrams: comparing maps directly → use [26]int array instead
✅ [26]int arrays can be compared with == directly in Go
✅ Initialize seen = {0:1} for prefix sum problems
```

## One-liner for interview:
"Sliding window maintains a subarray between left and right pointers —
 expand right each step, shrink left when window becomes invalid —
 turning O(n²) recalculation into O(n) by reusing previous work"

## My Notes:
_Add your own notes here_

---
Last updated: July 2026
Solved in real interviews: Longest Substring ✅, Find All Anagrams ✅
