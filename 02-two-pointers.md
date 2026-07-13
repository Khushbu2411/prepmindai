# Two Pointers Pattern

## Simple Explanation:
Instead of checking every pair with nested loops (O(n²)),
use two pointers — one at each end — moving toward each other.
Each step eliminates impossible combinations → O(n)

## Visual:
```
arr = [1, 2, 3, 4, 5]
       ↑           ↑
      left        right
      
→ compare arr[left] + arr[right] with target
→ too small? left++
→ too big?   right--
→ equal?     found!
```

## When to use:
```
✅ Sorted array → find pairs/triplets
✅ Compare elements from both ends
✅ Palindrome check
✅ Remove duplicates
✅ Container/area problems
```

## Template (Go):
```go
left, right := 0, len(arr)-1
for left < right {
    if condition_met {
        // found answer
    } else if need_bigger {
        left++
    } else {
        right--
    }
}
```

## Problems:
| # | Problem | Difficulty | Key trick |
|---|---|---|---|
| 167 | Two Sum II | Medium | Sorted → two pointers from ends |
| 125 | Valid Palindrome | Easy | Skip non-alphanumeric, compare |
| 11 | Container With Most Water | Medium | Move shorter height inward |
| 15 | 3Sum | Medium | Sort + fix one + two pointers |
| 42 | Trapping Rain Water | Hard | Track max from both sides |

## Hint (read before giving up, not before trying):
```
"Can I use one pointer from each end
 moving inward based on a comparison?
 What condition makes me move left?
 What condition makes me move right?"
```

## Code — Valid Palindrome:
```go
func isPalindrome(s string) bool {
    left, right := 0, len(s)-1
    for left < right {
        for left < right && !isAlphanumeric(s[left]) {
            left++
        }
        for left < right && !isAlphanumeric(s[right]) {
            right--
        }
        if toLower(s[left]) != toLower(s[right]) {
            return false
        }
        left++
        right--
    }
    return true
}
```

## Code — Container With Most Water:
```go
func maxArea(height []int) int {
    left, right := 0, len(height)-1
    maxWater := 0
    for left < right {
        water := (right-left) * min(height[left], height[right])
        maxWater = max(maxWater, water)
        if height[left] < height[right] {
            left++
        } else {
            right--
        }
    }
    return maxWater
}
```

## Common Gotchas:
```
❌ Forgetting left < right check → pointers cross
❌ Moving wrong pointer → wrong result
❌ Not handling duplicates in 3Sum
✅ Always move the SMALLER side inward for area problems
✅ For palindrome — check bounds before accessing
```

## One-liner for interview:
"Two pointers work when sorted input lets us eliminate
 impossible pairs by moving the smaller pointer right
 or larger pointer left — turning O(n²) into O(n)"

## My Notes:
_Add your own notes here as you learn_
```

---
Last updated: July 2026
Solved in real interviews: Valid Palindrome ✅, Two Sum II ✅, Container With Most Water ✅
