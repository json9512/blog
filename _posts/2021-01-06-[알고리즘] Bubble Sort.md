---
layout: post
title:  "Bubble Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/bubble-sort/)

버블 정렬에 대해 알아보자. 

- 가장 간단한 정렬 알고리즘
- 모든 item을 짝지어서 비교한다
- 이미 정렬된 array를 마지막에 다시 한 번 훑어야 하는 단점이 있다
- Inplace하고 Stable한 알고리즘이다

Time Complexity: 
- Worst: `O(n^2)` [array 가 reverse 정렬 됐을 시]
- Best: `O(n)` [array 가 정렬이 됐을 시]

Auxiliary Space Complexity: 
- `O(1)`

# Bubble sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/nmhjrI-aW5o"></iframe>
</div>

# Python
```python
def bubbleSort(arr):
  n = len(arr)
 
  # loop through the entire array
  for i in range(n):
    # inner loop
    # n-i-1: elements found above here are already sorted
    for j in range(0, n-i-1):
      # Swap if the element found is greater than the next element
      if arr[j] > arr[j+1]:
        arr[j], arr[j+1] = arr[j+1], arr[j]

array = [51, 23, 41, 22, 1, 5]
bubbleSort(array)
'''
outer loop iteration
[51, 23, 41, 22, 1, 5] initial
[23, 41, 22, 1, 5, 51]
[23, 22, 1, 5, 41, 51]
[22, 1, 5, 23, 41, 51]
[1, 5, 22, 23, 41, 51]
[1, 5, 22, 23, 41, 51] sorted

inner loop operation for first outer loop iteration
outer loop: [51, 23, 41, 22, 1, 5] initial

inner loop: [51, 23, 41, 22, 1, 5] initial
inner loop: [23, 51, 41, 22, 1, 5]
inner loop: [23, 41, 51, 22, 1, 5]
inner loop: [23, 41, 22, 51, 1, 5]
inner loop: [23, 41, 22, 1, 51, 5]
inner loop: [23, 41, 22, 1, 5, 51] after j loop finished
'''
```