<h1 style="border-bottom: 3px solid #4CAF50; padding-bottom: 6px;">
  Contains Duplicate
</h1>


**Difficulty:** <span style="color:green"><b>Easy</b></span>  
**Problem Link:** [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/description/)  
**Tags:** `Array` `Hashing` `Set` `Membership Check`

# Problem Overview

## Restating the Problem

You are given an array of integers.

Your task is to determine whether **any value appears more than once** in the array.

- If **at least one element repeats**, return `true`
- If **all elements are unique**, return `false`

## Why the Problem Is Deceptive

At surface level, this looks like a simple scanning task.

In reality, the problem is about **detecting violation of uniqueness**, not counting or ordering.

The difficulty comes from:

- Large input sizes
- No constraints on value range
- The requirement to stop as soon as *one* violation is found

This shifts the problem away from enumeration and toward **invariant detection**.

## What the Problem Is Really About

Conceptually, the problem asks whether the array satisfies the invariant:

> Each value appears at most once
> 

The moment this invariant is violated, the answer is determined.

---

# Problem Insights

## Mental Model to Activate

This is a **membership and uniqueness** problem.

The core question is not:

> “How many times does each value appear?”
> 

but:

> “Has this value appeared before?”
> 

This distinction is critical.

## Misleading Aspects of the Problem

- The wording encourages pairwise comparison
- Counting feels natural but is not strictly required
- Order and position are irrelevant

## Conceptual Reduction

The problem reduces to checking **set equality between indices and values**:

- Each new value must be unseen so far
- The invariant is **no repetition in the prefix processed so far**

Once this invariant breaks, computation can stop.

## Reasoning-Oriented Example

For `nums = [1, 2, 3, 1]`:

- After processing `[1, 2, 3]`, all values are unique
- Encountering `1` again violates the invariant
- No further inspection is necessary

---

# Brute Force Solution

## Core Idea

Compare every element with every other element.

- Fix one index
- Check all later indices for equality

This represents the most literal enforcement of the invariant.

## Pseudocode

```
for i from 0 to n - 1:
    for j from i + 1 to n - 1:
        if nums[i] == nums[j]:
            return true
return false
```

## Why This Works

- All possible index pairs are checked
- Any duplicate must appear in some pair

This establishes **correctness in principle**.

## Why This Is Only an Upper Bound

- The same values are compared repeatedly
- Past comparisons provide no reusable knowledge
- Work grows quadratically as input grows

The invariant is enforced, but **inefficiently**.

---

# Better Solution

## Representation Shift: Counting Occurrences

Instead of re-comparing values, we change representation:

- Track which values have already been encountered
- Use stored knowledge to avoid redundant work

This encodes the invariant **implicitly**.

## Pseudocode

```
create empty map frequency

for each element x in nums:
    if x exists in frequency:
        return true
    else:
        frequency[x] = 1

return false
```

## Why This Representation Works

- The map records prior occurrences
- Each lookup answers the question: *has this value appeared before?*
- Redundant comparisons are eliminated entirely

## Time Behavior

- Each element is processed once
- Each lookup and insertion is constant on average
- Total work grows proportionally with input size

## Space Behavior

- One entry per unique value
- In the worst case, all values are distinct

## Reference Implementations

### Java

```java
import java.util.HashMap;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) {
            if (freq.containsKey(x)) return true;
            freq.put(x, 1);
        }
        return false;
    }
}
```

### Python

```python
def contains_duplicate(nums):
    freq = {}
    for x in nums:
        if x in freq:
            return True
        freq[x] = 1
    return False
```

### C++

```cpp
#include <unordered_map>
using namespace std;

bool containsDuplicate(vector<int>& nums) {
    unordered_map<int, int> freq;
    for (int x : nums) {
        if (freq.count(x)) return true;
        freq[x] = 1;
    }
    return false;
}
```

---

# Optimal Solution

## Key Observation

Counting is unnecessary.

The invariant only requires **presence detection**, not frequency magnitude.

So we move to the minimal structure that enforces this invariant.

## Minimal Representation: Set of Seen Values

A set stores exactly what matters:

- Whether a value has appeared before

Nothing more.

## Pseudocode

```
create empty set seen

for each element x in nums:
    if x exists in seen:
        return true
    insert x into seen

return false
```

## Why This Is Inevitable

- The invariant is binary: seen or not seen
- Counting adds information that is never used
- Any correct solution must answer the same membership question

This enforces the invariant with **minimum possible work**.

## Time Behavior

- Single pass through the array
- Constant-time membership checks on average

## Space Behavior

- One entry per unique value
- Worst case when all values are distinct

## Reference Implementations

### Java

```java
import java.util.HashSet;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashSet<Integer> seen = new HashSet<>();
        for (int x : nums) {
            if (!seen.add(x)) return true;
        }
        return false;
    }
}
```

### Python

```python
def contains_duplicate(nums):
    seen = set()
    for x in nums:
        if x in seen:
            return True
        seen.add(x)
    return False
```

### C++

```cpp
#include <unordered_set>
using namespace std;

bool containsDuplicate(vector<int>& nums) {
    unordered_set<int> seen;
    for (int x : nums) {
        if (seen.count(x)) return true;
        seen.insert(x);
    }
    return false;
}
```

---

# Final Takeaways

### Combined Reasoning + Complexity Table

| Approach | Core Idea | Time Reasoning | **Time Complexity** | Space Reasoning | **Space Complexity** |
| --- | --- | --- | --- | --- | --- |
| **Brute Force (Pairwise Comparison)** | Compare all index pairs | Every element is compared with all elements after it; number of comparisons grows quadratically as array size increases | **`O(n²)`** | Only loop variables are used; no auxiliary structure grows with input | **`O(1)`** |
| **Better Solution (Counting with Map)** | Track occurrences | Each element is processed once; map lookup and insertion are constant on average, so work grows linearly | **`O(n)`** | Map stores each unique element encountered; worst case all elements are distinct | **`O(n)`** |
| **Optimal Solution (Presence with Set)** | Track seen values | Single pass through array; each membership check answers the invariant directly with constant average cost | **`O(n)`** | Set stores only unique elements; worst case all elements are unique | **`O(n)`** |
- **One-line mental model:** Duplicate detection is invariant violation of uniqueness.
- **Core pattern:** *Seen-before / membership tracking*
- **Key signal:** Order does not matter; early termination is allowed.
- **Transferable logic:** Applies to uniqueness checks, cycle detection, constraint validation, and prefix-based invariants.

---
