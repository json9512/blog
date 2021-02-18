---
layout: post
title:  "Merge Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/merge-sort/)

Merge 정렬에 대해 알아보자. 

- 분할 정복 알고리즘이다
- 주어진 array를 반으로 나눈다
- 나뉜 반쪽들을 각각 sort해주고 합친다
- Inplace 하지 않다
- Stable 하다
- external sorting에 유용한 알고리즘이다

Time Complexity: 
- Worst: `O(n log n)` 

Auxiliary Space Complexity: 
- `O(n)`

# Merge sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/JSceec-wEyw"></iframe>
</div>

# Python
```python
def mergeSort(arr):
    if len(arr) > 1:
        # find the mid point
        mid = len(arr) // 2

        # divide into two sub arrays
        left = arr[:mid]
        right = arr[mid:]

        # Sort the left half
        mergeSort(left)
        
        # Sort the right half
        mergeSort(right)
        
        # create index counter for left, right, original array
        i = j = k = 0

        while i < len(left) and j < len(right):
            if left[i] < right[j]:
                arr[k] = left[i]
                i+=1
            else:
                arr[k] = right[j]
                j+= 1
            k += 1
        
        # add remaining elements
        while i < len(left):
            arr[k] = left[i]
            i += 1
            k += 1

        while j < len(right):
            arr[k] = right[j]
            j += 1
            k += 1

array = [1, 5, 32, 2, 42, 3, 55]
mergeSort(array)
print(array) # [1, 2, 3, 5, 32, 42, 55]
```