---
layout: post
title:  "[알고리즘] Radix Sort"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/radix-sort/)

라딕스 정렬에 대해 알아보자

- 제일 큰 숫자의 자릿수를 구하고
- 자릿수를 기준으로 정렬을 한다
- 내부에서는 카운팅 정렬이 사용된다

Time complexity
- `O(d*n)` - d는 가장 큰 수의 자릿수, n은 element 개수

Space complexity
- `O(n + 2^d)` - 2^d 인 이유는 각 key를 저장하기위한 비트 수

# Radix sort visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/nu4gDuFabIM"></iframe>
</div>

# Python
```python
def radixSort(arr):
    def helperFunc(arr, expo):
        # implement counting sort
        N = len(arr)

        output = [0] * N
        # initialize counter array as 10
        counter = [0] * 10

        # store frequency in count
        for i in range(N):
            idx = arr[i] / expo
            counter[int(idx % 10)] += 1
        
        # change counter i
        for i in range(1, 10):
            counter[i] += counter[i-1]
        
        # build output array
        i = N - 1
        while i >= 0:
            idx = arr[i] / expo
            output[counter[int(idx % 10)] - 1] = arr[i]
            counter[int(idx % 10)] -= 1
            i -= 1
        
        # copy result to array
        for i in range(N):
            arr[i] = output[i]

    # find the max number to know the number of digits
    max_val = max(arr)

    # Do counting sort for every digit
    # Instead of passing the digit number,
    # exp is 10^i where i is the current digit
    exp = 1
    while max_val / exp > 0:
        helperFunc(arr, exp)
        exp *= 10
```