---
layout: post
title:  "[알고리즘] Quick Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/quick-sort/)

Quick 정렬에 대해 알아보자. 

- 분할 정복 알고리즘이다
- pivot을 사용해 주어진 array를 partition한다
- pivot 보다 작은 것은 왼쪽에, 보다 큰 것은 오른쪽에 놓는 작업이다
- pivot의 위치에 따라 알고리즘의 효율성이 달라진다
- Stable하지 않다
- Inplace 하다
- Inplace 알고리즘이기 때문에 merge sort보다 선호 될 때가 있다

Time Complexity: 
- Worst: `O(n^2)` [array에서 가장 큰 수 혹은 작은 수가 pivot인데 array가 이미 정렬이 됐을 때]
- Best: `O(n log n)`

Auxiliary Space Complexity: 
- `O(log n)` (Best)
- `O(n)` (Worst)

# Quick sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/PgBzjlCcFvc"></iframe>
</div>

# Python
```python
def partition(arr, low, high):
    # partition the array
    # place the pivot on the right position 
    # start i (all the numbers less than pivot) from low -1
    i = low-1

    # pivot will be the rightmost element
    pivot = arr[high]

    # iterate through the array
    for j in range(low, high):
        
        # if the current element is less than the pivot
        if arr[j] < pivot:
            # print for visuals
            print(arr)

            # increment the lower bound index
            i+=1

            # swap the current element with the element right after the lower bound index
            arr[i], arr[j] = arr[j], arr[i]
        

    # swap the pivot with the element at lower bound index + 1
    arr[i+1], arr[high] = arr[high], arr[i+1]

    # return the position of the pivot
    return i + 1

def quickSort(arr, low, high):
    # only partition when lower bound is lower than higher bound
    if low < high:
        partitioned = partition(arr, low, high)

        # quick sort the left sub array of pivot
        quickSort(arr, low, partitioned-1)

        # quick sort the right sub array of pivot
        quickSort(arr, partitioned+1, high)

arr = [0, 10, 7, 3, 8, 9, 1, 5] 
n = len(arr) 
quickSort(arr,0,n-1) 
print(arr)

'''
[0, 10, 7, 3, 8, 9, 1, 5] # j = 0
[0, 10, 7, 3, 8, 9, 1, 5] # swap j, i = 0, 0
[0, 3, 7, 10, 8, 9, 1, 5] # swap j = 3, i = 1 + 1 = 2; arr[j] <-> arr[i]
[0, 3, 1, 5, 8, 9, 7, 10]
[0, 1, 3, 5, 8, 9, 7, 10]
[0, 1, 3, 5, 8, 9, 7, 10]
[0, 1, 3, 5, 8, 9, 7, 10]
[0, 1, 3, 5, 7, 8, 9, 10] # final
'''
```

#### Iterative
```python
def partition(arr, low, high):
    # partition the array
    # place the pivot on the right position 
    # start i (all the numbers less than pivot) from low -1
    i = low-1

    # pivot will be the rightmost element
    pivot = arr[high]

    # iterate through the array
    for j in range(low, high):
        
        # if the current element is less than the pivot
        if arr[j] < pivot:
            # print for visuals
            print(arr)

            # increment the lower bound index
            i+=1

            # swap the current element with the element right after the lower bound index
            arr[i], arr[j] = arr[j], arr[i]
        

    # swap the pivot with the element at lower bound index + 1
    arr[i+1], arr[high] = arr[high], arr[i+1]

    # return the position of the pivot
    return i + 1


def quickSort(arr, low, high):
    # create supplementary stack
    size = high - low + 1
    stack = [0] * size

    # push initial values to stack
    top = 0
    stack[top] = low
    top += 1
    stack[top] = high

    # keep popping while stack is not empty
    while top >= 0:
        # pop low, high
        high = stack[top]
        top -= 1
        low = stack[top]
        top -=1

        # set pivot in its correct position
        pivot = partition(arr, low, high)

        # if there are elements on left side of pivot,
        # push it to stack
        if pivot-1 > low:
            top += 1
            stack[top] = low
            top += 1
            stack[top] = pivot - 1
        
        # if there are elements on right side of pivot,
        # push it to the stack
        if pivot+1 < high:
            top += 1
            stack[top] = pivot + 1
            top += 1
            stack[top] = high

arr = [4, 3, 5, 2, 1, 3, 2, 3] 
n = len(arr) 
quickSort(arr, 0, n-1) 
print(arr)
'''
[4, 3, 5, 2, 1, 3, 2, 3]
[2, 3, 5, 4, 1, 3, 2, 3]
[2, 1, 5, 4, 3, 3, 2, 3]
[2, 1, 2, 3, 3, 3, 5, 4]
[2, 1, 2, 3, 3, 3, 5, 4]
[2, 1, 2, 3, 3, 3, 4, 5]
[1, 2, 2, 3, 3, 3, 4, 5]
'''
```

## Pivot을 Mid로 할 때
출처: [BogoToBogo](https://www.bogotobogo.com/Algorithms/quicksort.php)

```python
def quickSort2(arr):
    def sort(arr, low, high):
        if low >= high:
            return
        
        left = low
        right = high

        # pivot selection
        mid = (left + right) // 2
        pivot = arr[mid]

        # Partition
        while left <= right:
            # find the element from left 
            # that is greater than pivot
            while arr[left] < pivot:
                left += 1
            
            # find the element from right
            # that is less than pivot
            while arr[right] > pivot:
                right -= 1
            
            # swap the left and right items
            # if left <= right
            if left <= right:
                arr[left], arr[right] = arr[right], arr[left]
                left += 1
                right -= 1
        
        # recursion
        sort(arr, low, right)
        sort(arr, left, high)

    sort(arr, 0, len(arr) - 1)
```