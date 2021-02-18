---
layout: post
title:  "Pigeonhole Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/pigeonhole-sort/)

Pigeonhole 정렬에 대해 알아보자

- 특정 Range에 있는 아이템들을 정렬할 때 사용한다
- Counting sort와 비슷하다
- 다른점은 Counting Sort는 아이템의 frequency를 저장한 `counter`로 정렬된 리스트에 아이템을 놓지만, Pigeonhole sort는 아이템 자체를 bucket에 복사한 후 다시 정렬된 리스트로 복사하는 방법이다.

Time complexity
- Worst Case: `O(n+k) k가 인풋의 range, n이 인풋의 개수`

Space complexity
- `O(n+k)` 

# Pigeonhole sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/nVQz0kZNC64"></iframe>
</div>

# Python
```python
def pigeonhole_sort(arr):
    # Find the range of the items
    min_val = int(min(arr))
    max_val = int(max(arr))
    range_of_val = max_val - min_val + 1

    # create holes(buckets) to store the items
    holes = [0] * range_of_val

    # populate the pigeonholes
    for value in arr:
        holes[value-min_val] += 1
    
    # place the items back to the array in order
    i = 0
    for count in range(range_of_val):
        while holes[count] > 0:
            holes[count] -= 1
            arr[i] = count + min_val
            i += 1
    
    # Time comp: O(n + k)
    # Space comp: O(n + k)
    # Stable

arr = [5, 2, 3, 1, 16, 4, 7]
pigeonhole_sort(arr)
print(arr) # [1, 2, 3, 4, 5, 7, 16]
```