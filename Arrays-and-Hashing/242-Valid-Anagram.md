<h1 style="border-bottom: 3px solid #4CAF50; padding-bottom: 6px;">
  Valid Anagram
</h1>

**Difficulty:** <span style="color:green"><b>Easy</b></span>  
**Problem Link:** [Valid Anagram](https://leetcode.com/problems/valid-anagram/description/)  
**Tags:** `String` `Hashing` `Frequency Count` `Membership Check`

---

# Problem Overview

## Restating the Problem

We are given two strings **`s`** and **`t`** and asked a deceptively simple question:

**Can one string be rearranged to form the other?**

At face value, this sounds like a string manipulation task.

In reality, this is a **constraint-checking and invariant-validation problem**.

What the problem *really* demands is:

- The ability to **reason about equivalence under reordering**
- Identifying **what property of strings must remain invariant** when order is ignored
- Choosing a representation that makes violations of that invariant obvious

This problem is less about strings and more about **multiset equality**.

---

# Problem Insights

### What mental model should be activated?

👉 **Think in terms of frequency distributions, not characters.**

Two strings are anagrams **if and only if**:

- They contain the **same elements**
- With the **same multiplicities**
- Order is irrelevant

This immediately shifts the thinking from:

> “How do I rearrange characters?”
> 

to:

> “How do I verify both strings represent the same multiset?”
> 

### What is misleading in the problem statement?

- The word *“anagram”* tempts candidates to think about **rearranging** characters.
- Rearrangement is a **red herring** — we never need to actually rearrange anything.
- The core task is **verification**, not construction.

### What does the problem reduce to conceptually?

At its core, this reduces to:

> Do both strings induce the same frequency mapping over characters?
> 

Once you see that, everything else becomes an implementation detail.

### Example (Reasoning-Oriented Walkthrough)

`s = "anagram"`, `t = "nagaram"`

Instead of scanning visually:

- Ask: *Does every character appear the same number of times in both?*
- If **any one character disagrees**, the entire claim collapses.

This is an **all-or-nothing invariant**.

---

# Brute Force Solution

### Baseline Thought Process

- Generate **all permutations** of `s`
- Check if `t` matches any of them

### Why this exists (and why it fails)

- Establishes correctness **in principle**
- Immediately collapses due to:
    - Factorial growth
    - Massive redundancy
    - Zero scalability

This approach is useful only to realize:

> “We must avoid rearrangement entirely.”
> 

### Pseudocode (Reference Only)

This pseudocode exists **only** to formalize why brute force is conceptually correct but practically useless.

```
generate all permutations of string s
for each permutation p:
    if p == t:
        return true
return false
```

**Why this pseudocode matters (and stops here):**

- It proves correctness by construction
- It makes the inefficiency self-evident
- It sets the ceiling from which all better ideas descend

---

# Better Solution

### Key Mental Shift

Instead of rearranging:

> Compare canonical representations
> 

A *canonical form* is a standardized representation such that:

- Equal multisets → identical canonical form

Sorting is one such canonicalization.

### How the idea is discovered

Ask yourself:

- “If order doesn’t matter, can I force both strings into the *same order*?”
- Sorting neutralizes ordering differences.

Now the problem becomes:

> “Do both strings reduce to the same normalized sequence?”
> 

### What unnecessary work is eliminated?

- We don’t generate permutations
- We don’t compare characters positionally
- We let sorting **implicitly encode frequency**

### Why constraints allow this

- Strings are finite
- Characters are comparable
- Sorting is deterministic

### Pseudocode (Supportive, Not Central)

```
if length(s) != length(t): return false
sort(s)
sort(t)
return s == t
```

### Complexity Analysis

The reasoning here relies on **canonicalization via sorting**.

- The dominant operation is forcing both strings into a common, order-independent form.
- Sorting is the only non-trivial work being done.
- Once sorted, comparison is linear and negligible relative to sorting.

Why this cost arises:

- Sorting must compare characters multiple times to establish order.
- Each comparison-based sort requires repeated passes over the data as input size grows.
- Space is consumed because we materialize sorted representations.

This solution trades **conceptual simplicity** for **extra ordering work** that is not fundamentally required by the problem.

## Reference Implementation (Validation Only)

### Java

```java
import java.util.*;

class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;

        char[] a = s.toCharArray();
        char[] b = t.toCharArray();

        Arrays.sort(a);
        Arrays.sort(b);

        return Arrays.equals(a, b);
    }
}
```

### Python

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False
        return sorted(s) == sorted(t)
```

### C++

```cpp
#include <algorithm>
using namespace std;

class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;
        sort(s.begin(), s.end());
        sort(t.begin(), t.end());
        return s == t;
    }
};
```

---

# Optimal Solution

### Core Insight (The Real Pattern)

> Anagram checking is frequency balancing.
> 

This is a **conservation problem**:

- Every character added must be canceled somewhere else
- If anything remains unbalanced → not an anagram

### Why this approach is inevitable

Sorting works because it *hides* frequency comparison inside ordering.

But if:

- The character set is fixed (e.g., lowercase letters)
- Order is irrelevant

Then directly counting frequencies is the **most honest representation**.

Anything else is indirect.

### The Key Invariant

After processing both strings:

> All character counts must be exactly zero
> 

If even one count is non-zero:

- Either a character is extra
- Or missing
- Or mismatched in frequency

### Mental Model (Generalized)

Think of this as:

- Adding weights from `s`
- Subtracting weights from `t`
- Final balance must be zero everywhere

This is a **net-flow invariant**.

### Pseudocode (Minimal, Logic-Backed)

```
if length(s) != length(t): return false
initialize count array

