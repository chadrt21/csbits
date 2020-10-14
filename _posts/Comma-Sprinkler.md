---
title:  "Problem B: Comma Sprinkler (2018)"
tags:
  - ACM ICPC
  - Python
---

While this problem is relatively simple it can be misleading if you don't take into account the time limit (the big O of the algorithm).

<!--more-->

## Problem

<embed link="problemB2018.pdf" type="application/pdf" width="100%" height="745px" />
If having difficulty viewing this PDF: [click here]({{site.url}}/problemB2018.pdf)

## Analysis

If this problem was solved using brute force then it becomes an issue to solve as the text gets larger; O(n^2). Thus if the input text is 1 billion, then it would take 1 quin-trillion (10^18) steps (~475 years).

```python
import re
# Parse input
in_test = input()
in_parse = re.split(r'\w+',in_test)

for i in range(len(in_parse)-1):
  if in_parse[i] is ', ':

  if in_parse[i] is ' ,':
```

Since this problem is comparing the relation between words it can be viewed as a graph problem; which simplifies the problem.

## Solution
https://www.youtube.com/watch?v=e6PXYpEU3n0
https://github.com/SnapDragon64/ACMFinalsSolutions/blob/master/finals2018/commaDK.cc

If a more efficient approach is taken by representing each of the words in a graph then the run time solution is O(n) thus in the worse case of a text of 1 billion words then it can be solved in 1 billion steps (~)


```python
import re
in_test = input()
```
