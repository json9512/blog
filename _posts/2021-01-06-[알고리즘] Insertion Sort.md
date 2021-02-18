---
layout: post
title:  "Insertion Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/insertion-sort/)

삽입 정렬에 대해 알아보자. 

- 내 손에 쥔 카드를 정렬 할 때와 비슷하다
- 선택한 카드를 알맞는 위치에 삽입하면 된다
- Inplace하고 Stable 하다

##### 알고리즘
1. arr[1] 부터 arr[n] 까지 loop을 돌린다
2. 현재 아이템을 그 전 아이템들과 비교한다
3. 현재 아이템보다 큰 아이템들을 한칸씩 위로 올려주고
4. 현재 아이템을 찾은 위치에 넣어준다 

Time Complexity: 
- Worst: `O(n^2)` 

Auxiliary Space Complexity: 
- `O(1)`

# Insertion sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/OGzPmgsI-pQ"></iframe>
</div>

# Python
```python
def insertionSort(arr):
  for i in range(1, len(arr)):
    print("outer", array)
    key = arr[i]
    
    # move elements of arr[0 ... i-1], that are
    # greater than key, to one position ahead of their current position
    j = i-1
    while j >= 0 and key < arr[j]:
      arr[j+1] = arr[j]
      j -= 1
      print("inner", array)
      
    arr[j+1] = key

array = [43, 32, 3, 21, 15, 4, 2]
insertionSort(array)
print(array) # [[2, 3, 4, 15, 21, 32, 43]]
```