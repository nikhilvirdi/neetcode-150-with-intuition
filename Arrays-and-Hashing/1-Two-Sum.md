<h1 style="border-bottom: 3px solid #4CAF50; padding-bottom: 6px;">
  Two Sum
</h1>

**Difficulty:** <span style="color:green"><b>Easy</b></span>  
**Problem Link:** [Two Sum](https://leetcode.com/problems/two-sum/description/)  
**Tags:** `Array` `Hashing` 

---

# Problem Overview

## Restating the Problem 

You are given an array of integers and a target value.

Your task is to determine whether **there exist two distinct indices** such that the **sum of their values equals the target**.

You are not asked to:

- sort the array
- count combinations
- find all pairs

You are asked to detect **existence of a valid pair**.

## Why the Problem Is Deceptive

At surface level, this looks like an arithmetic problem.

In reality, it is a **constraint satisfaction problem under pair interaction**.

The deception lies in:

- Thinking in terms of “pairs” instead of **relationships**
- Treating this as a search problem rather than an **invariant-checking problem**
- Focusing on indices too early instead of values

## What the Problem Is Really About

Conceptually, the problem asks:

> Does there exist a pair (a, b) in the array such that
> 
> 
> `a + b = target`?
> 

This can be reframed as:

> For some value x, does its complement (target − x) exist elsewhere in the array?
> 

This reframing is the key that unlocks the problem.

---

# Problem Insights

## The Mental Model to Activate

This problem is about **complement existence**, not pair enumeration.

Instead of thinking:

> “Which two numbers add up to target?”
> 

Think:

> “For each number, is the number it needs already present?”
> 

This activates a **lookup-based mental model**, not a comparison-based one.

## Misleading Aspects of the Problem

- The phrase “two sum” pushes the mind toward pairwise checking
- The array format suggests positional reasoning
- The temptation is to check every possible pair

But none of these are necessary.

## Conceptual Reduction

The equation:

```
a + b = target
```

can be rewritten as:

```
b = target − a
```

This turns the problem into:

> For each element a, check whether its required complement has appeared before (or will appear later).
> 

This is no longer a pair problem.

It is a **membership problem under a running invariant**.

## The Core Invariant

At any point while scanning the array:

> All previously seen values are available as potential complements.
> 

If the current value’s complement exists among them, the invariant is satisfied and the problem is solved.

## Reasoning-Oriented Example

For `nums = [2, 7, 11, 15]`, `target = 9`:

- First value: `2` → needs `7` (not seen yet)
- Second value: `7` → needs `2` (already seen)

The invariant breaks **positively** → a valid pair exists.

No further scanning is required.

---

# Brute Force Solution

## Core Idea

Check every possible pair of indices.

- Fix one index
- Try all other indices to see if the sum matches the target

This mirrors the most literal interpretation of the problem.

## Pseudocode

```
for i from 0 to n - 1:
    for j from i + 1 to n - 1:
        if nums[i] + nums[j] == target:
            return true
return false
```

## Why This Works

- Every possible pair is examined
- If a valid pair exists, it will be found

This establishes **correctness in principle**.

## Why This Is Only an Upper Bound

- Every new element is compared with many old ones
- Previous comparisons give no reusable information
- Work grows quadratically with input size

The invariant is checked **explicitly and repeatedly**, which is wasteful.

---

# Better Solution

## Representation Shift: Ordering the Data

Instead of checking arbitrary pairs, we can **restructure the data**.

A common shift is:

- Sort the array
- Use two pointers to move inward based on sum comparison

This changes the way we enforce the invariant.

## Why Sorting Helps Conceptually

Sorting introduces **monotonicity**:

- If the sum is too small → move left pointer right
- If the sum is too large → move right pointer left

The invariant becomes:

> All sums to the left are smaller; all sums to the right are larger.
> 

## Pseudocode

```
sort the array
left = 0, right = n - 1

while left < right:
    sum = nums[left] + nums[right]
    if sum == target:
        return true
    else if sum < target:
        left++
    else:
        right--

return false
```

## What Unnecessary Work Is Eliminated

- No redundant pair checks
- Large regions of the search space are skipped at once
- Decisions are guided by ordering, not brute comparison

## Time Behavior

- Sorting dominates the work
- Pointer movement is linear after sorting

## Space Behavior

- Extra space depends on sorting method
- No auxiliary structure proportional to combinations

## Reference Implementations

### Java

```java
import java.util.Arrays;

class Solution {
    public boolean twoSumExists(int[] nums, int target) {
        Arrays.sort(nums);
        int l = 0, r = nums.length - 1;
        while (l < r) {
            int sum = nums[l] + nums[r];
            if (sum == target) return true;
            if (sum < target) l++;
            else r--;
        }
        return false;
    }

```

### Python

```python
def two_sum_exists(nums, target):
    nums.sort()
    l, r = 0, len(nums) - 1
    while l < r:
        s = nums[l] + nums[r]
        if s == target:
            return True
        if s < target:
            l += 1
        else:
            r -= 1
    return False
```

### C++

```cpp
#include <algorithm>
using namespace std;

bool twoSumExists(vector<int>& nums, int target) {
    sort(nums.begin(), nums.end());
    int l = 0, r = nums.size() - 1;
    while (l < r) {
        int s = nums[l] + nums[r];
        if (s == target) return true;
        if (s < target) l++;
        else r--;
    }
    return false;
}
```

---

# Optimal Solution

## The Inevitable Insight

Sorting is still **indirect**.

The problem does not require order.

It requires **complement detection**.

So the optimal representation is one that answers:

> “Have I seen the complement already?”
> 

## Minimal Representation: Hash-Based Lookup

We maintain a structure of previously seen values.

For each value `x`:

- Compute `need = target − x`
- Check whether `need` already exists

This enforces the invariant **directly**.
![image.png](attachment:170910d2-d9f2-42b5-bb8c-48b6dfc6523d:da6b3d6b-279a-4271-a72c-dc6bd8e5214e.png)
## Pseudocode

```
create empty set seen

for each element x in nums:
    need = target - x
    if need exists in seen:
        return true
    insert x into seen

return false
```

## Why This Is Inevitable

- The invariant is about **existence**, not order
- Hash lookup answers the invariant in constant time
- Any correct solution must perform this logical check, explicitly or implicitly

This approach performs **minimum necessary work**.

## Time Behavior

- One pass through the array
- Each lookup and insertion is constant on average

## Space Behavior

- Stores previously seen values
- Worst case: all elements are distinct

## Reference Implementations

### Java

```java
import java.util.HashSet;

class Solution {
    public boolean twoSumExists(int[] nums, int target) {
        HashSet<Integer> seen = new HashSet<>();
        for (int x : nums) {
            int need = target - x;
            if (seen.contains(need)) return true;
            seen.add(x);
        }
        return false;
    }
}
```

### Python

```python
def two_sum_exists(nums, target):
    seen = set()
    for x in nums:
        if target - x in seen:
            return True
        seen.add(x)
    return False
```

### C++

```cpp
#include <unordered_set>
using namespace std;

bool twoSumExists(vector<int>& nums, int target) {
    unordered_set<int> seen;
    for (int x : nums) {
        if (seen.count(target - x)) return true;
        seen.insert(x);
    }
    return false;
}
```

---

# Final Takeaways

### Combined Reasoning + Complexity Table (Two Sum)

| Approach | Core Idea | Time Reasoning | **Time Complexity** | Space Reasoning | **Space Complexity** |
| --- | --- | --- | --- | --- | --- |
| **Brute Force (Pair Enumeration)** | Check all possible pairs | Every element is paired with all elements after it; number of pair checks grows quadratically as input size increases | **`O(n²)`** | Only loop variables are used; no auxiliary structure grows with input | **`O(1)`** |
| **Better Solution (Sorting + Two Pointers)** | Enforce monotonicity via order | Sorting dominates the work; after sorting, pointers move inward linearly based on sum comparison | **`O(n log n)`** | Extra space may be used by the sorting process (or array copies) | **`O(1)** *(or O(n) depending on sort)*` |
| **Optimal Solution (Complement Lookup)** | Complement existence invariant | Each element is processed once; complement lookup answers the invariant directly in constant average time | **`O(n)`** | Stores previously seen values; worst case all elements are distinct | **`O(n)`** |
- **One-line mental model:** Two Sum is a complement-existence problem.
- **Core invariant:** At each step, all previously seen values are valid complements.
- **Pattern name:** Complement lookup / Hash-based existence check.
- **Recognition signal:** Equation can be rearranged to isolate one variable.
- **Transferability:** Applies to k-sum, subarray sum, difference constraints, and many graph-style problems.
