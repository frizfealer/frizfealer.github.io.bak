---
title: "The k permutation"
last_modified_at: 2021-02-27
categories:
  - Blog
tags:
  - coding interview
  - Python
  - backtracking
  - permutation
---

## Problem Introduction

Recently I heard a coding interview problem from a friend. The problem goes like:
given n possible digits for permutations, return the k-smallest (or largest) permutation.

if n = 3 and k = 4, for n possible digits permutations, we have

1. 123
2. 132
3. 213
4. 231
5. 312
6. 321

The 1-smallest permutation is 123; whereas the 3-smallest permutation is 213.

**Example 1:**

```python
Input: n = 3, k = 2
Output: 132
```

**Example 2:**

```python
Input: n = 4, k = 4
Output: 1342
```

## Solution

### Brute-force Solution

First using DFS to generate all permutations. Second, using min-heap to get k-smallest permutation.

Time complexity: O(n!)+O(log(n!))+O(klog(n!))

<!-- # https://stackoverflow.com/questions/11256433/how-to-show-math-equations-in-general-githubs-markdownnot-githubs-blog -->

### Generting Permutations On-the-fly Solution

A better solution is generating permutations on-the-fly instead of generating all of them. In this process, we want to make sure the generating order is what we want, such that when generating the k-th permutation, it can directly return the results.

```python
def get_k_perm(n, k):
    # checking k is a valid number for n
    perms = 1
    valid_k = False
    for i in range(n, 0, -1):
        perms *= i
        if perms >= k:
            valid_k = True
            break
    if not valid_k:
        print(f"{k} is not valid for this {n}")
        return None
    #define dfs routine
    remain_nums = [i for i in range(1, n+1)]
    results = []
    def dfs(remain_nums, c_perm):
        if not remain_nums:
            results.append(c_perm.copy())
            return
        for i in range(len(remain_nums)):
            c_perm.append(remain_nums[i])
            dfs(remain_nums[:i] + remain_nums[i+1:],
                c_perm)
            c_perm.pop()
            if len(results) == k:
                break
    dfs(remain_nums, [])
    return results[-1]
```

Time complexity: In each call of dfs, it has one list-copy. It costs O(N). There are N such calls for each path to the end; therefore O(N^2). We only search over k such paths; so overall time complexity is O(kN^2).

## Pattern Solution

If we look closer at the permutations, we can found a specific pattern. E.g.

1. 123
2. 132
3. 213
4. 231
5. 312
6. 321

The first digit from the left, repeats 2! times, the second digit from the left repeats 1! times. Every i-th digit from the left repeats (n-i)! times. At first there are three digits = [1, 2, 3]; therefore, the first digit of the k-th permutation is digits[floor((k-1)/(n-1))!)]. At the second digit from the left, there are only two digits left, but we can use the same formula as before to determine the index of the digits array.

```python
def get_k_perm(n, k):
    # checking k is a valid number for n
    perms = 1
    valid_k = False
    for i in range(n, 0, -1):
        perms *= i
        if perms >= k:
            valid_k = True
            break
    if not valid_k:
        print(f"{k} is not valid for this {n}")
        return None
    nums = [i for i in range(1, n+1)]
    result = []
    divider = 1
    for i in range(1, n):
        divider *= i
    k -= 1
    while len(nums) > 1:
        index = k//divider
        k = k % divider
        divider = int(divider / (len(nums) - 1))
        result.append(nums[index])
        nums.remove(nums[index])
    result.append(nums[0])
    return result
```

Time complexity: for each digit, it costs O(n) to remove number from nums list, and we have n digits; therefore the total time complexity is O(n^2)
