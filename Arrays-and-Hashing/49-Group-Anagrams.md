<h1 style="border-bottom: 3px solid #4CAF50; padding-bottom: 6px;">
  Group Anagrams
</h1>

**Difficulty:** <span style="color:orange"><b>Medium</b></span>  
**Problem Link:** [Group Anagrams](https://leetcode.com/problems/group-anagrams/description/)  
**Tags:** `Array` `Hash Table` `String` `Sorting` `Frequency Count`




---

# Problem Overview

## Restating the Problem

You are given a list of strings.

Your task is to **group strings that are anagrams of each other**.

- Each group contains strings that can be rearranged to form one another
- The order of groups does not matter
- The order of strings inside a group does not matter

## Why the Problem Is Deceptive

At first glance, this looks like:

- a string manipulation problem
- or a sorting problem

But the actual difficulty is **not grouping**, and not rearranging strings.

The real challenge is:

- deciding **what makes two strings equivalent**
- and how to **represent that equivalence consistently**

## What the Problem Is Really About

Conceptually, this problem asks:

> How do we partition a collection of strings into equivalence classes where order of characters is irrelevant?
> 

This is a **classification problem**, not a comparison problem.

The key idea is:

- strings are grouped by a shared invariant
- not by direct pairwise checking

---

# Problem Insights

## Mental Model to Activate

This is a **canonical representation problem**.

Two strings belong to the same group **if and only if**:

- they reduce to the same canonical form
- under a transformation that ignores character order

So the task becomes:

> Find a representation of a string that uniquely identifies its anagram group.
> 

## Misleading Aspects

- Comparing strings directly is misleading
- Pairwise checking scales badly
- Rearranging characters is unnecessary work

## Core Invariant

For any anagram group:

> The frequency of each character is identical across all strings in the group.
> 
- Order does not matter.
- Positions do not matter.
- Only **counts** matter.

## Conceptual Reduction

Each string can be transformed into:

- a **key** that captures character frequency
- and strings with identical keys belong together

The entire problem reduces to:

> Group elements by identical invariant keys.
> 

## Example (Conceptual)

For strings: `["eat", "tea", "tan", "ate", "nat", "bat"]`

- `"eat"`, `"tea"`, `"ate"` → same frequency signature
- `"tan"`, `"nat"` → same frequency signature
- `"bat"` → unique signature

---

# Brute Force Solution

## Core Idea

Compare every string with every other string and group them if they are anagrams.

This is the most literal interpretation of grouping.

## Pseudocode

```
for each string s:
    if s is already grouped:
        continue
    create new group with s
    for each remaining string t:
        if s and t are anagrams:
            add t to the group
```

## Why This Works

- Every possible comparison is made
- Any two anagrams will eventually be grouped together

## Why This Is Only an Upper Bound

- Each anagram check itself is expensive
- Comparisons grow rapidly as the list grows
- The same work is repeated many times

The invariant is enforced **explicitly and repeatedly**, which is inefficient.

---

# Better Solution

## Representation Shift: Canonical Ordering

Instead of comparing strings directly, we transform each string into a **canonical form**.

A common canonical form:

- sort the characters of the string

All anagrams reduce to the same sorted string.

## Why This Representation Works

- Sorting removes positional differences
- Anagrams collapse into identical representations
- Grouping becomes a lookup task

## Pseudocode

```
create empty map groups

for each string s:
    key = sorted characters of s
    add s to groups[key]

return all values of groups
```

## Time Behavior

- Each string is processed independently
- Sorting dominates the cost per string
- Grouping operations are constant-time on average

## Space Behavior

- Map stores one key per unique anagram group
- Each string is stored exactly once

## Reference Implementations

### Java

```java
import java.util.*;

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            char[] arr = s.toCharArray();
            Arrays.sort(arr);
            String key = new String(arr);
            map.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
        }
        return new ArrayList<>(map.values());
    }
}
```

### Python

```python
from collections import defaultdict

def group_anagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    return list(groups.values())
```

### C++

```cpp
#include <unordered_map>
#include <vector>
#include <algorithm>
using namespace std;

vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> groups;
    for (auto& s : strs) {
        string key = s;
        sort(key.begin(), key.end());
        groups[key].push_back(s);
    }
    vector<vector<string>> result;
    for (auto& p : groups) result.push_back(p.second);
    return result;
}
```

---

# Optimal Solution

## Key Observation

- Sorting characters is still **indirect**.
- The invariant is not “sorted order”.
- The invariant is **character frequency**.
- So the optimal representation encodes frequency **directly**.

## Minimal Representation: Frequency Signature

For lowercase English letters:

- a fixed-size frequency array of length 26 uniquely identifies an anagram group

This representation:

- avoids sorting
- captures exactly what matters
- nothing extra

## Pseudocode

```
create empty map groups

for each string s:
    freq = array of size 26 initialized to 0
    for each character c in s:
        freq[c]++
    key = convert freq to string
    add s to groups[key]

return all values of groups
```

## Why This Is Inevitable

- The invariant is frequency equality
- Sorting encodes frequency indirectly
- Direct frequency encoding removes unnecessary work

This enforces the invariant with **minimum transformation**.

## Time Behavior

- Each character is processed once
- No character rearrangement occurs
- Work grows with total number of characters

## Space Behavior

- Keys are fixed-size frequency representations
- Storage depends on number of unique groups

## Reference Implementations

### Java

```java
import java.util.*;

class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<>();
        for (String s : strs) {
            int[] freq = new int[26];
            for (char c : s.toCharArray()) freq[c - 'a']++;
            String key = Arrays.toString(freq);
            map.computeIfAbsent(key, k -> new ArrayList<>()).add(s);
        }
        return new ArrayList<>(map.values());
    }
}
```

### Python

```python
from collections import defaultdict

def group_anagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        freq = [0]*26
        for c in s:
            freq[ord(c)-ord('a')] += 1
        groups[tuple(freq)].append(s)
    return list(groups.values())
```

### C++

```cpp
#include <unordered_map>
#include <vector>
#include <string>
using namespace std;

vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> groups;
    for (auto& s : strs) {
        vector<int> freq(26, 0);
        for (char c : s) freq[c - 'a']++;
        string key;
        for (int f : freq) key += "#" + to_string(f);
        groups[key].push_back(s);
    }
    vector<vector<string>> res;
    for (auto& p : groups) res.push_back(p.second);
    return res;
}
```

---

# Final Takeaways

### Combined Reasoning + Complexity Table

| Approach | Core Idea | Time Explanation | **Time Complexity** | Space Explanation | **Space Complexity** |
| --- | --- | --- | --- | --- | --- |
| **Brute Force** | Compare every string with every other | Each string is compared with all others, and each comparison checks anagram validity | **`O(n² · k)`** | No auxiliary structure beyond grouping lists | **`O(1)`** |
| **Better (Sorting Key)** | Canonical ordering | Each string is sorted to form a grouping key | **`O(n · k log k)`** | Sorted keys and grouped strings stored | **`O(n · k)`** |
| **Optimal (Frequency Key)** | Frequency invariant | Each character contributes directly to a fixed-size signature | **`O(n · k)`** | Fixed-size keys plus grouped strings | **`O(n · k)`** |

(`n` = number of strings, `k` = average string length)

- **One-line mental model:** Group anagrams by identical frequency invariants.
- **Core pattern:** Classification by canonical representation.
- **Recognition signal:** Order irrelevant, grouping required.
- **Transferability:** Appears in multiset grouping, normalization problems, and equivalence-class partitioning.

---
