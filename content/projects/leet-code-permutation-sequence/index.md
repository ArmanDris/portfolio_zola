+++
authors = ["Arman Drismir"]
title = "Solution for Leet Code 60. Permutation Sequence"
description = "Solution for Leet Code 60. Permutation Sequence"
date = 2026-03-01
[taxonomies]
tags = ["Leet Code", "Python", "Algorithm Design"]
[extra]
+++

The set [1, 2, 3, ..., n] contains a total of n! unique permutations.

By listing and labeling all of the permutations in ascending order, we get the following sequence for n = 3:

<div style="font-size: 1em; line-height: 1.1;">
<ol>
  <li><code>123</code></li>
  <li><code>132</code></li>
  <li><code>213</code></li>
  <li><code>231</code></li>
  <li><code>312</code></li>
  <li><code>321</code></li>
</ol>
</div>

## The solution:

First, think about the problem of generating just the first number of the
solution. To help visualize the pattern look at the first digit in the 
permutation set for `n=4`.

<div style="font-size: 1em; line-height: 1.1;">
<ol>
  <li><code>1 234</code></li>
  <li><code>1 243</code></li>
  <li><code>1 324</code></li>
  <li><code>1 342</code></li>
  <li><code>1 423</code></li>
  <li><code>1 432</code></li>
<br />
  <li><code>2 134</code></li>
  <li><code>2 143</code></li>
  <li><code>2 314</code></li>
  <li><code>2 341</code></li>
  <li><code>2 413</code></li>
  <li><code>2 431</code></li>
<br />
  <li><code>3 124</code></li>
  <li><code>3 142</code></li>
  <li><code>3 214</code></li>
  <li><code>3 241</code></li>
  <li><code>3 412</code></li>
  <li><code>3 421</code></li>
<br />
  <li><code>4 123</code></li>
  <li><code>4 132</code></li>
  <li><code>4 213</code></li>
  <li><code>4 231</code></li>
  <li><code>4 312</code></li>
  <li><code>4 321</code></li>
</ol>
</div>

We can see that the first number starts at 1, and increments after 6 iterations.
For an arbitrary `n`, how many iterations will it take before the first digit
increments?

**In general, the first digit increments after `(n-1)!` iterations.** 

So after every `(n-1)!` sized "block" the first digit will increase by 1.
Therefore `k // (n-1)!` (where `//` is truncated division) will get us the
number of "blocks" until the `k`th permutation. After each block the first
digit increments by one, so `k // (n-1)!` will give us the first digit!

<br />
Now we try to find the next digit. If we draw out each block's remaining digits 
we can see that a similar-ish pattern emerges, where there are "blocks" that
start with the same digit.

<div style="font-size: 1em; line-height: 1.1;">
<ol>
  <li><code>_ 234</code></li>
  <li><code>_ 243</code></li>
<br />
  <li><code>_ 324</code></li>
  <li><code>_ 342</code></li>
<br />
  <li><code>_ 423</code></li>
  <li><code>_ 432</code></li>
<br />
<br />
  <li><code>_ 134</code></li>
  <li><code>_ 143</code></li>
<br />
  <li><code>_ 314</code></li>
  <li><code>_ 341</code></li>
<br />
  <li><code>_ 413</code></li>
  <li><code>_ 431</code></li>
<br />
<br />
  <li><code>_ 124</code></li>
  <li><code>_ 142</code></li>
<br />
  <li><code>_ 214</code></li>
  <li><code>_ 241</code></li>
<br />
  <li><code>_ 412</code></li>
  <li><code>_ 421</code></li>
<br />
<br />
  <li><code>_ 123</code></li>
  <li><code>_ 132</code></li>
<br />
  <li><code>_ 213</code></li>
  <li><code>_ 231</code></li>
<br />
  <li><code>_ 312</code></li>
  <li><code>_ 321</code></li>
</ol>
</div>

The pattern is a little hard to spot, but what is happening is that within each
"super-block" of 6 permutations, we have a "sub-block" of size`(n-2)!`, and 
within each sub block we increment from 1 to 4, using the numbers that have not
already been used by the first digit.

To get the second digit we calculate the size of our "sub-block", in this case
`(4-2)!` so `2`. Then we calculate how many "sub-blocks" into our "super-block"
`k` appears in. The formula for this is 
`(k - super_block_start_index - 1) // sub_block_size`.

So we can get how many "sub-blocks" into our "super-block" `k` appears at, but
each "sub-block" has a different number, so how does this help us?

**Key insight:** Within each "super-block" the first character of the first
"sub-block" is always the smallest available number. The first character of the
second "sub-block" is always the 2nd smallest available number, etc...

So the second digit is  `(k - super_block_start_index - 1) // sub_block_size = x`
where `x` represents the `x`th smallest character we can still use.

<br />

This process is repeated, dividing the problem into smaller "sub-blocks" until
we have resolved each character in the answer.

The final code for the problem is this:
```py
from math import factorial

class Solution:
    def getPermutation(self, n: int, k: int) -> str:
        solution = ""
        
        nums = [x for x in range(1,n+1)]
        box_start_index = 0

        iteration = 0
        while len(solution) < n:
            box_size = factorial((n - iteration - 1))
            box_index = (k - box_start_index - 1) // box_size
            solution += str(nums[box_index])
            nums.pop(box_index)

            box_start_index += box_size * box_index
            iteration += 1


        return solution
```