for each char in s:
    increment count[char]

for each char in t:
    decrement count[char]

if any count != 0:
    return false
return true
```

### Why nothing simpler works

- Comparing sets ignores frequency
- Comparing sorted strings hides unnecessary work
- Any approach must, explicitly or implicitly, verify **frequency equality**

This does it **directly**.

### Complexity Analysis

This approach is driven by **frequency conservation**, not ordering.

- Each character contributes a constant, bounded update to a counter.
- Each position in the strings is touched exactly once.
- No rearrangement, no comparisons between characters.

Why this leads to optimal performance:

- The work grows strictly in proportion to input length.
- The character domain is fixed (lowercase letters), so auxiliary storage does not scale with input size.
- Verification is a simple invariant check: all balances must cancel out.

This is the **minimum work necessary** to verify multiset equality.

For Unicode:

- The same reasoning holds.
- The only change is replacing fixed arrays with dynamically keyed storage.
- Logic stays identical; representation adapts.

## Reference Implementation (Validation Only)

### Java (Lowercase English Letters)

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;

        int[] freq = new int[26];

        for (int i = 0; i < s.length(); i++) {
            freq[s.charAt(i) - 'a']++;
            freq[t.charAt(i) - 'a']--;
        }

        for (int v : freq) {
            if (v != 0) return false;
        }
        return true;
    }
}
```

### Python (Lowercase English Letters)

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        freq = [0] * 26
        for cs, ct in zip(s, t):
            freq[ord(cs) - ord('a')] += 1
            freq[ord(ct) - ord('a')] -= 1

        return all(v == 0 for v in freq)
```

### C++ (Lowercase English Letters)

```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.size() != t.size()) return false;

        vector<int> freq(26, 0);
        for (int i = 0; i < s.size(); i++) {
            freq[s[i] - 'a']++;
            freq[t[i] - 'a']--;
        }

        for (int v : freq) {
            if (v != 0) return false;
        }
        return true;
    }
};
```

### Unicode-Safe Variant (Concept Validation)

```java
import java.util.*;

class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;

        Map<Character, Integer> freq = new HashMap<>();
        for (char c : s.toCharArray())
            freq.put(c, freq.getOrDefault(c, 0) + 1);

        for (char c : t.toCharArray()) {
            if (!freq.containsKey(c)) return false;
            freq.put(c, freq.get(c) - 1);
            if (freq.get(c) == 0) freq.remove(c);
        }
        return freq.isEmpty();
    }
}
```

### Unicode Follow-Up (Pattern Extension)

The pattern remains unchanged:

- Only the **representation** adapts

Instead of:

- Fixed-size array → use a **hash map**

Reasoning stays identical:

> Count → balance → verify zero
> 

---

# Final Takeaways

### Combined Reasoning + Complexity Table

| Approach | Core Idea | Time Reasoning | **Time Complexity** | Space Reasoning | **Space Complexity** |
| --- | --- | --- | --- | --- | --- |
| **Brute Force (Permutations)** | Try all rearrangements | Number of possible permutations of a length-`n` string is `n!`, which grows explosively and becomes infeasible even for small `n` | **`O(n!)`** | Space needed for recursion stack or generating a permutation of length `n` | **`O(n)`** |
| **Better Solution (Sorting)** | Canonicalize order | Sorting forces both strings into a comparable form; repeated comparisons during sorting dominate total work | **`O(n log n)`** | Extra space for sorted character arrays / copies of strings | **`O(n)`** |
| **Optimal Solution (Frequency Count)** | Frequency balancing invariant | Each character is visited a constant number of times; no reordering or comparisons are required | **`O(n)`** | Fixed-size frequency structure since alphabet size is bounded (lowercase English) | **`O(1)`** |
| **Optimal (Unicode Variant)** | Frequency balancing with map | Same linear scan logic, but frequency storage grows with the number of distinct characters | **`O(n)`** | Storage proportional to distinct characters encountered | **`O(k)`** |

### One-Line Mental Model

> Anagrams are strings with identical frequency distributions.
> 

### Pattern Name

**Frequency Balancing / Multiset Equality**

### Signals to Recognize This Pattern Again

- Order does not matter
- Exact counts matter
- Equality under rearrangement
- Conservation-style problems

---
