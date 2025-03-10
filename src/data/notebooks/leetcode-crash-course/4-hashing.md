---
title: "Hashing"
pubDate: 2024-12-13
chapter: 4
draft: false
notebookTitle: "Leetcode - Interview Crash Course"
---

## Hash Maps
- Uses a hash function which converts any arbitrary input(keys) into an integer that is less than the fixed size set by a programmer. It has an algorithm and modulus which make sures that the value falls under a range of numbers
- hash funtion is combined with an array, it creates a hash map, hash table or dictionary
- arrays map indices to values, hashmaps maps keys to values. The keys should be immutable. Hash maps are unordered which means that the insertion order is not relevant and not remembered
- The following operations are $O(1)$ for a hash map:
  - Add an element and associate it with a value
  - Delete an element if it exists
  - Check if an element exists
- The most important thing to understand is that with arrays we used to have indexes map to values and we needed the index to get the value. Now, with hash map we can store the values as a key to get direct access to the value.
*This helps to keep the main information in the key of a hash map and keep any meta data related to it in the value part*
- Tradeoffs for hash maps:
  - for smaller input sizes, hash maps are slower because of overhead
  - resizing the hash table is more expensive than resizing a dynamic array
  - **Collisions**: Different keys can convert to the same integer while hashing using a hash function, the data can get overriden and lost in the case if not handled. For handling this, **chaining** is a common approach which stores a linked list inside the array with key, value pair and add the new key, value to its tail. Whenever we search for a value using a key, it will traverse the entire linked list and give us the correct value

## Sets
- very similar to Hash map, but does not map the keys to anything
- helpful if we only care about checking if element exists
- They also don't track frequency, that is sets does not store any duplicate

> Storing an array as key is possible in Hash map and Sets. array are mutable, so we convert it to a tuple in python (`tuple(arr)`) which is immutable and then store it as a key.

## Pattern 1: Checking for existence
- Most common application of a hash table is to determine if an element exists in $O(1)$ as array takes $O(n)$ to do this
- Anytime you find your algorithm running `if ... in ...`, then consider using a hash map or set to store elements to have these operations run in $O(1)$
- Example of returing indices of two numbers such that they add up to `target` in an array `nums`
```python
# Time Complexity: O(n), Space Complexity: O(n)
def twoSum(self, nums: List[int], target: int) -> List[int]:
  dic = {}
  for i in range(len(nums)):
    num = nums[i]
    complement = target - num
    if complement in dic:   # This operation is O(1)!
      return [i, dic[complement]]

    dic[num] = i

  return [-1, -1]
```
- Example of returing the first character to appear twice from a string `s`

*Bruteforce solution*
```python
# Time Complexity: O(n^2), Space Complexity: O(1)
def repeatedCharacter(self, s: str) -> str:
  for i in range(len(s)):
    c = s[i]
    for j in range(len(s)):
      if s[j] == c:
        return c

  return ""
```
*Using a set can reduce this to $O(1)$*
```python
# Time Complexity: O(n)
# Space Complexity
# O(m) where m is the number of allowable characters in the input
# O(1) as input can have characters from English Alphabet which is constant 26
def repeatedCharacter(self, s: str) -> str:
  seen = set()
  for c in s:
    if c in seen:
      return c
    seen.add(c)

  return ""
```
- Example of finding all unique elements `x` in `nums` that satisfy the following: `x + 1` is not in `nums`, and `x - 1` it not in `nums`
```python
# Time Complexity: O(n), Space Complexity: O(n)
def find_numbers(nums):
  ans = []
  nums = set(nums)

  for num in nums:
    if (num + 1 not in nums) and (num - 1 not in nums):
      ans.append(num)

  return ans
```

