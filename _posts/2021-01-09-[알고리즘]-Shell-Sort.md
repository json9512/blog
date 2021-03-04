---
layout: post
title:  "Shell Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/shellsort/)

쉘 정렬에 대해 알아보자

- 삽입정렬을 살짝 바꾼 알고리즘
- Gap을 이용해서 정렬을 한다.
- Gap을 어떻게 정하는지에 따라 속도가 조금씩 차이가 난다.
- Stable 하지 않다

Time complexity
- Worst Case: `O(n ^ 2)`

Space complexity
- `O(1)` 

# Shell sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/SHcPqUe2GZM"></iframe>
</div>

# Python
```python
def shellSort(arr):
    # Set gap
    N = len(arr)
    gap = N // 2

    # perform insertion sort with gap
    while gap > 0:
        for i in range(gap, N):
            # store item at i index
            temp = arr[i]

            # shift already sorted items
            j = i
            while j >=  gap and arr[j - gap] > temp:
                arr[j] = arr[j - gap]
                j -= gap
            
            # put ith item in correct position
            arr[j] = temp
        
        # reduce the gap by half of its size
        gap //= 2
    
    # O(n^2) for this version
    # O(1)
    # not stable

arr = [5, 2, 3, 1, 16, 4, 7]
shellSort(arr)
print(arr) # [1, 2, 3, 4, 5, 7, 16]
```