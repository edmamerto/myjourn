---
title: My Py Mistakes
date: "2020-02-17T17:51:00.000Z"
layout: post
draft: false
path: "/posts/my-py-mistakes/"
category: "Gatsby"
tags:
  - "Algorithms"
  - "Interviewing"
  - "Python"
description: "Different tiers and their benefits with concepts and examples"
---


## My Py Mistakes

Why am I writing this?
- I can go back to this page to help me remember things
- I've been writing python for some time now and find myself repeating same mistakes
- This is work in progress, and will keep adding as I make more mistake :) 

## py mistakes

### Array range iteration

The following example prints `1, 2`
```python
intervals = [[1,3],[2,6],[4,5]]
# index -->    0     1     2

for i in range(1,len(intervals)):
	print i
```
I always mistake this for 
```python
intervals = [[1,3],[2,6],[4,5]]
# index -->    0     1     2

for i in range(1,len(intervals)-1):
	print i

# prints 1
```
I get this mistake because some algorithms you may want to check the next element of current 
```python
nums = [ 3, 2, 4 ]
# i ---> 0  1  2

max = 0

for i in range(len(nums)-1):

	if nums[i] > nums[i+1]:
		nums[i], nums[i+1] = nums[i+1], nums[i]

print nums

# prints [2,3,4]
```

### For vs. While
these two are equivalent
```python
i=0
while i < len(nums):
	i+=1
```
```python
for i in len(nums):