## Pattern 2: Counting
- Counting is referred to tracking the frequency of things in a hash map where keys will map to integers
- Anytime we need to count something, think about using hash map
- We can use **Sliding window with a hashmap** where the constraint involves tracking multiple elements
- Example of finding the length of the longest substring in a string `s` that contains at most `k` distinct characters
```python
# Time Complexity: O(n), Space Complexity: O(k) as the algorithm delete elements once the hash map grows beyond k
from collections import defaultdict

def find_longest_substring(s, k):
  counts = defaultdict(int)
  left = ans = 0

  for right in range(len(s)):
    counts[s[right]] += 1

    while len(counts) > k:
      counts[s[left]] -= 1
      if counts[s[left]] == 0:
        del counts[s[left]]
      left += 1
      
    ans = max(ans, right - left + 1)

  return ans
``` 
- Example of returning a sorted array containing all the numbers that appear in all `n` arrays inside a 2D array `nums`
```python
# nums has n elements with m elements inside every array
# Hash map creation and in the worst case, if every element is unique, going through the hash map both will take O(n.m)
# Sorting will take m.logm, so the time complexity is O(m.(n + logm))
# Time Complexity: O(m.(n + logm)), Space Complexity: O(n.m)
from collection import defaultdict

def intersection(self, nums: List[List[int]]) -> List[int]:
  counts = defaultdict(int)
  for arr in nums:
    for x in nums:
      counts[x] += 1

  n = len(nums)
  ans = []
  for key in counts:
    if count[key] == n:
      ans.append(key)

  return sorted(ans)
```
- Example of determining if all characters have same frequency in a string `s`
```python
# Time Complexity: O(n), Space Complexity: O(k)
from collections import defaultdict

def areOccurrencesEqual(self, s: str) -> bool:
  counts = defaultdict(int)

  for c in s:
    counts[c] += 1
  
  frequencies = counts.values()   # Get the values in a dictionary and creates an array
  return len(set(frequencies)) == 1
```
One liner using Python collection's `Counter`
```python
from collections import defaultdict

def areOccurrencesEqual(self, s: str) -> bool:
  return len(set(Counter(s).values())) == 1
```
### Count the number of subarrays with an "exact" constraint
- helps to find a solution to the questions 'Find the number of subarrays that have sum exactly equal to `k`'
- Example of finding the number of subarrays whose sum is equal to `k` from an integer array `nums`
```python
# Time Complexity: O(n), Space Complexity: O(n)
from collections import defaultdict

def subarraySum(self, nums: List[int], k: int) -> int:
  counts = defaultdict(int)
  counts[0] = 1
  ans = curr = 0

  for num in nums:
    curr += num
    ans += counts[curr - k]
    counts[curr] += 1

  return ans
```
- Example of couting number of nice subarrays(exactly `k` odd numbers) in an array of positive integers `nums`
```python
# Time Complexity: O(n), Space Complexity: O(n)
from collections import defaultdict

def numberOfSubarrays(self, nums: List[int], k: int) -> int:
  counts = defaultdict(int)
  counts[0] = 1
  ans = curr = 0

  for num in nums:
    curr += nums % 2
    ans += counts[curr - k]
    counts[curr] += 1

  return ans
```
- A *little twist* to the above pattern can ask for 'Find the length of longest subarray that have sum exactly equal to `k`'
- Example of returning the maximum length of a contiguos subarray with an equal number of `0` and `1` given a binary array `nums`
```python
# Time Complexity: O(n), Space Complexity: O(n)
from collections import defaultdict

class Solution:
    def findMaxLength(self, nums: List[int]) -> int:
      counts = defaultdict(int)
      counts[0] = -1
      ans = curr = 0

      for i in range(len(nums)):
        num = 1 if nums[i] == 1 else -1
        curr += num
        if curr in counts:
          ans = max(ans, i - counts[curr])
        else:
          counts[curr] = i

      return ans
```

