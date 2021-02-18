---
layout: post
title:  "Search algorithms"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/fundamentals-of-algorithms/)

여러가지 Search 알고리즘에 대해 알아보자
1. [Linear Search](#linear-search)
2. [Binary Search](#binary-search)
3. [Jump Search](#jump-search)
4. [Interpolation Search](#interpolation-search)
5. [Exponential Search](#exponential-search)
6. [Ternary Search](#ternary-search)

# Linear Search
- 제일 쉬운 알고리즘
- `O(n)` 의 시간 복잡도를 가진다

```python
def search(arr, target=None):
    if not target:
        return -1
    
    for i in range(len(arr)):
        if arr[i] == target:
            return i
    
    return -1

arr = [1, 5, 3, 2, 4]
print(search(arr, 5)) # 1
print(search(arr, 0)) # -1
print(search(arr)) # -1
```

# Binary Search
- 이분탐색
- 각 iteration마다 탐색 해야 할 배열의 크기를 반으로 줄인다
- 단, 탐색해야 하는 배열이 이미 정렬이 되어야한다.
- `O(log n)`의 시간 복잡도를 가진다

```python
def binary_search(arr, target=0):
    arr.sort()
    left = 0
    right = len(arr) - 1

    while left <= right:
        mid = (left+right) // 2
        if arr[mid] == target:
            return mid
        
        if arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1

arr = [1, 5, 3, 2, 4]
print(binary_search(arr, 5)) # 4 because [1, 2, 3, 4, 5] -> a[4] = 5
print(binary_search(arr, 0)) # -1
print(binary_search(arr)) # -1
```

# Jump Search
- Linear 탐색과 비슷하지만 block을 정해서 문제의 단위를 줄이는 방식
- 배열은 정렬이 되어있어야 한다
- `O(√n)`의 시간복잡도를 가진다
- 선형탐색 `O(n)`과 이분탐색 `O(log n)`사이다.

```python
import math 

def jump_search(arr, target=0):
    arr.sort()
    n = len(arr)

    # jump를 할 step을 지정해준다
    # jump와 jump사이가 하나의 block이 된다
    step = math.sqrt(n)

    # target이 있는 block을 찾아준다
    prev = 0
    while arr[int(min(step, n)-1)] < target:
        prev = step
        step += math.sqrt(n)
        if prev >= n:
            return -1
    
    # 찾은 block에 Linear 탐색을 시작
    i = prev
    while arr[int(i)] < target:
        i += 1

        # block의 끝이나 배열의 끝에 도달 했으면 
        # target은 없는 것
        if i == min(step, n):
            return -1
    
    if arr[int(i)] == target:
        return int(i)
    
    return -1

arr = [1, 5, 3, 2, 4]
print(jump_search(arr, 5)) # 4 because [1, 2, 3, 4, 5] -> a[4] = 5
print(jump_search(arr, 0)) # -1
print(jump_search(arr)) # -1
```

# Interpolation Search
- 배열이 정렬이 되어있어야 한다
- 균일한 분포를 가질 때 사용가능하다
- 찾아야할 타겟과 가까운 지점에서 탐색을 시작한다
- `O(n)`의 시간복잡도를 갖지만 균일분포된 배열에서의 탐색은 `O(log log n)`이다

```python
def interpolation_search(arr, target=0):
    arr.sort()

    n = len(arr)

    # low, high를 지정한다
    low = 0
    high = n - 1

    # target은 low, high내에 있어야한다
    while low<=high and arr[low]<=target<=arr[high]:
        if low == high:
            if arr[low] == target:
                return low
            return -1
        
        # 균일분포도를 고려해서 탐색위치를 지정한다
        ceil = float(high-low) / (arr[high]-arr[low])
        ceil *= (target-arr[low])
        pos = low + int(ceil)

        if arr[pos] == target:
            return pos
        
        if arr[pos] < target:
            low = pos + 1
        else:
            high = pos -1
    
    return -1

arr = [1, 5, 3, 2, 4]
print(interpolation_search(arr, 5)) # 4 because [1, 2, 3, 4, 5] -> a[4] = 5
print(interpolation_search(arr, 0)) # -1
print(interpolation_search(arr)) # -1
```

# Exponential Search

- Jump 탐색처럼 탐색의 위치를 찾은 후, 그 block을 이분 탐색한다
- 이분탐색을 사용하여 탐색의 크기를 재귀적으로 줄인다
- 이분탐색보다 빠를 때도 있다
    - 찾아야하는 위치가 배열의 앞쪽에 위치한다면 더 빠르게 탐색이 가능하다
    - 탐색의 위치를 선형적으로 찾기 때문이다
- `O(log n)`의 시간복잡도를 가진다

```python
def binary_search(arr, left, right, target):
    while left <= right:
        mid = (left+right) // 2

        if arr[mid] == target:
            return mid

        if arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1


def exponential_search(arr, target=0):
    arr.sort()

    n = len(arr)

    if arr[0] == target:
        return 0
    
    # 탐색할 위치를 찾는다
    i = 1
    while i<n and arr[i]<=target:
        i *= 2
    
    # 탐색의 시작과 끝 지점을 정한다
    left = i // 2
    right = min(i, n-1)

    return binary_search(arr, left, right, target)

arr = [1, 5, 3, 2, 4]
print(exponential_search(arr, 5)) # 4 because [1, 2, 3, 4, 5] -> a[4] = 5
print(exponential_search(arr, 0)) # -1
print(exponential_search(arr)) # -1
```

# Ternary Search
- 분할정복 알고리즘
- 배열은 정렬이 되어있어야 한다
- 이분탐색이 탐색 크기를 반으로 줄이는 것처럼, 삼분탐색은 3등분을 해서 찾는 알고리즘
- `O(log n)`의 시간 복잡도를 가진다
- 이분탐색보다 느리다. 이유는 3등분으로 분할해서 탐색을 하다보니 비교 횟수가 높아지기 때문이다

```python
def ternary_search(arr, left, right, target=0):
    arr.sort()
    if left <= right:
        mid1 = left+(right-left) // 3
        mid2 = mid1+(right-left) // 3

        if arr[mid1] == target:
            return mid1
        
        if arr[mid2] == target:
            return mid2
        
        if arr[mid1] > target:
            return ternary_search(arr, left, mid1-1, target)
        
        if arr[mid2] < target:
            return ternary_search(arr, mid2+1, right, target)
    
    return -1

arr = [1, 5, 3, 2, 4]
print(ternary_search(arr, 0, len(arr)-1, 5)) # 4 because [1, 2, 3, 4, 5] -> a[4] = 5
print(ternary_search(arr, 0, len(arr)-1, 0)) # -1
print(ternary_search(arr, 0, len(arr)-1)) # -1
```