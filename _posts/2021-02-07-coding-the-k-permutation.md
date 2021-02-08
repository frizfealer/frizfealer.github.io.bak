---
title: "The k permutation (Google coding interview)"
categories:
  - Blog
tags:
  - coding interview
  - Python
  - Google
---

## Problem Introduction

Recently I heard a Google coding interview problem from a friend, the problem goes like:
Consider n possible digits for permutations, find out the k-smallest (or largest) permutation.

if n = 3 and k = 4. For n possible digits permutations, we have

1. 123
2. 132
3. 213
4. 231
5. 312
6. 321

The fourth ranking permutation is 231, so the answer is 231.

**Example 1:**

```Python
Input: n = 3, k = 2
Output: 132
```

**Example 2:**

```Python
Input: n = 4, k = 4
Output: 1342
```

## Solution

### Brute-force Solution

it first using DFS to generate all permutations and then using heap to get top-k permutation

Time complexity: ![equation](http://www.sciweavers.org/tex2img.php?eq=O%28n%21%29%20%2B%20O%28%5Clog%28n%21%29%29%20%2B%20O%28k%5Clog%28n%21%29%29&bc=White&fc=Black&im=jpg&fs=12&ff)
<!-- # https://stackoverflow.com/questions/11256433/how-to-show-math-equations-in-general-githubs-markdownnot-githubs-blog -->

### Order-maintained Solution

A better solution is maintaining the correct order when generating permutations, such that when generating the k-th permutation, it can directly return the results. The below codes uses a heap to maintain the the orders of viable numbers in the current dfs call, and return whenever we have k permutations in a global results list.

```Python
def get_k_perm(n, k):
    comb = 1
    valid_k = False
    for i in range(n, 0, -1):
        comb *= i
        if comb >= k:
            valid_k = True
            break
    if not valid_k:
        print(f"{k} is not valid for this {n}")
        return None
    
    nums = [i for i in range(1, n+1)]
    results = []

    def dfs(nums, c_perm):
        if not nums:
            results.append(c_perm.copy())
            return
        heap = nums.copy()
        heapify(heap)
        while heap:
            min_digit = heappop(heap)
            nums.remove(min_digit)
            c_perm.append(min_digit)
            dfs(nums, c_perm)
            nums.append(min_digit)
            c_perm.pop()
            if len(results) >= k:
                break

    dfs(nums, [])
    return results[-1]
```

Time complexity: for each heappop, it costs O(logN). and for list remove and list copy, it costs O(N). In each call of dfs, it has one list-copy, one heapify, one heappop, and one list-remove, it costs O(2logN + 2N). Along the path to the end, it costs O(2logN + 2N + log(N-1) + 2(N-1) + ... + log(1) + 2) = O(N^2), and it searches over k such paths, so overall time complexity is O(kN^2)

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