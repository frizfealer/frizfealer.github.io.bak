---
title: "Monotonic stack"
last_modified_at: 2021-02-27
categories:
  - Blog
tags:
  - Algorithm/Data Structure
  - Python
  - stack
---

## Introduction

A monotonic stack is a stack maintaining the monotonic property for its element. E.g. if it is an increasing monotonic stack, it is [x1, x2, ..., xn] such that x1 <= x2 <= ... xn.
There are some problems when you maintain monotonic stack is useful. In these, you are asked to return the next larger number for each number in an array. The keywords here are **next (prev) greater (lesser)**

For example, given a input array

```python
input_array = [4, 3, 7, 5, 6]
```

The next greater number for each element is

```python
next_larger = [7, 7, -1, 6, -1]
```

If there is no such value, we set it as -1. For the last element, since there is no value after that, it always set to -1.

A brute-force solution uses a nested-for-loop, where the outer loop goes through each element, and the inner loop searches for the next greater element. It's an O(n^2) solution. However; using a monotonic stack, we only go through the array one time to construct the next larger number and it's an O(n) solution.

## Algorithm

Here are some good references for the algorithm ([ref1](https://labuladong.gitbook.io/algo-en/ii.-data-structure/monotonicstack), [ref2](https://stackoverflow.com/questions/55780200/intuition-behind-using-a-monotonic-stack)). It maintains a monotonic decreasing stack. Let's say if your input is sorted and all unique, e.g. [1, 3, 4, 5], then the next greater element is always the next one. However, if there are some decreasing orders or duplicated values, we cannot determine the next greater when we saw them. Rather, we put them in the stack, and if later we encounter a greater one. For example

```python
input_array = [6, 4, 3, 3, 4, 5, 6]
```

Going from 6 to 3, we see a decreasing order and don't know the next greater element. We keep them in a monotonic stack. Only when we see the fifth element (4), we know it is the next greater element we are looking for at least for the last element. We then look back into the stack to see if there are other elements also see this as the next greater. In this case, both third and fourth elements are, and they are pop out of the stack.

```python
stack = [6, 4, 4]
```

After popping out the "3"s, we can still maintain the monotonic stack. Next, we encounter five, following the same logic, we pop out the "4"s, and the stack becomes

```python
stack = [6, 5]
```

When seeing 6 in the end, we pop out 5 and the stack becomes

```python
stack = [6, 6]
```

Since we maintain this monotonic decrease in the process, *whenever we see the violation of monotonic decreasing, we know there we can find the next greater element.* For each pop, we record the popped element and the current element as the current element is the next greater element in the stack.

## Leetcode problems

(### 496. Next Greater Element I)

For reference, the brute-force solution is

```python
def nextGreaterElement(nums1: List[int], nums2: List[int]) -> List[int]:
  num2idx = {i: idx for idx, i in enumerate(nums2)}
  ans = []
  for i in nums1:
    idx = num2idx[i]
    next_g = -1
    for j in range(idx, len(nums2)):
      if nums2[j] > i:
        next_g = nums2[j]
        break
    ans.append(next_g)
  return ans  
```

And monotonic-stack solution is

```python
def nextGreaterElement(nums1: List[int], nums2: List[int]) -> List[int]:
  num2ng = {i: -1 for i in nums2}
  stack = []
  ans = []
  for i in range(len(nums2)):
    while stack and nums2[i] > stack[-1]:
      num2ng[stack.pop()] = nums2[i]
    stack.append(nums2[i])
  for i in nums1:
    ans.append(num2ng[i])
  return ans
```

### 739. Daily Temperatures

This is similar to the problem 496. But instead of asking for return the value, it asking return the difference between indices.

```python
def dailyTemperatures(T: List[int]) -> List[int]:
  ans = [0]*len(T)
  stack = []
  for i in range(len(T)):
    while stack and T[i] > T[stack[-1]]:
      index = stack.pop()
      ans[index] = i - index  
    stack.append(i)
  return ans
```

### 503. Next Greater Element II

This is a bit tricky as it set the array to be circular. We can double the array, either physically or virtually to simulate the circular condition in this problem.

```python
def nextGreaterElements(nums: List[int]) -> List[int]:
  ans = [-1]*len(nums)
  stack = []
  for idx in range(len(nums)*2):
      while stack and nums[stack[-1]] < nums[idx%len(nums)]:
          ans[stack.pop()] = nums[idx % len(nums)]
      stack.append(idx % len(nums))
  return ans
```

### 907. Sum of Subarray Minimums

Before going through this problem, let's check the two variants

#### Next Less Element

Instead of return the next greater element, here we are asked to return the next less. The difference is we maintain a monotonic increasing stack.

```python
def nextLessElement(nums):
  ans = [-1]*len(nums)
  stack = []
  for i in range(len(nums)):
    while stack and nums[stack[-1]] > nums[i]:
      ans[stack.pop()] = i
    stack.append(i)
  return ans
```

#### Previous Greater Element

Instead of return the next greater element, here we are asked to return the previous greater element. While it is also a valid solution to go through the input backward, the solution below goes through the input forward. This solution is valid because the elements that are less than or equal to the current element in the stack are pop, so we only see the previous greater element. Also, these poped elements are not used anymore because the current elements we pushed will be the previous greater element afterward.

```python
def prevGreaterElement(nums):
  ans = [-1]*len(nums)
  stack = []
  for i in range(len(nums)):
    while stack and nums[stack[-1]] <= nums[i]:
      stack.pop()
    if stack:
      ans[i] = stack[-1]
    stack.append(i)
  return ans
```

With these two variable functions in mind, we can solve the problem 907 easily.

Please see this [post](https://leetcode.com/problems/sum-of-subarray-minimums/discuss/178876/stack-solution-with-very-detailed-explanation-step-by-step) for the detailed explanation of the algorithm.

```python
def sumSubarrayMins(arr: List[int]) -> int:
  # distance to the left least element
  left = [i for i in range(1, len(arr)+1)]
  # distance to the right least elemnt
  right = [i for i in range(len(arr), 0, -1)]
  n_stack = []
  p_stack = []
  for i in range(len(arr)):
    while n_stack and arr[n_stack[-1]] > arr[i]:
      idx = n_stack.pop()
      right[idx] = i - idx
    n_stack.append(i)
    while p_stack and arr[p_stack[-1]] > arr[i]:
      p_stack.pop()
    if not p_stack:
      left[i] = i + 1
    else:
      left[i] = i - p_stack[-1]
    p_stack.append(i)
  ans = 0
  for i in range(len(arr)):
      ans += left[i]*right[i]*arr[i]
      ans %= 10**9 + 7
        
    return ans
```
