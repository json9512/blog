---
layout: post
title:  "Selection Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/selection-sort/)

선택 정렬에 대해 알아보자. 

- 가장 작은 수를 가장 앞단에 놓는 알고리즘이다
- Inplace 하다
- Stable 하지 않다

Time Complexity: 
- Worst: `O(n^2)` 

Auxiliary Space Complexity: 
- `O(1)`

# Selection sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/xWBP4lzkoyM"></iframe>
</div>

# Python
```python
def selectionSort(arr):
  # loop through the entire array
  for i in range(len(arr)):
    # idx position to store minimum value
    min_idx = i
    
    # loop through the to-sort array (i+1, end)
    for j in range(i+1, len(arr)):
      
      # Update idx with the minimum value
      if arr[j] < arr[min_idx]:
        min_idx = j
    
    # swap positions after j loop is finished
    arr[i], arr[min_idx] = arr[min_idx], arr[i]

array = [64 ,32, 52, 1, 31, 36, 31]
selectionSort(array)
print(array) # [1, 31, 31, 32, 36, 52, 64]
```