---
layout: post
title:  "Cycle Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/cycle-sort/)

Cycle 정렬에 대해 알아보자

- Inplace한 알고리즘
- stable하지 않은 알고리즘
- 아이템의 위치를 계속 찾으면서 최하 쓰기 횟수로 기존 array를 정렬하는 방식

Time complexity
- Worst Case: `O(n^2)`

Space complexity
- `O(1)` 

# Cycle sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/gZNOM_yMdSQ"></iframe>
</div>

# Python
```python
def cycle_sort(arr):
    # loop through the array and find the cycles to rotate
    for cycle_start in range(0, len(arr)-1):
        item = arr[cycle_start]

        # find where to place the item
        pos = cycle_start
        for i in range(cycle_start+1, len(arr)):
            if arr[i] < item:
                pos += 1
        
        # if pos is the start of cycle, skip
        if pos == cycle_start:
            continue
            
        # otherwise, place the item at pos or after any
        # duplicate
        while item == arr[pos]:
            pos += 1
        
        arr[pos], item = item, arr[pos]

        # rotate the rest of the cycle
        while pos != cycle_start:
            
            # find where to place the item
            pos = cycle_start
            for i in range(cycle_start+1, len(arr)):
                if arr[i] < item:
                    pos += 1
            
            # place the item
            while item == arr[pos]:
                pos += 1
            
            arr[pos], item = item, arr[pos]
    
    # O(n^2), O(1), not stable

arr = [5, 2, 3, 1, 16, 4, 7]
cycle_sort(arr)
print(arr) # [1, 2, 3, 4, 5, 7, 16]
```