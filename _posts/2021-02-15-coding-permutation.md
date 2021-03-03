---
title: "List all permutations"
categories:
  - Blog
tags:
  - coding interview
  - Python
  - algorithm
  - backtracking
  - permutation
---

## Problem Introduction

Given a set of distinct characters, find all of its permutations.

Permutation is defined as the re-arranging of the elements of the set. For example, {1, 2, 3} has the following six permutations:

1. {1, 2, 3}
2. {1, 3, 2}
3. {2, 1, 3}
4. {2, 3, 1}
5. {3, 1, 2}
6. {3, 2, 1}

If a set has ‘n’ distinct elements it will have n! permutations.

**Example 1:**

```python
Input: 'ABC'
Output: ['ABC', 'ACB', 'BAC', 'BCA', 'CAB', 'CBA']
```

## Solution

### Naive DFS solution

A naive dfs solution is maintaining a list of remaining characters and current constructed characters, and go through every combination. When there is no remaining characters, we add current permutation into a list.

```python
def permutation_dfs1(in_str):
    results = []
    def dfs(c_perm, remain_str):
        if len(remain_str) == 0:
            results.append(c_perm.copy())
        for i in range(len(remain_str)):
            c_perm.append(remain_str[i])
            dfs(c_perm, remain_str[:i]+remain_str[i+1:])
            c_perm.pop()
    dfs([], list(in_str))
    return results
```

### Better DFS solution

A better dfs solution is swapping every possible character to the current position. After swapping, every character after the first position becomes remaining characters. The next recursion call moves the position forward, when the current position larger than the number of input characters, the recursion returns.

```python
def permutation_dfs2(in_str):
    results = []
    in_str = list(in_str)
    def dfs(idx):
        if idx == len(in_str):
            results.append(in_str.copy())
        for i in range(idx, len(in_str)):
            in_str[idx], in_str[i] = in_str[i], in_str[idx]
            dfs(idx+1)
            in_str[idx], in_str[i] = in_str[i], in_str[idx]
    dfs(0)
    return results
```

