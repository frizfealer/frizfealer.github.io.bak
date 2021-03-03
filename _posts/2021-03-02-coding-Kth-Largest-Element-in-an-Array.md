---
title: "Kth Largest Element in an Array"
categories:
  - Blog
tags:
  - coding interview
  - Python
  - algorithm
  - divide and conquer
  - heap
---

## Problem Introduction

Find the kth largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Example 1:**

```shell
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

**Example 1:**

```shell
Input: [3,2,3,1,2,4,5,5,6] and k = 4
Output: 4
```

**Note:**
This is the leetcode problem 215, also refer to [**selection problem**](https://en.wikipedia.org/wiki/Selection_algorithm) in computer science.

## Solution

### Sorting Solution

There are multiple ways of solving this problem.

Thanks to the availibity of sorting api call, the simplest implementation is first sort the array, from large to small, then select the k-th element from the sorted array.

Time complexity: O(nlogn) for sorting.

### Brute-force Solution

A brute-force way of solving this is also simple; though the implementation is a bit invovled. The algorithm is as follows. We first find out the largest element in the array. Then we marked that number as already used, and find out the largest element in the array that has not been used. We only need to keep track of the previously used element because the current element is always smaller then the previous element and all elements before. If there are duplicate elements, we also need to keep track the previous index of element being used.

```python
def find_kth_largest(nums, k):
  last_max_val = float('inf')
  last_max_idx = -1
  for _ in range(k):
    max_val = -float('inf')
    max_idx = -1
    for i in range(len(nums)):
      if nums[i] > max_val and \
        nums[i] <= last_maxl_val and \
        i > last_max_idx:
        max_val = nums[i]
        max_idx = i
    last_max_val = max_val
    last_max_idx = max_idx
```

Time complexity: O(kn), where O(n) for each inner loop.

### Min-heap Solution

By maintaining a min-heap of size k while inserting all numbers from the array, we have the least k-small, the k-largest elements in the heap.

```python
from heapq import *
def find_kth_largest(nums, k):
  min_heap = []
  for i in range(len(nums)):
    if i < k:
      min_heap.append(nums[i])
    else:
      heapreplace(min_heap, nums[i])
  return min_heap[0]
```

Time complexity: O(nlogk). Each heap operation causes O(logk)

### Quickselect Solution with Random Pivots

The algorithm using the idea of divide-and-conquer. In each run, we choose a pivot element. In this case, we randomly choose a element. A **partition** function put the elements smaller than the pivot to its left and the elements larger than the pivot to its right, and returns the location of pivot. In other words, a partition function puts the pivot in its right location.

![partition function illustration]({{ site.url }}/assets/images/partition_function_illustrations.jpg)

After the parition, if we find out the location of pivot is equals to the input k, than it is the element we want. Otherwise; there are two possibilities. In one case, the pivot is on the right of the k location, and we search the left of the pivot. In another case, the pivot is on the left of the k location, and we search the right of the pivot. In each iteration, we reduce the search space and if there is just one element or we find the exact k location, we can return directly.

```python
import random

def find_kth_largest(nums, k):
  low, high = 0, len(nums)-1
  while True:
    if low == high:
      return nums[low]
    pivot_idx = random.randint(low, high)
    pivot_idx = partition(nums, low, high, pivot_idx)
    if pivot_idx == k - 1:
      return nums[pivot_idx]
    elif pivot_idx < k - 1:
      low = pivot_idx + 1
    else:
      high = pivot_idx - 1

def partition(nums, low, high, pivot_idx):
  pivot = nums[pivot_idx]
  nums[pivot_idx], nums[high] = nums[high], nums[pivot_idx]
  s = low
  for r in range(low, high):
    if nums[r] > pivot:
      nums[r], nums[s] = nums[s], nums[r]
      s += 1
  nums[s], nums[high] = nums[high], nums[s]
  return s

```

Time complexity: The best and average case is O(n). The worst cases happens when we can only parition the array into 1 and N-1 parts. Therefore, resulting in an O(n^2) worst-case.

### Quickselect Solution with Median of Medians

A Good pivot divides the array evenly and has the time complexity O(n). If there is a linear time algorithm to select such good pivots, then we have a better solution than random version. Luckily, there is an algorithm called [**Median of medians**](https://en.wikipedia.org/wiki/Median_of_medians) that guarantees to return a pivot between 30 to 70 percentile, and guarantees a O(n) worst-case time complexity.

```python

def find_kth_largest(nums, k):
  return select(nums, 0, len(nums)-1, k)

def select(nums, low, high, k):
  while True:
    if low == high:
      return nums[low]
    pivot_idx = find_pivot(nums, low, high)
    pivot_idx = partition(nums, low, high, pivot_idx)
    if pivot_idx == k - 1:
      return nums[pivot_idx]
    elif pivot_idx < k - 1:
      low = pivot_idx + 1
    else:
      high = pivot_idx - 1

def partition(nums, low, high, pivot_idx):
  pivot = nums[pivot_idx]
  nums[pivot_idx], nums[high] = nums[high], nums[pivot_idx]
  s = low
  for r in range(low, high):
    if nums[r] > pivot:
      nums[r], nums[s] = nums[s], nums[r]
      s += 1
  nums[s], nums[high] = nums[high], nums[s]
  return s

def find_pivot(nums, low, high):
  if high - low < 5:
    return partition5(nums, low, high)
  for i in range(low, high+1, 5):
    print(f"{i}: {min(i+5, high)}")
    median5 = partition5(nums, i, min(i+5, high))
    nums[median5], nums[low + (i-low)//5] = \
      nums[low + (i-low)//5], nums[median5]
    mid_idx = (high-low)/10 + low + 1
  return select(nums, low, low + (high - low)// 5, mid_idx)

def partition5(nums, low, high):
  i = low + 1
  while i <= high:
    j = i
    while j > 0 and nums[j-1] < nums[j]:
      nums[j-1], nums[j] = nums[j], nums[j-1]
      j -= 1
    i += 1
  return ((low+high)//2)
```

