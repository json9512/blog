---
layout: post
title:  "Bucket Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/bucket-sort-2/)

버켓 정렬에 대해 알아보자

- 인풋이 특정 range에 고르게 분포되어 있을 때 유용하다
- Counting Sort에서는 key가 항상 index인데, 만약 주어진 값들이 float이라면?
- Bucket Sort을 사용하면 된다

**방법**
1. n 개의 빈 bucket을 만든다 (list)
2. arr[i]에 대해 이하 반복한다:
    1. arr[i]를 bucket[n*arr[i]]에 저장한다
3. Insertion Sort을 사용해 bucket을 정렬한다
4. 정렬된 bucket을 다 이어준다

Time complexity
- Best Case: `O(n * k)`
- Worst Case: `O(n ^ 2)`

Space complexity
- `O(nk)` 

# Bucket sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/VuXbEb5ywrU"></iframe>
</div>

# Python
```python
def bucketSort(arr):
    # number of buckets to create
    slots = 10 
    
    # create 2d array for buckets
    buckets = [[] for i in range(slots)]

    for i in arr:
        idx_b = int(slots * i)
        buckets[idx_b].append(i)
    
    # sort individual buckets
    for i in range(slots):
        insertionSort(buckets[i])
    
    # concatenate all slots
    k = 0

    for i in range(slots):
        for j in range(len(buckets[i])):
            arr[k] = buckets[i][j]
            k += 1
    
    # Best: O(n + k) same as counting sort
    # worst: O(n^2) same as insertion
    # space: O(n * k)

arr = [0.8, 0.22, 0.21, 0.4, 0.5, 0.1, 0.99]
bucketSort(arr)
print(arr)
# [0.1, 0.21, 0.22, 0.4, 0.5, 0.8, 0.99]
```