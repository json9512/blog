---
layout: post
title:  "[알고리즘] Counting Sort"
tags: algorithm
comments: true
---

출처: [GeeksforGeeks](https://www.geeksforgeeks.org/counting-sort/)

Counting 정렬에 대해 알아보자

- 특정 범위에 있는 아이템을 정렬 할 때 사용한다
- 각 아이템의 카운트를 구한 뒤
- 수학적 방식으로 접근해서 정렬된 array를 만든다
- Inplace 하지 않다
- Stable 하다

##### 알고리즘
1. arr 내에 모든 아이템의 개수를 counter array에 저장한다
2. counter array를 (1, n) 으로 for loop을 돌면서, counter[i] += counter[i - 1] 로 바꾼다
3. arr를 뒤에서부터 돌면서 
    1. 각 아이템 (i)의 counter[i] - 1 값을 index로 사용한다
    2. 아이템 i를 output array에 index 위치에 저장한다
4. arr를 다시 한번 돌면서 각 item을 output array에 있는 값으로 바꾼다

Time Complexity:
- Worst: `O(n+k)` [n 이 아이템의 수, k가 아이템의 범위]

Space Complexity:
- `O(n+k)`


# Counting sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/7zuGmKfUt7s"></iframe>
</div>

# Python
```python
def countingSort(arr):
    # extract max and min value
    max_val = int(max(arr))
    min_val = int(min(arr))
    range_of_val = max_val - min_val + 1

    # final sorted array
    output = [0 for i in range(len(arr))]

    # counter array
    counter = [0 for i in range(range_of_val)]

    # Count the items in the array
    for i in range(len(arr)):
        idx = arr[i] - min_val
        counter[idx] += 1
    
    # Modify counter array to keep the index position of the items
    # in the sorted array
    for i in range(1, len(counter)):
        # add the previous item's counter value
        counter[i] += counter[i-1]
    
    # Create the sorted array
    for i in range(len(arr) - 1, -1, -1):
        # find the index value for the item arr[i]
        idx = arr[i] - min_val
        counter_val = counter[idx]
        
        # place it in the final array
        output[counter_val - 1] = arr[i]

        # decrease the value in the counter array
        counter[idx] -= 1
    
    # update the values in array 
    for i in range(len(arr)):
        arr[i] = output[i]

arr = [5, 2, 3, 1, 16, 4, 7]
countingSort(arr)
print(arr) # [1, 2, 3, 4, 5, 7, 16]
```