## More hashing examples
- Given an array of strings `strs`, group the anagrams together
```python
# Time Complexity: O(n.m.logm), Space Complexity: O(n.m)
from collections import defaultdict

def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
  groups = defaultdict(list)
  # n: length of strs, m: length of strings, iteration over and sorting costs O(n.m.logm)
  for s in strs:
    key = "".join(sorted(s))
    groups[key].append(s)

  return list(groups.values()) # Worst case: O(n) as every key keep a single string value
```
- Given an integer array `cards`, find the length of the shortest subarray that contains at least one duplicate. If the array has no duplicates, return `-1`
```python
# Time Complexity: O(n), Space Complexity: O(n)
from collections import defaultdict

def minimumCardPickup(self, cards: List[int]) -> int:
  dic = defaultdict(list)

  for i in range(len(cards)):
    dic[cards[i]].append(i)

  ans = float("inf")
  for key in dic:
    arr = dic[key]
    for i in range(len(arr) - 1):
        ans = min(ans, arr[i + 1] - arr[i] + 1)
        
  return ans if ans < float('inf') else -1
```
*Optimisation* realising that we only need the most recent index for each number. This improves the space complexity as the previous one has $O(n)$ space complexity always, but this one will have $O(n)$ space complexity only in the worst case when there are no duplicates
```python
# Time Complexity: O(n), Space Complexity: O(n)
from collections import defaultdict

def minimumCardPickup(self, cards: List[int]) -> int:
  dic = defaultdict(int)
  ans = float("inf")
  for i in range(len(cards)):
    if cards[i] in dic:
      ans = min(ans, i - dic[cards[i]] + 1)

    dic[cards[i]] = i
        
  return ans if ans < float('inf') else -1
```
- Given an array of integers `nums`, find the maximum value of `nums[i] + nums[j]`, where `nums[i]` and `nums[j]` have the same **digit sum** (the sum of their individual digits). Return `-1` if there is no pair of numbers with the same digit sum
```python
# Time Complexity: O(n.logn), Space Complexity: O(n)
from collections import defaultdict

def maximumSum(self, nums: List[int]) -> int:
  def get_digit_sum(num):
    digit_sum = 0
    while num:
      digit_sum += num % 10
      num //= 10

    return digit_sum

  dic = defaultdict(list)
  for num in nums:
    digit_sum = get_digit_sum(num)
    dic[digit_sum].append(num)

  ans = -1
  for key in dic:
    curr = dic[key]
    if len(curr) > 1:
      curr.sort(reverse=True)
      ans = max(ans, curr[0] + curr[1])

  return ans
```
*Optimisation*: Realise we can solve it just by capturing the maximum number under each digit sum, instead of keeping all the elements
```python
# Time Complexity: O(n), Space Complexity: O(n)
from collections import defaultdict

def maximumSum(self, nums: List[int]) -> int:
  def get_digit_sum(num):
    digit_sum = 0
    while num:
        digit_sum += num % 10
        num //= 10

    return digit_sum

    dic = defaultdict(int)
    ans = -1
    for num in nums:
      digit_sum = get_digit_sum(num)
      if digit_sum in dic:
        ans = max(ans, num + dic[digit_sum])
      dic[digit_sum] = max(dic[digit_sum], num)

    return ans
```
- Given an `n x n` matrix `grid`, return the number of pairs `(R, C)` where `R` is a row and `C` is a column, and `R` and `C` are equal(elements are same and appear in same order) if we consider them as 1D arrays
```python
# Time Complexity: O(n^2), Space Complexity: O(n^2)
from collections import defaultdict

class Solution:
    def equalPairs(self, grid: List[List[int]]) -> int:
      def convert_to_key(arr):
        return tuple(arr)

      dic = defaultdict(int)
      for row in grid:
        dic[convert_to_key(row)] += 1

      dic2 = defaultdict(int)
      for col in range(len(grid[0])):
        current_col = []
        for row in range(len(grid)):
          current_col.append(grid[row][column])
        
        dic2[convert_to_key(current_col)] += 1

      ans = 0
      for arr in dic:
        ans += dic[arr] * dic2[arr]

      return ans
```