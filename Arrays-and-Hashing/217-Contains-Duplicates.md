# **Contains Duplicate**

<hr style="border: none; height: 3px; background-color: #4CAF50;">


**Difficulty:** <span style="color:green"><b>Easy</b></span>  
**Problem Link:** [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/description/)  
**Tags:** `Array` `Hashing` `Set` `Membership Check`

---

# **Problem Overview**

## **Restating the Problem**

You are given an array of integers.

Your task is to determine whether **any value appears more than once** in the array.

- If **at least one element repeats**, return `true`
- If **all elements are unique**, return `false`

## **Inputs and Outputs**

### **Input**

- An integer array `nums`
- Array length ranges from `1` up to a large value (on the order of (10^5))
- Integer values may be very large (both negative and positive)

### **Output**

- A boolean value:
    - `true` → duplicate exists
    - `false` → all elements are distinct

## **Step-by-Step Example**

**Example:**

`nums = [1, 2, 3, 1]`

- Start with the first element `1`
- Scan the remaining elements
- Another `1` is found later in the array
- Since the same value appears more than once → return `true`

## **Edge Cases**

- Array of size `1` → duplicates impossible
- Array where all elements are identical
- Large array with all unique elements
- Presence of negative numbers or very large integers

---

# **Problem Insights**

## **What Is the Problem Really Asking?**

At its core, the problem asks:

> Can we determine whether any two different indices in the array store the same value?
> 

This problem is **not** about counting duplicates.

The moment **one duplicate is detected**, the answer is finalized.

## **Why the Problem Is Non-Trivial**

- Input size can be large, making inefficient comparisons expensive
- A naive comparison-based approach scales poorly
- The problem evaluates:
    - Understanding of time complexity growth
    - Time vs space trade-offs
    - Ability to use data structures to track prior occurrences

---

# **Brute Force Solution**

## **Core Idea**

Compare every element with every other element.

- Fix one element
- Check whether the same value appears again later in the array

This mirrors the most straightforward human approach.

## **Pseudocode**

```
for i from 0 to n - 1:
    for j from i + 1 to n - 1:
        if nums[i] == nums[j]:
            return true
return false

```

## **Why This Works**

- Every unique index pair `(i, j)` is examined exactly once
- If any two values match, a duplicate is confirmed

## **Time Complexity Derivation**

- The outer loop runs `n` times
- The inner loop runs fewer times on each iteration:
    - `(n - 1), (n - 2), ..., 1`
- Total comparisons grow proportionally to the square of `n`

## **Space Complexity Derivation**

- Only loop variables are used
- No extra memory grows with input size

## **Why This Approach Is Inefficient**

- Performs redundant comparisons
- Continues work even after earlier values have been processed
- Becomes impractical for large inputs

This motivates removing **redundant pairwise checks**.

---

# **Better Solution (Counting-Based Detection)**

## **Improvement Over Brute Force**

Instead of comparing values repeatedly:

- Maintain a structure that records how many times each value appears
- As soon as a value is seen again, return `true`

## **Pseudocode**

```
create empty map frequency

for each element x in nums:
    if x exists in frequency:
        return true
    else:
        frequency[x] = 1

return false
```

## **Why This Works**

- Each element is processed exactly once
- Lookup and insertion operations are fast
- The algorithm terminates immediately upon detecting repetition

## **Time Complexity Reasoning**

- Single pass through the array → linear growth
- Each map operation runs in constant average time
- Overall work grows linearly with input size

## **Space Complexity Reasoning**

- The map may store all unique elements
- In the worst case, memory usage grows with the array size

## **Trade-Offs**

- Faster than brute force
- Requires additional memory
- Slightly higher implementation complexity

## **Reference Implementations**

### **Java**

```java
import java.util.HashMap;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashMap<Integer, Integer> freq = new HashMap<>();

        for (int x : nums) {
            if (freq.containsKey(x)) {
                return true;
            }
            freq.put(x, 1);
        }
        return false;
    }
}
```

### **Python**

```python
def contains_duplicate(nums):
    freq = {}
    for x in nums:
        if x in freq:
            return True
        freq[x] = 1
    return False
```

### **C++**

```cpp
#include <unordered_map>
using namespace std;

bool containsDuplicate(vector<int>& nums) {
    unordered_map<int, int> freq;
    for (int x : nums) {
        if (freq.count(x)) {
            return true;
        }
        freq[x] = 1;
    }
    return false;
}
```

---

# **Optimal Solution (Presence-Based Detection)**

## **Core Optimization**

Counting is unnecessary.

To detect a duplicate, we only need to know:

> Has this value appeared before?
> 

A structure that stores **only unique values** is sufficient.

## **Pseudocode**

```
create empty set seen

for each element x in nums:
    if x exists in seen:
        return true
    insert x into seen

return false
```

## **Why This Is Optimal**

- Each element is processed exactly once
- No counting overhead
- Immediate termination on first duplicate
- Minimal and clean logic

## **Time Complexity Reasoning**

- One traversal of the array
- Each set operation runs in constant average time
- Total operations grow linearly with input size

## **Space Complexity Reasoning**

- The set stores unique elements only
- Worst case: all elements are distinct
- Memory usage grows with array size

## **Reference Implementations**

### **Java**

```java
import java.util.HashSet;

class Solution {
    public boolean containsDuplicate(int[] nums) {
        HashSet<Integer> seen = new HashSet<>();

        for (int x : nums) {
            if (!seen.add(x)) {
                return true;
            }
        }
        return false;
    }
}
```

### **Python**

```python
def contains_duplicate(nums):
    seen = set()
    for x in nums:
        if x in seen:
            return True
        seen.add(x)
    return False
```

### **C++**

```cpp
#include <unordered_set>
using namespace std;

bool containsDuplicate(vector<int>& nums) {
    unordered_set<int> seen;
    for (int x : nums) {
        if (seen.count(x)) {
            return true;
        }
        seen.insert(x);
    }
    return false;
}
```

---

## **Time & Space Complexity**

| Approach | Time Complexity | Space Complexity | Core Idea |
| --- | --- | --- | --- |
| Brute Force | `O(n²)` | `O(1)` | Compare all pairs |
| Better | `O(n)` | `O(n)` | Track counts |
| Optimal | `O(n)` | `O(n)` | Track presence |

## Final Takeaways

- **Key Insight:** Duplicate detection is fundamentally a *membership* problem
- **Pattern Learned:** “Seen-before” → use hash-based data structures
- **Reuse When:** Checking uniqueness, detecting repeats, or tracking prior occurrences efficiently

---
