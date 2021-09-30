---
title: "List all permutations"
categories:
  - Blog
tags:
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
def permutation_dfs1(in_str: str) -> List[str]:
    results = []
    def dfs(remain_str, c_perm=[]):
        if len(remain_str) == 0:
            results.append(''.join(c_perm))
        for i in range(len(remain_str)):
            dfs(remain_str[:i]+remain_str[i+1:], c_perm + [remain_str[i]])
    dfs(list(in_str))
    return results
```

A caveat of this code is that list addition (c_perm + [remain_str[i]]) in Python actually create an copy of the array; so we don't need to have statements to add/remove elements from the list. (Also, slicing of a list creates a copy.)

Usually a backtracking algorithm is like.
```python
def permutation_dfs1(in_str: str) -> List[str]:
    results = []
    def dfs(remain_str, c_perm=[]):
        if len(remain_str) == 0:
            results.append(''.join(c_perm))
        for i in range(len(remain_str)):
            char = remain_str.pop(i)
            c_perm.append(char)
            dfs(remain_str, c_perm)
            c_perm.pop()
            remain_str.insert(i, char)
    dfs(list(in_str))
    return results
```


### Better DFS solution

A better dfs solution is swapping every possible character to the current position. After swapping, every character after the first position becomes remaining characters. The next recursion call moves the position forward, when the current position larger than the number of input characters, the recursion returns.

```python
def permutation_dfs2(in_str: str) -> List[str]:
    results = []
    in_str = list(in_str)
    def dfs(idx=0):
        if idx == len(in_str):
            results.append(''.join(in_str))
        for i in range(idx, len(in_str)):
            in_str[idx], in_str[i] = in_str[i], in_str[idx]
            dfs(idx+1)
            in_str[idx], in_str[i] = in_str[i], in_str[idx]
    dfs()
    return results
```

### Extention: List permutation with duplication inputs
If the input array contains duplicated numbers, and we are asked to return only the unique permutations.

When each dfs call, we determine one position of character. Therefore, the main idea is we avoid a dfs call if it has duplicate inputs

Here are two solution. Solution using sort to find out those duplications in the input.

```python
def unique_permutation(in_str: str) -> List[str]:
    in_str = list(in_str)
    in_str.sort(key=lambda x: ord(x))
    ans = []

    def dfs(remain, c_perm=[]):
        if len(c_perm) == len(in_str):
            ans.append(''.join(c_perm))
            return
        for i in range(len(remain)):
            # will skip the case, like [1, 1, 2] when i == 1
            if i > 0 and remain[i] == remain[i - 1]:
                continue
            dfs(remain[:i] + remain[i + 1:], c_perm + [remain[i]])
    dfs(in_str)
    return ans
```

Another solution using dictionary to avoid duplication in each position.

```python
def unique_permutation(in_str: str) -> List[str]:
    in_str = list(in_str)
    char_counter = Counter(in_Str)

    def dfs(c_perm=[]):
        if len(c_perm) == len(in_str):
            ans.append(''.join(c_perm))
            return
        for char in char_counter:
            if char_counter[char] > 0:
                char_counter[char] -= 1
                dfs(c_perm + [char])
                char_counter[char] += 1
    dfs()
    return ans
```