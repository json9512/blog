---
layout: post
title:  "[알고리즘] Min num of x required for y"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/minimum-number-platforms-required-railwaybus-station/)

- `y` 라는 변수가 `x`라는 변수에 의존할 때, `x`가 필요한 최소 개수를 구하는 알고리즘
- 그리디 알고리즘

예를 들면, 기차가 `y` 이고 `x`가 기차가 정차를 하는 플랫폼이라고 가정하자.

이때 기차가 플랫폼에 도착하는 시간 `n`과 플랫폼에서 기차가 출발하는 시간 `m`인 `n, m` 배열이 주어질 때, 최소한의 플랫폼 개수를 구하는 것이 목적이다. 

```python
# 기차는 y, 플랫폼은 x
# n은 기차들이 x에 들어오는 시간이 담긴 배열
# m은 기차들이 x에서 출발하는 시간이 담긴 배열
n = ["9:00", "9:40", "9:50", "11:00", "15:00", "18:00"]
m = ["9:10", "12:00", "11:20", "11:30", "19:00", "20:00"]

# 특정 시간에 최대 몇 대의 기차 y가 x에 들어와야 하는지를 계산하면
# x가 최소한 몇 개가 필요한지가 나온다
```

#### 방법
1. 주어진 `n`, `m` 배열을 정렬한다
2. 플랫폼 개수를 세는 `plat`을 선언한 뒤 포인터 `i`, `j`를 선언하고 `i<n and j<n` 일때 룹을 돈다
3. `n[i]` 와 `m[j]`을 비교해서 
    - `n[i] <= m[j]`라면 `plat`의 개수를 1 더하고 `i`를 1 더해준다
    - 반대라면 `plat`을 1 빼주고 `j`를 1 더해준다
4. 룹이 종료되면 `plat`이 답이다

# Python
```python
def find_min_platform(arr, dep):
    N = len(arr)

    # 도착 시간 arr와 출발 시간 dep을 정렬한다
    arr.sort()
    dep.sort()

    # counter들을 선언한다
    plat = 1
    i = 1
    j = 0
    min_num_of_plat = 1

    # Loop 을 돈다
    while i < N and j < N:
        # 도착 시간이 출발 시간보다 낮거나 같으면
        # 출발 하는 기차와 도착한 기차가 정차할
        # 플랫폼의 개수가 늘어난다
        if arr[i] <= dep[j]:
            i += 1
            plat += 1
        elif arr[i] > dep[j]:
            # 반대는 줄어든다
            j += 1
            plat -= 1

        # min_num_of_plat의 값을 갱신해준다
        if plat > min_num_of_plat:
            min_num_of_plat = plat

    return min_num_of_plat


if __name__ == "__main__":
    arrival = [900, 940, 950, 1100, 1500, 1800]
    departure = [910, 1200, 1120, 1130, 1900, 2000]

    print(
        "Min number of platform required and any given time is:",
        find_min_platform(arrival, departure)
        )
    # Min number of platform required and any given time is: 3
```

기차와 플랫폼으로 예시를 들었지만 아무거나 가능하다. 렌터카 수, 식당 테이블 수, 최소 가동 돼야 할 기계/프로그램 등등

시간 복잡도는:
- `O(n)` 만큼 loop이 돌고, `O(n log n)`으로 Sort을 한다
- 전체 시간 복잡도는 `O(n log n)`

Space complexity는 `O(1)`이다

**같은 문제를 `O(n)` 시간 복잡도와 `O(n)` 공간 복잡도로 풀 수 있다**

#### 방법 
1. 출발과 도착시간의 일시가 같다면, `O(n)`으로 계산이 가능하다
2. 각 분 마다 필요한 플랫폼의 개수를 계속 세면 된다

```python
def find_min_platform(arr, dep):
    N = len(arr)

    arr.sort()
    dep.sort()

    # 각 분마다 platform의 개수를 세는 배열
    platforms = [0] * 2361 # 2361은 시간:분 최대치다
    required_plat = 1

    for i in range(N):
        # 기차가 들어오면 플랫폼이 필요하니 1을 더 하자
        platforms[arr[i]] += 1

        # 기차가 나가면 플랫폼에서 제외하자
        platforms[dep[i]+1] -= 1
    
    # 하루에 최대 플랫폼 개수가 몇 인지 세자
    for i in range(1, len(platforms)):

        # 누적 합계를 계속 세자
        platforms[i] += platforms[i-1]
        required_plat = max(required_plat, platforms[i])
    
    return required_plat

if __name__ == "__main__":
    arrival = [900, 940, 950, 1100, 1500, 1800]
    departure = [910, 1200, 1120, 1130, 1900, 2000]

    print(
        "Min number of platform required and any given time is:",
        find_min_platform(arrival, departure)
        )
    # Min number of platform required and any given time is: 3
```
