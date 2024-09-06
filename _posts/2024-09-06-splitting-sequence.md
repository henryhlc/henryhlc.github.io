---
layout: post
title: "Splitting sequences to consecutive parts"
---

This is a short note about how I reason about processing consecutive parts
of a sequence that are split based on some conditions. This implementation
structure has been working well for me in solving relevant
problems on platforms such as [leetcode](https://leetcode.com) and 
[codeforces](https://codeforces.com/).

We will start by considering two example scenarios. The first is computing
the run length encoding of a string. Given `"aaabbcddd"`, we would like to
get `"a3b2c1d3"` which specifies that the string starts with 3 `'a'`s follows
by 2 `'b'`s etc. The second is splitting a string by `"/"`. Given `"abc/def/ccd"`,
we want to get `["abc/","def/", "ccd"]`.

The general approach I use now is as follows
```python
open = -1
for i in range(n):
    if open == -1:
        open = i
        # do something to start a new part.
    # process element in the part
    if i+1 == n or closeCondition(i,i+1):
        # do something to close the current part.
        open = -1 
```

The `closeCondition` is an expression or a function that computes whether
we should close the current part at position `i`. The key is that this
could consider elements at both `i` and `i+1` (with the help of the
`i+1==n` guard) and other current part information. In this way, we can
approach the two above examples in the same way. For run length encoding,
we check if `s[i+1]` has changed, and for spliting by dash we check if 
`s[i]=='\'`. There is no distinction between whether we have a condition
that tells us to end the current part or to start a new part.

Another reason I default to this structure is that the first and last parts
are handled easily. Setting `open` to `-1` at the start makes sure that
we always start a new part at the beginning, and this part will be
processed just like other parts. Checking `i+1==n` at the end makes
sure that we always end the last block when there is no more elements
and this part will also be processed like other blocks. There is no
need for code before or after the loop to handle the first or the last
block.

In the last part, I include simple implementations for the two examples
considered using the above approach, and another example where we need
to find the non-decreasing parts of an array of numbers.

```python
# Run length encoding
s = "aabbcccda"
r = ""
open = -1
for i in range(len(s)):
    if open == -1:
        open = i
    if i+1 == len(s) or s[i+1] != s[i]:
        r = f"{r}{i-open+1}{s[i]}"
        open = -1

print(r)  # "2a2b3c1d1a"
```

```python
# Split by /
s = "abc/ddf/efg"
parts = []
open = -1
for i in range(len(s)):
    if open == -1:
        open = i
    if i+1 == len(s) or s[i] == '/':
        parts.append(s[open:i+1]) 
        open = -1
print(parts)  # ["abc/", "ddf/", "efg"]
```

```python
# Non-decreasing consecutive parts 
ns = [1, 2, 1, 4, 4, 5, 2, 6, 1]
parts = []
open = -1
for i in range(len(ns)):
    if open == -1:
        open = i
        parts.append([])
    parts[-1].append(ns[i])
    if i+1 == len(ns) or ns[i+1] < ns[i]:
        open = -1
print(parts)  # [[1,2], [1,4,4,5], [2,6], [1]]
```