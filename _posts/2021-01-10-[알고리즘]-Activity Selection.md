---
layout: post
title:  "Activity Selection"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/activity-selection-problem-greedy-algo-1/)

Activity Selection에 대해 알아보자

- 그리디 알고리즘
- N개의 activity의 시작 시간과 끝나는 시간이 있을때, 한 사람이 activity를 수행 할 수 있는 최대 횟수를 구하는 것
- 단, 사람은 같은 시간에 딱 하나의 activity만 수행 할 수 있다.
 
 **알고리즘**
- 주어진 N개의 activity를 끝나는 시간을 기준으로 오름차순 정렬한다
- 제일 첫 번째 activity를 결과 값에 저장한다
- 나머지 activity는:
    - 현재 activity의 시작 시간이 전의 activity의 끝나는 시간과 같거나 크다면, 현재 activity를 결과 값에 저장한다.

# Python
```python
def acitivty_selection(arr: list) -> list:
    '''Returns number of activities in list'''
    # sort the items by finish time
    arr.sort(key=lambda x: x[1])

    # add the first item to the answer
    answer = []
    answer.append(arr.pop(0))
    prev_item = answer[0]

    # compare the current item's start time with
    # the previous item's end time
    for i in range(len(arr)):
        curr_item = arr[i]

        if curr_item[0] >= prev_item[1]:
            answer.append(curr_item)
            prev_item = curr_item
    return answer


# create activity array
# (start time, finish time)
activities = [
    (1, 2),
    (3, 4),
    (0, 6),
    (5, 7),
    (8, 9),
    (5, 9)
]

ans = acitivty_selection(activities)
print(ans)
# [(1, 2), (3, 4), (5, 7), (8, 9)]
```

# Activity Selection을 사용한 문제
- [백준 1931번 회의실 배정](https://www.acmicpc.net/problem/1931)
- [Leetcode 452번  Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/)
- [Leetcode 1326번 Minimum Number of Taps to Open to Water a Garden](https://leetcode.com/problems/minimum-number-of-taps-to-open-to-water-a-garden/)
- [Leetcode 435번 Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)