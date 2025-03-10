+++
authors = ["Arman Drismir"]
title = "Leet Code Languish"
description = "Ramblings about leet code problems"
date = 2025-03-06
[taxonomies]
tags = ["Algorithm Design", "Python"]
[extra]
toc = true
toc_sidebar = true
+++

## Two sum

I first tried a nested for loop to check what two numbers sum to target. ngl I thought this was as good as you could do but it turns out you can do better...

The better solution is to create a map to store all the values you have seen. Then at the start of each iteration you calculate the value you need to get `target`. Now you can check if you have seen that value is $O(1)$ time. We put the index of the seen values as the key so if we have seen the value we need we also have its index. That way we can return the current iterations index and the needed values index to solve the problem in $O(n)$ time.

```py
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        map = {}
        for i in range(len(nums)):
            compliment = target - nums[i]
            val = map.get(compliment)
            if val != None:
                return [i, map[compliment]]
            map[nums[i]] = i
        
        return []
```

## Best time to buy and sell stock

My first real solution to this was to create an array of the highest value 
that came after index i. Then I could find the biggest difference between 
prices[i] and that array at i and I would have the max possible profit. 

While refining this I realized I could do it in one sweep by keeping track 
of the highest difference between the smallest value before prices[i] and prices[i]. Then we just return that value.

```py
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        lowest_so_far = prices[0]
        highest_diff = 0
        for i in range(1, len(prices), 1):
            price_diff = prices[i] - lowest_so_far
            if price_diff > highest_diff:
                highest_diff = price_diff
            if prices[i] < lowest_so_far:
                lowest_so_far = prices[i]
        
        return highest_diff
```

## 217. Contains duplicates

This was literally just using a map. I got optimal in like 30 seconds.

```py
class Solution(object):
    def containsDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: bool
        """
        map = {}
        for n in nums:
            if map.get(n) is not None:
                return True
            map[n] = 1
        
        return False
```

### 238. Product of array except self

It seems like we should get the product of the entire array and then each i in answer is $answers[i] = total / nums[i]$

This does not work because we may have 0's and negatives in our answer. Hacky soln:
1. Get total excluding 0's
 - If n is 0 exclude it from the total (replace it with identity number which for multiplication is 1)
2. Getting answers
 - if there is more than 1 0's then return an array of all 0's
 - if there is exactly 1 zero then if n is zero then return the total and if n is not zero then return 0
 - if there are no zeros then for each n return total/n

This actually worked! And im pretty sure is optimal as well as beating 93% of submissions in terms of memory :p

I think my code is so memory efficient because I use functools reduce which is probably very optimized?

```py
import functools

arm_num_zeros = 0
def custom_prod(x, y):
    global arm_num_zeros
    if x == 0 or y == 0:
        if x == 0 and y == 0:
            arm_num_zeros += 2
        else:
            arm_num_zeros += 1
        if x != 0:
            return x
        if y != 0:
            return y
        return 1

    return x * y

class Solution(object):
    def productExceptSelf(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        arm_num_zeros = 0
        global arm_num_zeros
        total = functools.reduce(custom_prod, nums)

        if arm_num_zeros >= 2:
            return [0] * len(nums)
        
        answer = []
        for n in nums:
            if arm_num_zeros == 1:
                if n == 0:
                    answer.append(total)
                    continue
                else:
                    answer.append(0)
                    continue
            # There is no zeros
            answer.append(total//n)
        return answer
```