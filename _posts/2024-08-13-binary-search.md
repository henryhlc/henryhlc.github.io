---
layout: post
title: "Implementing binary search over a sequence"
---

This is a short note about how I reason about implementing variants of binary
search over a sequence. This has been working well for me in solving relevant
problems on platforms such as [leetcode](https://leetcode.com) and 
[codeforces](https://codeforces.com/).

Binary search over a sequence is conceptually simple to describe. To find an
target element in an sorted array, you find the element at the middle check if
you have found the target, if not, then recursively check the half before the
middle or the half after the middle.

However, there are more details. You need to decide what to do if there are
multiple elements. For example [C++](https://en.cppreference.com/w/cpp/algorithm#Binary_search_operations_.28on_partitioned_ranges.29) 
standard library has the lower bound and the upper bound, which together gives
the range of elements of the given value. 
```
        upper_bound
        v
12333333456
  ^
  lower_bound
```

You need to make sure that you gracefully handle the case where the element
is not found and the end condition is correct. A buggy example could be:
```
# Buggy
while high - low > 0: 
    mid = (high + low) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] > target:
        high = mid
    else:
        low = mid
```
It gets stucked at `low = 0` forever in the following example
```
         high = 1
         v
arr = [1,3]
       ^
       low = 0
target = 2
```
The problem is not fixed if we change the loop condition to `high - low > 1`
because in that case the loop will miss the element when the array is `[2,2]`

To make solving problems with binary search easier, I organized my thoughts and
ended up with the following approach which I find easy to reason about.

First, I started thinking of all binary search problems as finding the highest
index where a condition is not met and the lowest index where a condition is
met. The requirement for the condition is that if the condition is true at `i`
the condition must be true at `j` if `j > i`. Pictorally we get the following:
```
       lowest where a condition is met
       v
00000001111111
      ^
      highest where a condition is not met
```
This helps me use the same algorithm to handle several cases by specifying
the condition differently. For example the smallest element greater than target
uses the condition `> target` and the smallest element greater than or equal
to the target uses the condition `>= target`.
```
target = 4
arr = [1,2,3,4,5,6,7]
      [0,0,0,1,1,1,1], for >= target
      [0,0,0,0,1,1,1], for > target
```

Second, I maintain the condition that the range `(low, high)` contains elements
that have not been visited before. To make this true, at the start both `low`
and `high` should be just out of the range, and the loop should continue if and
only if `high - low > 1` which is equivalent to saying there are unexplored
elements. The final algorithm is as follows:
```
low = -1
high = len(arr)
while high - low > 1:
    mid = (high + low) // 2
    if cond(mid):
        high = mid
    else
        low = mid
```

A benefit of this setup is that this handles the "element not found" cases well.
We can easily tell if the condition is always met or never met by checking if
`high` or `low` is out of range. Also, as `low` and `high` starts out of
range there is no case where we would need to check values at the boundary
before the loop starts.
```
           high
           v
 0000000000
          ^
          low
 high
 v
 1111111111
^
low
```
